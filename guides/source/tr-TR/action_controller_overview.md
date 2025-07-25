**RESMİ KILAVUZA BU ADRES ÜZERİNDEN ULAŞABİLİRSİNİZ: <https://guides.rubyonrails.org>**

--------------------------------------------------------------------------------

Action Controller
=================

Bu kılavuzda, Controller'ın nasıl çalıştığını ve uygulamanızdaki istek döngüsüne nasıl uyum sağladığını öğreneceksiniz.

Bu kılavuzu okuduktan sonra şunları yapmayı öğreneceksiniz:

* Bir controller aracılığıyla isteğin akışını takip etmeyi
* Controller'ınıza aktarılan parametrelere erişmeyi
* Strong Parameters ve izin değerleri kullanmayı
* Verileri çerez, oturum ve flash'te depolamayı
* İstek işleme sırasında kodu yürütmek için action callback'leriyle çalışmayı
* İstek ve Yanıt Nesnelerini kullanmayı

Giriş
-----

Action Controller, Model View Controller ([MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)) desenindeki C'dir. [Router](../routing/); bir controller'ı gelen bir istekle eşleştirdikten sonra, controller isteği işlemekten ve uygun çıktıyı üretmekten sorumludur.

Çoğu geleneksel [RESTful] (https://en.wikipedia.org/wiki/Representational_state_transfer) uygulamasında, controller isteği alır, bir modelden veri getirir veya kaydeder ve HTML çıktısı oluşturmak için bir view kullanır.

Bir controller'ın model ve view arasında yer aldığını düşünebilirsiniz. Controller, model verilerini view için kullanılabilir hale getirir, böylece view bu verileri kullanıcıya gösterebilir. Controller ayrıca view'den kullanıcı girdisi alır ve model verilerini buna göre kaydeder veya günceller.

Controller Oluşturma
---------------------

Bir controller, `ApplicationController`'dan miras alan ve diğer sınıflar gibi metotlara sahip bir Ruby sınıfıdır. Gelen bir istek router tarafından bir controller ile eşleştirildiğinde, Rails o controller sınıfının bir örneğini oluşturur ve action ile aynı isme sahip metodu çağırır.

```ruby
class ClientsController < ApplicationController
  def new
  end
end
```

Yukarıdaki `ClientsController` göz önüne alındığında, bir kullanıcı yeni bir client eklemek için uygulamanızda `/clients/new` adresine giderse, Rails `ClientsController`'ın bir örneğini oluşturacak ve `new` metodunu çağıracaktır. Eğer `new` metodu boşsa, Rails varsayılan olarak otomatik olarak `new.html.erb` view'ini render edecektir.

NOT: Buradaki `new` metodu, `ClientsController`'ın bir örneği üzerinde çağrılan bir instance metodudur. Bu, `new` sınıf metodu (yani `ClientsController.new`) ile karıştırılmamalıdır.

`new` metodunda, controller tipik olarak `Client` modelinin bir örneğini oluşturur ve bunu view'da `@client` adlı bir instance variable olarak kullanılabilir hale getirir:

```ruby
def new
  @client = Client.new
end
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    Tüm controller'lar <code>ApplicationController</code>'dan miras alır, o da <a href="https://api.rubyonrails.org/classes/ActionController/Base.html">ActionController::Base</a>'den miras alır. <a href="https://guides.rubyonrails.org/api_app.html">Yalnızca API</a> uygulamaları için <code>ApplicationController</code> <a href="https://edgeapi.rubyonrails.org/classes/ActionController/API.html">ActionController::API</a>'dan miras alır.
  </div>
</div>

### Controller İsimlendirme Kuralı

Rails, controller adındaki kaynağın çoğul yapılmasını tercih eder. Örneğin, `ClientController` yerine `ClientsController` ve `SiteAdminController` veya `SitesAdminsController` yerine `SiteAdminsController` tercih edilir. Ancak, çoğul isimler kesinlikle gerekli değildir (örneğin `ApplicationController`).

Bu isimlendirme kuralını takip etmek, [varsayılan route generator'larını](../routing/#crud-eylemleri-ve-action-ları) (örneğin `resources`) [:controller](../routing/#kullanılacak-bir-controlller-i-belirtme) gibi seçeneklerle nitelendirmeye gerek kalmadan kullanmanıza olanak tanır. Bu kural ayrıca isimlendirilmiş route helper'larını uygulama boyunca tutarlı hale getirir.

Controller isimlendirme kuralı modellerden farklıdır. Controller isimleri için çoğul isimler tercih edilirken, [model isimleri](../active_record_basics/#isimlendirme-kurallari) için tekil form tercih edilir (örneğin `Accounts` yerine `Account`).

Controller action'ları `public` olmalıdır, çünkü yalnızca `public` metotlar action olarak çağrılabilir. Action olmaya yönelik _olmayan_ yardımcı metotların görünürlüğünü (`private` veya `protected` ile) düşürmek de iyi bir yöntemdir.

<div class="guide-alert guide-alert-danger">
  <div class="guide-alert-icon">❗</div>
  <div class="guide-alert-content">
    Bazı metod isimleri Action Controller tarafından rezerve edilmiştir. Bunları yanlışlıkla yeniden tanımlamak <code>SystemStackError</code> ile sonuçlanabilir. Controller'larınızı yalnızca RESTful <a href="../routing/#kaynak-routing-rails-varsayilan">Resource Routing</a> action'larıyla sınırlandırırsanız bu konuda endişelenmenize gerek olmaz.
  </div>
</div>

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    Rezerve edilmiş bir metodu action adı olarak kullanmanız gerekiyorsa geçici bir çözüm olarak şunu yapabilirsiniz: Rezerve edilmiş metot adını, rezerve edilmemiş action metodunuza eşlemek için özel bir route kullanabilirsiniz.
  </div>
</div>

Parametreler
------------

Gelen istek tarafından gönderilen veriler, controller'ınızda [`params`](https://api.rubyonrails.org/classes/ActionController/StrongParameters.html#method-i-params) hash'inde mevcuttur. İki tür parametre verisi vardır:

- URL'in bir parçası olarak gönderilen query string parametreleri (örneğin, `http://example.com/accounts?filter=free` adresindeki `?`'den sonra).
- Bir HTML formundan gönderilen POST parametreleri.

Rails query string parametreleri ile POST parametreleri arasında ayrım yapmaz; her ikisi de controller'ınızdaki `params` hash'inde mevcuttur. Örneğin:

```ruby
class ClientsController < ApplicationController
  # Bu action "/clients?status=activated" URL'inden HTTP GET isteği ile
  # query string parametrelerini alır
  def index
    if params[:status] == "activated"
      @clients = Client.activated
    else
      @clients = Client.inactivated
    end
  end

  # Bu action "/clients" URL'ine POST isteği ile request body'sindeki 
  # form verilerinden parametreleri alır.
  def create
    @client = Client.new(params[:client])
    if @client.save
      redirect_to @client
    else
      render "new"
    end
  end
end
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    <code>params</code> hash'i sıradan bir Ruby Hash değildir; bunun yerine bir <a href="https://api.rubyonrails.org/classes/ActionController/Parameters.html">ActionController::Parameters</a> nesnesidir. Hash'ten miras almaz, ancak çoğunlukla Hash gibi davranır. <code>params</code>'ı filtrelemek için metotlar sağlar ve bir Hash'ten farklı olarak <code>:foo</code> ve <code>"foo"</code> anahtarları aynı kabul edilir.
  </div>
</div>

### Hash ve Array Parametreleri

`params` hash'i tek boyutlu anahtarlar ve değerlerle sınırlı değildir. İç içe array'ler ve hash'ler içerebilir. Bir değer array'i göndermek için, anahtar adının sonuna boş bir çift köşeli parantez `[]` ekleyin:

```
GET /users?ids[]=1&ids[]=2&ids[]=3
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
     <code>[</code> ve <code>]</code> karakterleri URL'lerde izin verilmediği için bu örnekteki gerçek URL <code>/users?ids%5b%5d=1&ids%5b%5d=2&ids%5b%5d=3</code> olarak kodlanacaktır. Çoğu zaman bu konuda endişelenmenize gerek yoktur çünkü tarayıcı bunu sizin için kodlayacak ve Rails otomatik olarak decode edecektir, ancak bu istekleri sunucuya manuel olarak göndermeniz gerekirse bunu aklınızda bulundurmalısınız.
  </div>
</div>

`params[:ids]`'in değeri `["1", "2", "3"]` array'i olacaktır. Parametre değerlerinin her zaman string olduğuna dikkat edin. Rails, türü tahmin etmeye veya cast etmeye çalışmaz.

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    <code>params</code>'taki <code>[nil]</code> veya <code>[nil, nil, ...]</code> gibi değerler güvenlik nedenleriyle varsayılan olarak <code>[]</code> ile değiştirilir. Daha fazla bilgi için <a href="../security/#guvenli-olmayan-sorgu-olusturma">Güvenlik Rehberi</a>'ne bakın.
  </div>
</div>

Bir hash göndermek için, anahtar adını köşeli parantezlerin içine dahil edersiniz:

```html
<form accept-charset="UTF-8" action="/users" method="post">
  <input type="text" name="user[name]" value="Acme" />
  <input type="text" name="user[phone]" value="12345" />
  <input type="text" name="user[address][postcode]" value="12345" />
  <input type="text" name="user[address][city]" value="Carrot City" />
</form>
```

Bu form gönderildiğinde, `params[:user]`'ın değeri `{ "name" => "Acme", "phone" => "12345", "address" => { "postcode" => "12345", "city" => "Carrot City" } }` olacaktır. `params[:user][:address]`'teki iç içe hash'e dikkat edin.

`params` nesnesi bir Hash gibi davranır, ancak anahtar olarak sembolleri ve string'leri birbirinin yerine kullanmanıza izin verir.

### Bileşik Anahtar Parametreleri

[Bileşik anahtar (composite key) parametreleri](../active_record_composite_primary_keys/) bir ayırıcı (örneğin, alt çizgi) ile ayrılmış bir parametrede birden fazla değer içerir. Bu nedenle, Active Record'a geçirebilmek için her değeri çıkarmanız gerekir. Bunu yapmak için `extract_value` metodunu kullanabilirsiniz.

Örneğin, aşağıdaki controller göz önüne alındığında:

```ruby
class BooksController < ApplicationController
  def show
    # URL parametrelerinden composite ID değerini çıkar.
    id = params.extract_value(:id)
    @book = Book.find(id)
  end
end
```

Ve bu route:

```ruby
get "/books/:id", to: "books#show"
```

Bir kullanıcı `/books/4_2` URL'ine istek gönderdiğinde controller, bileşik anahtar değeri `["4", "2"]`'yi çıkaracak ve `Book.find`'a geçecektir. `extract_value` metodu herhangi bir sınırlandırılmış parametreden array'leri çıkarmak için kullanılabilir.

### JSON Parametreleri

Eğer uygulamanız bir API sunarsa, muhtemelen parametreleri JSON formatında kabul edeceksiniz. İsteğinizin `content-type` header'ı `application/json` olarak ayarlanmışsa, Rails parametrelerinizi otomatik olarak `params` hash'ine yükleyecektir ve normalde yaptığınız gibi bunlara erişebilirsiniz.

Örneğin, bu JSON içeriğini gönderiyorsanız:

```json
{ "user": { "name": "acme", "address": "123 Carrot Street" } }
```

Controller'ınız şunu alacaktır:

```ruby
{ "user" => { "name" => "acme", "address" => "123 Carrot Street" } }
```

#### Wrap Parametre Yapılandırması

JSON parametrelerine otomatik olarak controller adını ekleyen [Wrap Parametre](https://api.rubyonrails.org/classes/ActionController/ParamsWrapper/Options/ClassMethods.html#method-i-wrap_parameters) kullanabilirsiniz. Örneğin, root `:user` anahtar öneki olmadan aşağıdaki JSON'u gönderebilirsiniz:

```json
{ "name": "acme", "address": "123 Carrot Street" }
```

Bu veriyi `UsersController`'a gönderdiğinizi varsayarak, JSON şu şekilde `:user` anahtarı içinde sarmalanacaktır:

```ruby
{ name: "acme", address: "123 Carrot Street", user: { name: "acme", address: "123 Carrot Street" } }
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
  Wrap Parametre, controller adıyla aynı olan bir anahtar içinde hash'e parametrelerin bir klonunu ekler. Sonuç olarak, hem parametrelerin orijinal versiyonu hem de "sarmalanmış" versiyonu params hash'inde bulunacaktır.
  </div>
</div>

Bu özellik, controller'ınızın adına dayalı olarak seçilen bir anahtarla parametreleri klonlar ve sarmalar. Varsayılan olarak `true` olarak yapılandırılmıştır. Parametreleri sarmalamasını istemiyorsanız bunu `false` olarak [yapılandırabilirsiniz](../configuring/config-action-controller-wrap-parameters-by-default).

```ruby
config.action_controller.wrap_parameters_by_default = false
```

Ayrıca anahtarın adını veya sarmalamak istediğiniz belirli parametreleri özelleştirebilirsiniz, daha fazla bilgi için [API dokümantasyonu](https://api.rubyonrails.org/classes/ActionController/ParamsWrapper.html)'na bakın.

### Routing Parametreleri

`routes.rb` dosyasındaki route bildirimin bir parçası olarak belirtilen parametreler de `params` hash'inde kullanılabilir hale getirilir. Örneğin, bir client için `:status` parametresini yakalayan bir route ekleyebiliriz:

```ruby
get "/clients/:status", to: "clients#index", foo: "bar"
```

Bir kullanıcı `/clients/active` URL'ine gittiğinde, `params[:status]` "active" olarak ayarlanacaktır. Bu route kullanıldığında, `params[:foo]` da sanki query string'de geçirilmiş gibi "bar" olarak ayarlanacaktır.

Route bildirimi tarafından tanımlanan `:id` gibi diğer parametreler de kullanılabilir olacaktır.

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    Yukarıdaki örnekte, controller'ınız ayrıca <code>params[:action]</code>'ı "index" ve <code>params[:controller]</code>'ı "clients" olarak alacaktır. <code>params</code> hash'i her zaman <code>:controller</code> ve <code>:action</code> anahtarlarını içerecektir, ancak bu değerlere erişmek için <code><a href="https://api.rubyonrails.org/classes/ActionController/Metal.html#method-i-controller_name">controller_name</a></code> ve <code><a href="https://api.rubyonrails.org/classes/AbstractController/Base.html#method-i-action_name">action_name</a></code> metotlarını kullanmanız önerilir.
  </div>
</div>    

### `default_url_options` Metodu

Controller'ınızda `default_url_options` adlı bir metod tanımlayarak [`url_for`](https://api.rubyonrails.org/classes/ActionView/RoutingUrlFor.html#method-i-url_for) için global varsayılan parametreler ayarlayabilirsiniz. Örneğin:

```ruby
class ApplicationController < ActionController::Base
  def default_url_options
    { locale: I18n.locale }
  end
end
```

Belirtilen varsayılanlar URL'ler oluşturulurken başlangıç noktası olarak kullanılacaktır. `url_for`'a veya `posts_path` gibi herhangi bir path helper'a geçirilen seçenekler tarafından geçersiz kılınabilirler. Örneğin, `locale: I18n.locale` ayarlayarak, Rails otomatik olarak her URL'e locale'i ekleyecektir:

```ruby
posts_path # => "/posts?locale=en"
```

Gerekirse bu varsayılanı yine de geçersiz kılabilirsiniz:

```ruby
posts_path(locale: :fr) # => "/posts?locale=fr"
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    Perde arkasında, <code>posts_path</code> uygun parametrelerle <code>url_for</code> çağırmanın kısaltmasıdır.
  </div>
</div>

Yukarıdaki örnekte olduğu gibi `ApplicationController`'da `default_url_options` tanımlarsanız, bu varsayılanlar tüm URL oluşturma işlemleri için kullanılacaktır. Metod ayrıca belirli bir controller'da da tanımlanabilir, bu durumda yalnızca o controller için oluşturulan URL'lere uygulanır.

Belirli bir istekte, metod gerçekte oluşturulan her URL için çağrılmaz. Performans nedenleriyle döndürülen hash istek başına önbelleğe alınır.

Strong Parametreler
------------------

Action Controller [Strong Parametreleri](https://api.rubyonrails.org/classes/ActionController/StrongParameters.html) ile, parametreler Active Model toplu atamalarda açıkça izin verilmeden kullanılamaz. Bu, hangi niteliklere toplu güncelleme için izin vereceğinize karar vermeniz ve bunları denetleyicide belirtmeniz gerektiği anlamına gelir. Bu, kullanıcıların hassas model niteliklerini yanlışlıkla güncellemesini önlemek için uygulanan bir güvenlik önlemidir.

Buna ek olarak, parametreler gerekli olarak işaretlenebilir ve eğer tüm gerekli parametreler iletilmezse, istek sonucunda 400 Bad Request (Geçersiz İstek) hatası döndürülür.

```ruby
class PeopleController < ActionController::Base
  # Bu kod ActiveModel::ForbiddenAttributesError hatasını verecektir,
  # çünkü açıkça izin verilmeden toplu atama yapılıyor.
  def create
    Person.create(params[:person])
  end

  # Bu kod çalışacaktır çünkü `expect` çağrısıyla 
  # toplu atama izni veren `person_params` yardımcı metodunu kullanıyoruz.
  def update
    person = Person.find(params[:id])
    person.update!(person_params)
    redirect_to person
  end

  private
    # İzin verilen parametreleri kapsüllemek için özel bir yöntem kullanmak
    # iyi bir yöntemdir. Aynı listeyi hem create hem de update
    # işlemleri için kullanabilirsiniz.
    def person_params
      params.expect(person: [:name, :age])
    end
end
```

### Değerleri İzinli Hale Getirme

#### `expect`

[`expect`](https://api.rubyonrails.org/classes/ActionController/Parameters.html#method-i-expect) metodu, parametreleri zorunlu kılmak ve izin vermek için özlü ve güvenli bir yöntem sağlar.

```ruby
id = params.expect(:id)
```

Yukarıdaki `expect` her zaman bir skaler değer döndürür — yani bir dizi (array) veya sözlük (hash) döndürmez. Başka bir örnek olarak, form parametrelerinde, kök anahtarın mevcut olduğundan emin olmak ve ilgili özniteliklerin izinli olduğundan emin olmak için `expect` kullanabilirsiniz.

```ruby
user_params = params.expect(user: [:username, :password])
user_params.has_key?(:username) # => true
```

Yukarıdaki örnekte, `:user` anahtarı belirtilen anahtarlarla iç içe geçmiş bir hash değilse, `expect` bir hata verir ve 400 Geçersiz İstek yanıtı döner.

Tüm bir parametre hash'ini zorunlu kılmak ve izin vermek için [`expect`](https://api.rubyonrails.org/classes/ActionController/Parameters.html#method-i-expect) şu şekilde kullanılabilir:

```ruby
params.expect(log_entry: {})
```

Bu, `:log_entry` parametreler hash'i ve içindeki herhangi bir alt hash'i izinli olarak işaretler ve izinli skalerleri kontrol etmez, her şey kabul edilir.

<div class="guide-alert guide-alert-danger">
  <div class="guide-alert-icon">❗</div>
  <div class="guide-alert-content">
    Boş bir hash ile <code>expect</code> çağrılırken son derece dikkatli olunmalıdır, çünkü bu tüm mevcut ve gelecekteki model özniteliklerinin toplu olarak atanmasına izin verir.
  </div>
</div>

#### `permit`

[`permit`](https://api.rubyonrails.org/classes/ActionController/Parameters.html#method-i-permit) çağrıldığında `params` içindeki belirtilen anahtara (`:id` veya aşağıdaki örnekteki `:admin`) toplu atama için izin verilir (örneğin `create` veya `update` ile):

```irb
params = ActionController::Parameters.new(id: 1, admin: "true")
=> #<ActionController::Parameters {"id"=>1, "admin"=>"true"} permitted: false>
params.permit(:id)
=> #<ActionController::Parameters {"id"=>1} permitted: true>
params.permit(:id, :admin)
=> #<ActionController::Parameters {"id"=>1, "admin"=>"true"} permitted: true>
```

İzin verilen `:id` anahtarı için, değeri aşağıdaki izinli skaler değer türlerinden biri olmalıdır: `String`, `Symbol`, `NilClass`, `Numeric`, `TrueClass`, `FalseClass`, `Date`, `Time`, `DateTime`, `StringIO`, `IO`, `ActionDispatch::Http::UploadedFile` ve `Rack::Test::UploadedFile`.

Eğer ilgili anahtar için `permit` çağrısı yapılmadıysa, o anahtar filtrelenir. Array'ler, hash'ler veya diğer nesneler varsayılan olarak dahil edilmez.

`params` içerisinde izinli skaler değerlerden oluşan bir array'i dahil etmek için, anahtarı boş bir array'e eşleyebilirsiniz:

```irb
params = ActionController::Parameters.new(tags: ["rails", "parameters"])
=> #<ActionController::Parameters {"tags"=>["rails", "parameters"]} permitted: false>
params.permit(tags: [])
=> #<ActionController::Parameters {"tags"=>["rails", "parameters"]} permitted: true>
```

Hash değerlerini dahil etmek için, anahtarı boş bir hash'e eşleyebilirsiniz:

```irb
params = ActionController::Parameters.new(options: { darkmode: true })
=> #<ActionController::Parameters {"options"=>{"darkmode"=>true}} permitted: false>
params.permit(options: {})
=> #<ActionController::Parameters {"options"=>#<ActionController::Parameters {"darkmode"=>true} permitted: true>} permitted: true>
```

Yukarıdaki `permit` çağrısı, `options` içindeki değerlerin izinli skalerler olmasını garanti eder ve diğer tüm verileri filtreler.

<div class="guide-alert guide-alert-danger">
  <div class="guide-alert-icon">❗</div>
  <div class="guide-alert-content">
  Boş bir hash ile <code>permit</code> kullanmak bazen tüm geçerli anahtarları veya hash'in iç yapısını belirtmek mümkün ya da pratik olmadığında kullanışlıdır. Ancak, boş bir hash ile yapılan bu <code>permit</code> çağrısı, rastgele verilerin kabul edilmesinin önünü açar.
  </div>
</div>

#### `permit!`

Ayrıca, [`permit!`](https://api.rubyonrails.org/classes/ActionController/Parameters.html#method-i-permit-21) (ünlem işaretiyle) vardır ve bu, bir parametre hash'inin tamamına değerleri kontrol etmeden izin verir.

```irb
params = ActionController::Parameters.new(id: 1, admin: "true")
=> #<ActionController::Parameters {"id"=>1, "admin"=>"true"} permitted: false>
params.permit!
=> #<ActionController::Parameters {"id"=>1, "admin"=>"true"} permitted: true>
```

<div class="guide-alert guide-alert-danger">
  <div class="guide-alert-icon">❗</div>
  <div class="guide-alert-content">
    <code>permit!</code> kullanılırken son derece dikkatli olunmalıdır çünkü bu, mevcut ve <i>gelecekteki</i> tüm model özniteliklerinin toplu olarak atanmasına izin verir.
  </div>
</div>

### İç İçe Parametreler

Ayrıca `expect` (veya `permit`) özelliğini iç içe geçmiş parametreler üzerinde de kullanabilirsiniz:

```ruby
# Örnek beklenen parametreler:
params = ActionController::Parameters.new(
  name: "Martin",
  emails: ["me@example.com"],
  friends: [
    { name: "André", family: { name: "RubyGems" }, hobbies: ["keyboards", "card games"] },
    { name: "Kewe", family: { name: "Baroness" }, hobbies: ["video games"] },
  ]
)

# aşağıdaki expect çağrısı parametrelerin izinli olmasını sağlar
name, emails, friends = params.expect(
  :name,                 # izinli skaler (tekil) değer
  emails: [],            # izinli skaler değerlerden oluşan array
  friends: [[            # Parametre hash'lerinden oluşan izinli array
    :name,               # izinli skaler değer
    family: [:name],     # family: { name: "izinli skaler değer" }
    hobbies: []          # izinli skaler değerlerden oluşan array
  ]]
)
```

Bu ifade, `name`, `emails` ve `friends` özniteliklerini izinli kılar ve her birini döndürür. `emails`'in izinli skaler değerlerden oluşan bir array olması beklenir ve `friends`'in belirli özniteliklere sahip kaynaklardan oluşan bir array olması beklenir (array'i açıkça zorunlu kılmak için yeni çift array sözdizimine dikkat edin). Bu öznitelikler `name` özniteliğine (herhangi bir izinli skaler değer kabul edilir), izinli skaler değerlerden oluşan bir array olan `hobbies` özniteliğine ve yalnızca `name` anahtarına sahip bir hash olarak kısıtlanmış `family` özniteliğine sahip olmalıdır.

### Örnekler

Farklı kullanım senaryoları için `permit` kullanımına dair bazı örnekler aşağıda verilmiştir.

**Örnek 1**: `new` aksiyonunuzda izinli öznitelikleri kullanmak isteyebilirsiniz. Bu durumda kök anahtar [`require`](https://api.rubyonrails.org/classes/ActionController/Parameters.html#method-i-require) ile çağrılamaz, çünkü genellikle `new` çağrıldığında mevcut değildir:

```ruby
# `fetch` kullanarak varsayılan değer belirtebilir ve
# Strong Parametre API'yı buradan kullanabilirsiniz.
params.fetch(:blog, {}).permit(:title, :author)
```

**Örnek 2**: Model sınıf metodu `accepts_nested_attributes_for`, ilişkili kayıtları güncellemenize ve silmenize olanak tanır. Bu, `id` ve `_destroy` parametrelerine dayanır:

```ruby
# :id ve :_destroy izin verilir
params.expect(author: [ :name, books_attributes: [[ :title, :id, :_destroy ]] ])
```

**Örnek 3**: Tamsayı anahtarlara sahip hash’ler farklı şekilde ele alınır ve bu öznitelikleri doğrudan çocuklarmış gibi tanımlayabilirsiniz. Bu tür parametreleri, `accepts_nested_attributes_for` ile birlikte `has_many` ilişkisini kullandığınızda alırsınız:

```ruby
# Aşağıdaki verilerin izinli olmasını sağlamak için:
# {"book" => {"title" => "Some Book",
#             "chapters_attributes" => { "1" => {"title" => "First Chapter"},
#                                        "2" => {"title" => "Second Chapter"}}}}

params.expect(book: [ :title, chapters_attributes: [[ :title ]] ])
```

**Örnek 4**: Ürün adını temsil eden parametrelere ve bu ürünle ilişkili rastgele verilere sahip bir senaryo hayal edin. Burada ürün adı özniteliğine ve tüm veri hash’ine izin vermek istersiniz:

```ruby
def product_params
  params.expect(product: [ :name, data: {} ])
end
```

Çerezler
--------

Çerez kavramı sadece Rails’e özgü değildir. [Çerez (cookie)](https://en.wikipedia.org/wiki/HTTP_cookie) (HTTP çerezi veya web çerezi olarak da bilinir), sunucudan gelen ve kullanıcının tarayıcısında saklanan küçük bir veri parçasıdır. Tarayıcı bu çerezleri saklayabilir, yenilerini oluşturabilir, mevcut olanları değiştirebilir ve sonraki isteklerde sunucuya geri gönderebilir. Çerezler web istekleri arasında verilerin kalıcılığını sağlar ve böylece web uygulamaları kullanıcı tercihlerinin hatırlanmasını mümkün kılar.

Rails, çerezlere kolay erişim için [`cookies`](https://api.rubyonrails.org/classes/ActionController/Cookies.html#method-i-cookies) metodunu sunar. Bu metot bir hash gibi çalışır:

```ruby
class CommentsController < ApplicationController
  def new
    # Yorum yapan kişinin adı çerezde saklanmışsa, otomatik olarak doldur.
    @comment = Comment.new(author: cookies[:commenter_name])
  end

  def create
    @comment = Comment.new(comment_params)
    if @comment.save
      if params[:remember_name]
        # Yorum yapan kişinin adını bir çereze kaydet.
        cookies[:commenter_name] = @comment.author
      else
        # Varsa, yorumcu adı için olan çerezi sil.
        cookies.delete(:commenter_name)
      end
      redirect_to @comment.article
    else
      render action: "new"
    end
  end
end
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
  Bir çerezi silmek için <code>cookies.delete(:anahtar)</code> kullanmalısınız. <code>key</code> anahtar değerini <code>nil</code> yapmak çerezi silmez.
  </div>
</div>

Skaler bir değer geçildiğinde, kullanıcı tarayıcısını kapattığında çerez silinecektir. Çerezin belirli bir zamanda sona ermesini istiyorsanız, çerezi ayarlarken `:expires` seçeneğiyle bir hash iletin. Örneğin, 1 saat içinde sona erecek bir çerez ayarlamak için:

```ruby
cookies[:login] = { value: "XJ-122", expires: 1.hour }
```

Süresi hiç dolmayan bir çerez oluşturmak isterseniz, permanent (kalıcı) çerez alanını kullanabilirsiniz. Bu çerezler 20 yıl sonra sona erecek şekilde ayarlanır:

```ruby
cookies.permanent[:locale] = "fr"
```

### Şifrelenmiş ve İmzalanmış Çerezler

Çerezler istemci tarayıcısında saklandığından, kötü niyetli müdahalelere açık olabilir ve hassas verileri saklamak için güvenli kabul edilmez. Rails, hassas verilerin saklanması için imzalanmış ve şifrelenmiş çerez kapları sunar. İmzalanmış çerez veriye kriptografik bir imza ekleyerek içeriğin bütünlüğünü korur. Şifrelenmiş çerez veriyi hem imzalar hem de şifreler, böylece kullanıcı veriyi okuyamaz.

Daha fazla ayrıntı için [API dokümantasyonuna](https://api.rubyonrails.org/classes/ActionDispatch/Cookies.html) bakınız.

```ruby
class CookiesController < ApplicationController
  def set_cookie
    cookies.signed[:user_id] = current_user.id
    cookies.encrypted[:expiration_date] = Date.tomorrow
    redirect_to action: "read_cookie"
  end

  def read_cookie
    cookies.encrypted[:expiration_date] # => "2024-03-20"
  end
end
```

Bu özel çerezler, verileri string olarak serileştirip okunduğunda Ruby nesnelerine dönüştürmek için bir serializer kullanır. Hangi serializer’ın kullanılacağını [`config.action_dispatch.cookies_serializer`](../configuring/#config-action-dispatch-cookies-serializer)  üzerinden belirleyebilirsiniz. Yeni uygulamalarda varsayılan serializer `:json`'dur.

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
  Ancak unutmayın: JSON, <code>Date</code>, <code>Time</code>, <code>Symbol</code> gibi Ruby nesnelerinin serileştirilmesi konusunda sınırlı destek sunar. Bu tip veriler <code>string</code> olarak serileştirilir ve çözümlenir. 
  </div>
</div>

Daha karmaşık nesneler saklamak istiyorsanız, bunları manuel olarak dönüştürmeniz gerekebilir. Rails çerez oturum deposu kullanıyorsa, yukarıda belirtilen davranışlar `session` ve `flash` hash’leri için de geçerlidir.

Oturum
------

Çerezler istemci tarafında saklanırken, oturum verileri sunucu tarafında (bellekte, veritabanında veya önbellekte) saklanır ve genellikle süresi geçicidir; kullanıcının oturumuna bağlıdır (örneğin, tarayıcıyı kapatana kadar). Oturumun örnek kullanım senaryosu, kullanıcı kimlik doğrulaması gibi hassas verilerin saklanmasıdır.

Bir Rails uygulamasında, oturum hem denetleyicide (controller) hem de görünümde (view) kullanılabilir.

### Oturum ile Çalışmak

Controller'da oturuma erişmek için `session` örnek yöntemini kullanabilirsiniz. Oturum değerleri bir hash gibi anahtar/değer çiftleri olarak saklanır:

```ruby
class ApplicationController < ActionController::Base
  private
    # Oturumda `:current_user_id` anahtarını arar ve bunu kullanarak
    # mevcut `User`'ı bulur. Bu, bir Rails uygulamasında kullanıcı girişi işlemlerini
    # yönetmek için yaygın bir yöntemdir; oturum açma işlemi oturum değerini ayarlar,
    # oturumu kapatma ise bunu kaldırır.
    def current_user
      @current_user ||= User.find_by(id: session[:current_user_id]) if session[:current_user_id]
    end
end
```

Oturuma bir şey kaydetmek için, bir değeri bir anahtara atayabilirsiniz, tıpkı bir hash’e değer ekler gibi. Bir kullanıcı kimlik doğrulamadan geçtikten sonra, `id`’si oturuma kaydedilir ve sonraki isteklerde kullanılabilir:

```ruby
class SessionsController < ApplicationController
  def create
    if user = User.authenticate_by(email: params[:email], password: params[:password])
      # Kullanıcı kimliği oturuma kaydedilir, böylece
      # sonraki isteklerde kullanılabilir.
      session[:current_user_id] = user.id
      redirect_to root_url
    end
  end
end
```

Oturumdan bir şeyi kaldırmak için, anahtar/değer çiftini silin. `current_user_id` anahtarını oturumdan silmek, kullanıcıyı oturumdan çıkarmanın tipik bir yoludur:

```ruby
class SessionsController < ApplicationController
  def destroy
    session.delete(:current_user_id)
    # Mevcut kullanıcıyı da temizle.
    @current_user = nil
    redirect_to root_url, status: :see_other
  end
end
```

Tüm oturumu [`reset_session`](https://api.rubyonrails.org/classes/ActionController/Metal.html#method-i-reset_session) ile sıfırlamak mümkündür. Oturum sabitleme (session fixation) saldırılarını önlemek için oturum açtıktan sonra `reset_session` kullanmanız önerilir. Ayrıntılar için [Güvenlik Rehberi](https://edgeguides.rubyonrails.org/security.html#session-fixation-countermeasures)’ne bakın.

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
  Oturumlar tembel (lazy) şekilde yüklenir. Action kodunuzda oturuma erişmezseniz, yüklenmezler. Bu nedenle oturumları devre dışı bırakmanız gerekmez – onlara erişmemek yeterlidir.
  </div>
</div>

### Flash

[flash](https://api.rubyonrails.org/classes/ActionDispatch/Flash.html), controller işlemleri arasında geçici veri aktarımı için bir yol sağlar. Flash’a yerleştirdiğiniz herhangi bir şey bir sonraki işleme kadar kullanılabilir olacak ve ardından silinecektir. Flash genellikle bir işlemin sonucunu kullanıcıya göstermek için yönlendirme öncesinde mesaj (örneğin `notice` ve `alert`) ayarlamada kullanılır.

Flash, [`flash`][] yöntemi ile erişilir. Session’a benzer şekilde, flash değerleri anahtar/değer çiftleri olarak saklanır.

Örneğin, bir kullanıcının oturumdan çıkış işlemini yöneten denetleyicide, bir flash mesajı ayarlanabilir ve bu mesaj bir sonraki istekte kullanıcıya gösterilebilir:

```ruby
class SessionsController < ApplicationController
  def destroy
    session.delete(:current_user_id)
    flash[:notice] = "You have successfully logged out."
    redirect_to root_url, status: :see_other
  end
end
```

Uygulamanızda kullanıcı etkileşimli bir işlem yaptıktan sonra mesaj göstermek, işlemin başarılı olduğunu (ya da hatalar olduğunu) bildirmek açısından iyi bir geri bildirim yoludur.

`:notice`’e ek olarak, `:alert` da kullanılabilir. Genellikle bu mesaj türleri CSS ile farklı renklerle stilize edilir (örneğin notice için yeşil, alert için turuncu/kırmızı).

Flash mesajı doğrudan `redirect_to` yöntemi içinde bir parametre olarak da atanabilir:

```ruby
redirect_to root_url, notice: "You have successfully logged out."
redirect_to root_url, alert: "There was an issue."
```

Sadece `notice` ve `alert` ile sınırlı değilsiniz.
Flash’te (session gibi) herhangi bir anahtar belirleyerek, `:flash` argümanı ile atama yapılabilir. Örneğin, `:just_signed_up` ataması:

```ruby
redirect_to root_url, flash: { just_signed_up: true }
```

Bu durumda view'de aşağıdaki gibi kullanılabilir:

```erb
<% if flash[:just_signed_up] %>
  <p class="welcome">Welcome to our site!</p>
<% end %>
```

Yukarıdaki logout örneğinde `destroy` işlemi, uygulamanın `root_url` adresine yönlendirir, ve mesaj gösterilmeye hazır olur. Ancak mesaj otomatik olarak gösterilmez. Mesajın görüntülenip görüntülenmeyeceğine sonraki işlem karar verir.

#### Flash Mesajlarını Görüntülemek

Eğer önceki işlem bir flash mesajı ayarlamışsa, kullanıcıya bunu göstermek iyi bir fikirdir. Flash mesajlarını tutarlı şekilde göstermek için uygulamanın varsayılan düzenine (layout) HTML eklenebilir. Örneğin `app/views/layouts/application.html.erb` dosyasında:

```erb
<html>
  <!-- <head/> -->
  <body>
    <% flash.each do |name, msg| -%>
      <%= content_tag :div, msg, class: name %>
    <% end -%>

    <!-- more content -->
    <%= yield %>
  </body>
</html>
```

Buradaki `name`, flash mesaj türünü belirtir (örneğin `notice` veya `alert`). Bu bilgi genellikle mesajın nasıl görüneceğini stilize etmek için kullanılır.

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
  Layout’ta yalnızca <code>notice</code> ve <code>alert</code> göstermek isterseniz, <code>name</code>’e göre filtreleyebilirsiniz. Aksi halde flash’ta ayarlanan tüm anahtarlar gösterilir.
  </div>
</div>

Flash mesajlarını okumayı ve görüntülemeyi layout’a dahil etmek, uygulamanızın bunları otomatik olarak göstermesini sağlar; her view'in flash’ı ayrı okumasına gerek kalmaz.

#### `flash.keep` ve `flash.now`

[`flash.keep`][], flash değerinin ek bir isteğe aktarılmasını sağlar. Bu, birden fazla yönlendirme olduğunda kullanışlıdır.

Örneğin, aşağıdaki denetleyicideki `index` eyleminin `root_url` ile eşleştiğini varsayalım. Ve buradaki tüm isteklerin `UsersController#index` adresine yönlendirilmesini istiyorsunuz. Bir action, flash'ı ayarlayıp `MainController#index` adresine yönlendirirse, başka bir istek için değerlerin kalıcı olmasını sağlamak üzere `flash.keep` kullanmadığınız sürece, başka bir yönlendirme gerçekleştiğinde bu flash değerleri kaybolacaktır.

```ruby
class MainController < ApplicationController
  def index
    # Tüm flash değerlerini koru
    flash.keep

    # Belirli bir anahtarı korumak isterseniz:
    # flash.keep(:notice)
    redirect_to users_url
  end
end
```

[`flash.now`][], flash değerlerini aynı istek içinde kullanılabilir hale getirir.
Varsayılan olarak, flash’a değer eklemek bir sonraki istekte bunların kullanılabilir olmasını sağlar. Örneğin, `create` işlemi bir kaynağı kaydedemezse ve `new` şablonunu doğrudan render ederse, bu yeni bir istek oluşturmaz; ancak yine de mesaj göstermek isteyebilirsiniz. Bunu yapmak için, flash’ı aşağıdaki gibi `flash.now` ile kullanabilirsiniz:

```ruby
class ClientsController < ApplicationController
  def create
    @client = Client.new(client_params)
    if @client.save
      # ...
    else
      flash.now[:error] = "Could not save client"
      render action: "new"
    end
  end
end
```

[`flash`]: https://api.rubyonrails.org/classes/ActionDispatch/Flash/RequestMethods.html#method-i-flash  
[`flash.keep`]: https://api.rubyonrails.org/classes/ActionDispatch/Flash/FlashHash.html#method-i-keep  
[`flash.now`]: https://api.rubyonrails.org/classes/ActionDispatch/Flash/FlashHash.html#method-i-now

### Oturum Depoları

Tüm oturumların, oturum nesnesini temsil eden benzersiz bir ID'si vardır; bu oturum ID'leri bir çerezde saklanır. Gerçek oturum nesneleri şu depolama mekanizmalarından biriyle saklanır:

* [`ActionDispatch::Session::CookieStore`](https://api.rubyonrails.org/classes/ActionDispatch/Session/CookieStore.html) – Her şeyi istemci tarafında saklar.
* [`ActionDispatch::Session::CacheStore`](https://api.rubyonrails.org/classes/ActionDispatch/Session/CacheStore.html) – Verileri Rails önbelleğinde saklar.
* [`ActionDispatch::Session::ActiveRecordStore`](https://github.com/rails/activerecord-session_store) – Verileri Active Record kullanarak bir veritabanında saklar (bu yöntem için [`activerecord-session_store`](https://github.com/rails/activerecord-session_store) isimli gem gerekir).
* Özel bir depolayıcı ya da üçüncü taraf bir gem tarafından sağlanan bir depolayıcı.

Çoğu oturum deposu için, çerezdeki benzersiz oturum ID'si oturum verilerini sunucuda (örneğin bir veritabanı tablosu içinde) bulmak için kullanılır. Rails, oturum ID'sini URL ile geçmenize izin vermez, çünkü bu daha az güvenlidir.

#### `CookieStore`

`CookieStore` varsayılan ve önerilen oturum deposudur. Tüm oturum verisini doğrudan çerezin içinde saklar (yine de oturum ID'si gerektiğinde kullanılabilir). `CookieStore` hafif bir yapıya sahiptir ve yeni bir uygulamada kullanmak için yapılandırmaya ihtiyaç duymaz.

`CookieStore` yaklaşık 4 kB veri saklayabilir – diğer depolama seçeneklerinden çok daha azdır – ancak genellikle yeterlidir. Oturuma büyük miktarda veri kaydetmek önerilmez. Özellikle karmaşık nesnelerin (örneğin model örneklerinin) oturumda saklanmasından kaçınılmalıdır.

#### `CacheStore`

Eğer oturumlarınızda kritik veri yoksa ya da uzun süreli saklama gerekmiyorsa (örneğin yalnızca flash mesajlar için kullanılıyorsa), `CacheStore` kullanılabilir. Oturumları, uygulamanızda yapılandırdığınız önbellek sistemiyle saklar. Avantajı, mevcut önbellek altyapınızı oturumlar için de kullanabilmenizdir – ek kurulum veya yönetim gerekmez. Dezavantajı ise oturum verisinin geçici olması ve herhangi bir anda kaybolma riskinin bulunmasıdır.

Oturum depolama hakkında daha fazlası için [Güvenlik Rehberi](../security/#oturumlar)'ni inceleyin.

### Oturum Saklama Seçenekleri

Oturum depolamayla ilgili birkaç yapılandırma seçeneği mevcuttur. Depolama türünü bir initializer dosyasında ayarlayabilirsiniz:

```ruby
Rails.application.config.session_store :cache_store
```

Rails, oturum verisini imzalarken bir oturum anahtarı (çerezin adı) belirler. Bunlar da bir initializer dosyasında değiştirebilir:

```ruby
Rails.application.config.session_store :cookie_store, key: "_your_app_session"
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
  Initializer dosyasını değiştirdikten sonra sunucunuzu yeniden başlatmayı unutmayın.
  </div>
</div>

Çerez için bir alan adı belirtmek isterseniz, `:domain` anahtarıyla geçirebilirsiniz:

```ruby
Rails.application.config.session_store :cookie_store, key: "_your_app_session", domain: ".example.com"
```

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
  Daha fazla bilgi için <a href="../configuring/#config-session-store">Yapılandırma Rehberi</a>'nde <code><a href="../configuring/#config-session-store">config.session_store</a></code> kısmına göz atın.
  </div>
</div>

Rails, `CookieStore` için `config/credentials.yml.enc` içinde oturum verisini imzalamakta kullanılan gizli bir anahtar ayarlar. Bu kimlik bilgileri `bin/rails credentials:edit` komutuyla güncellenebilir:

```yaml
# aws:
#   access_key_id: 123
#   secret_access_key: 345

# Rails'teki tüm MessageVerifier işlemleri için temel gizli anahtar olarak kullanılır; çerezleri korumak da dahil.
secret_key_base: 492f...
```

<div class="guide-alert guide-alert-danger">
  <div class="guide-alert-icon">❗</div>
  <div class="guide-alert-content">
  <code>CookieStore</code> kullanırken <code>secret_key_base</code> değerini değiştirmek, var olan tüm oturumları geçersiz kılar. Mevcut oturumları döndürmek için bir <a href="https://edgeguides.rubyonrails.org/configuring.html#config-action-dispatch-cookies-rotations">çerez döndürücü</a> yapılandırmanız gerekir.
  </div>
</div>

Controller Callbacks
--------------------

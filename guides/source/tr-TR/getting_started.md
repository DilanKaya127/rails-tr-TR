**RESMİ KILAVUZA BU ADRES ÜZERİNDEN ULAŞABİLİRSİNİZ: <https://guides.rubyonrails.org>**

---------------------------------------------------------------------------------------

Rails ile Başlarken
===================

Bu kılavuz Ruby on Rails'i nasıl kurup çalıştıracağınızı anlatmaktadır.

Bu kılavuzu okuduktan sonra şunları öğreneceksiniz:

* Rails'i nasıl kuracağınızı, yeni bir Rails uygulaması nasıl oluşturacağınızı ve uygulamanızı bir veritabanına nasıl bağlayacağınızı.
* Rails uygulamasının genel yapısını.
* MVC (Model, View, Controller) ve RESTful tasarımının temel ilkelerini.
* Rails uygulamasının başlangıç parçalarını hızlı bir şekilde nasıl oluşturacağınızı.
* Kamal kullanarak uygulamanızı üretim ortamına nasıl dağıtacağınızı (deploy edeceğinizi).


Giriş
-----

Ruby on Rails'e hoş geldiniz! Bu kılavuzda, Rails ile web uygulamaları geliştirmenin temel konularını ele alacağız. Bu kılavuzu takip etmek için Rails'te herhangi bir deneyime ihtiyacınız yok.

Rails, Ruby programlama dili için geliştirilmiş bir web framework'üdür. Rails, Ruby'nin birçok özelliğinin avantajını barındırmaktadır, bu yüzden sizlere Ruby'nin temellerini öğrenmenizi *şiddetle* öneririz. Böylece bu kılavuzda karşılacağınız bazı temel terimleri ve kelimeleri anlıyor olacaksınız.

- [Resmi Ruby Websitesi](https://www.ruby-lang.org/en/documentation/)
- [Ücretsiz Programlama Kitaplarını İçeren Liste](https://github.com/EbookFoundation/free-programming-books/blob/main/books/free-programming-books-langs.md#ruby)

Rails'in Felsefesi
------------------

Rails, Ruby programlama dilinde yazılmış bir web uygulaması geliştirme framework'üdür. Her geliştiricinin başlangıç için ihtiyaç duyduğu şeyleri varsayarak web uygulamalarını programlamayı kolaylaştırmak için tasarlanmıştır. Diğer birçok dil ve framework'e göre daha az kod yazarak daha fazlasını başarmanızı sağlar. Deneyimli Rails geliştiricileri, web uygulaması geliştirmeyi daha eğlenceli hale getirdiğini de belirtmektedir.

Rails, inatçı bir yazılımdır. İşleri yapmanın “en iyi” bir yolu olduğu varsayımından hareket eder ve bu yolu teşvik etmek için tasarlanmıştır - ve bazı durumlarda alternatiflerden caydırır. “Rails Yöntemi”ni öğrenirseniz, muhtemelen üretkenliğinizde muazzam bir artış göreceksiniz. Diğer dillerden edindiğiniz eski alışkanlıkları Rails geliştirirken ısrarla sürdürür ve başka yerlerde öğrendiğiniz kalıpları kullanmaya çalışırsanız, daha az mutlu bir deneyim yaşayabilirsiniz.

Rails felsefesi iki ana kılavuz ilke içerir:

- **Kendini Tekrar Etme:** DRY (Don't Repeat Yourself), yazılım geliştirmenin bir ilkesidir ve “Her bilgi parçası; sistem içinde tek, açık ve kesin bir şekilde temsil edilmelidir” der. Aynı bilgiyi tekrar tekrar yazmayarak kodumuz daha kolay bakımı yapılabilir, daha genişletilebilir ve daha az hata içerir hale gelir.
- **Yapılandırma Üzerine Kurallar:** Rails, bir web uygulamasında birçok şeyin en iyi şekilde nasıl yapılacağına dair fikirlere sahiptir ve bunları sonsuz yapılandırma dosyaları aracılığıyla kendiniz tanımlamanızı gerektirmek yerine, varsayılan olarak bu kurallar kümesini kullanır.

Yeni Bir Rails Uygulaması Geliştirme
------------------------------------

`store` adında bir proje geliştireceğiz, Rails'in yerleşik özelliklerinden birkaçını gösteren basit bir e-ticaret sitesi olacak.

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
    Dolar işareti <code>$</code> ile başlayan tüm komutlar terminalde çalıştırılmalıdır.
  </div>
</div>

### Ön Koşullar

Bu proje için şunlar gerekli:

* Ruby 3.2 veya daha üst sürümü
* Rails 8.1.0 veya daha üst sürümü
* Bir kod editörü

Ruby ve/veya Rails'i indirmeniz gerekiyorsa, bu tutorialı takip edin: [Ruby on Rails İndirme Kılavuzu](../install_ruby_on_rails/)

Rails'in doğru sürümünün yüklü olduğunu doğrulayalım. Mevcut sürümü görüntülemek için bir terminal açın ve aşağıdakileri çalıştırın. Bir sürüm numarası görmelisiniz:

```bash
$ rails --version
Rails 8.1.0
```

Rails 8.1.0 veya daha yüksek bir sürüm olmalı.

### İlk Rails Uygulamanızı Oluşturma


Rails, hayatınızı kolaylaştırmak için çeşitli komutlar sunar. Tüm komutları görmek için `rails --help` komutunu çalıştırın.

`rails new` komutu, yeni bir Rails uygulamasının temelini oluşturur, bu yüzden buradan başlayalım.

`store` uygulamamızı oluşturmak için terminalinizde aşağıdaki komutu çalıştırın:

```bash
$ rails new store
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    Rails'in oluşturduğu uygulamayı flag kullanarak özelleştirebilirsiniz.
Bu seçenekleri görmek için <code>rails new --help</code> komutunu çalıştırın.
  </div>
</div>

Yeni uygulamanız oluşturulduktan sonra, uygulama dizinine geçin:

```bash
$ cd store
```

### Dizin Yapısı

Yeni bir Rails uygulamasında yer alan dosya ve dizinlere hızlıca bir göz atalım. Dosyaları ve dizinleri görmek için bu klasörü kod editörünüzde açabilir veya terminalinizde `ls -la` komutunu çalıştırabilirsiniz.

| Dosya/Klasör | Amacı |
| ------------ | ----- |
|app/|Uygulamanız için controllers, models, views, helpers, mailer, jobs ve assets içerir. **Bu kılavuzun geri kalanında çoğunlukla bu klasöre odaklanacaksınız.**|
|bin/|Uygulamanızı başlatan `rails` betiğini (scriptini) içerir ve uygulamanızı kurmak, güncellemek, dağıtmak veya çalıştırmak için kullandığınız diğer betikleri de içerebilir.|
|config/|Uygulamanızın yönlendirmeleri (route), veritabanı ve daha fazlası için yapılandırma içerir. Bu konu [Rails Uygulamalarının Yapılandırılması](../configuring/) bölümünde daha ayrıntılı olarak ele alınmıştır.|
|db/|Mevcut veritabanı şemanızın yanı sıra veritabanı değişikliklerini (migrations) de içerir.|
|Dockerfile|Docker için yapılandırma dosyasıdır.|
|Gemfile<br>Gemfile.lock|Bu dosyalar, Rails uygulamanız için hangi gem bağımlılıklarının gerekli olduğunu belirtmenizi sağlar. Bu dosyalar [Bundler](https://bundler.io) gem'i tarafından kullanılır.|
|lib/|Uygulamanız için genişletilmiş modüller.|
|log/|Uygulama log dosyaları.|
|public/|Statik dosyaları ve derlenmiş assetleri içerir. Uygulamanız çalışırken, bu dizin olduğu gibi gösterilecektir.|
|Rakefile|Bu dosya komut satırından çalıştırılabilecek görevleri bulur ve yükler. Görev tanımları Rails'in bileşenleri boyunca tanımlanmıştır. `Rakefile`ı değiştirmek yerine, uygulamanızın `lib/tasks` dizinine dosyalar ekleyerek kendi görevlerinizi eklemelisiniz.|
|README.md|Bu, uygulamanız için kısa bir kullanım kılavuzudur. Uygulamanızın ne yaptığını, nasıl kurulacağını vb. başkalarına anlatmak için bu dosyayı düzenlemelisiniz.|
|script/|Tek seferlik veya genel amaçlı [script](https://github.com/rails/rails/blob/main/railties/lib/rails/generators/rails/script/USAGE) ve [benchmark](https://github.com/rails/rails/blob/main/railties/lib/rails/generators/rails/benchmark/USAGE)  içerir.|
|storage/|Disk Hizmeti (Disk Service) için SQLite veritabanlarını ve Active Storage dosyalarını içerir. Bu konu [Active Storage](../active_storage_overview/) bölümünde ele alınmıştır.|
|test/|Birim testleri (unit test), fikstürler ve diğer test aparatları. Bunlar [Rails Uygulamalarını Test Etme](../testing/) bölümünde ele alınmıştır.|
|tmp/|Geçici dosyalar (önbellek ve pid dosyaları gibi).|
|vendor/|Tüm üçüncü taraf kodları için bir alan. Tipik bir Rails uygulamasında bu, üçüncü taraf kodlar arasında özel olarak eklenen (vendored) gem'ler içerir.|
|.dockerignore|Bu dosya Docker'a hangi dosyaları konteynere kopyalamaması gerektiğini söyler.|
|.gitattributes|Bu dosya, bir Git deposundaki belirli yollar için meta verileri tanımlar. Bu meta veriler Git ve diğer araçlar tarafından davranışlarını geliştirmek için kullanılabilir. Daha fazla bilgi için [gitattributes belgelerine](https://git-scm.com/docs/gitattributes) bakın.|
|.git/|Git deposu dosyalarını içerir.|
|.github/|GitHub'a özgü dosyaları içerir.|
|.gitignore|Bu dosya Git'e hangi dosyaları veya kalıpları (pattern) yok sayması gerektiğini söyler. Dosyaları yok sayma hakkında daha fazla bilgi için [GitHub - Ignoring files](https://help.github.com/articles/ignoring-files) adresine bakın.|
|.kamal/|Kamal'ın gizli/önemli verilerini ve dağıtım betiklerini (hook) içerir. |
|.rubocop.yml|Bu dosya RuboCop için yapılandırmayı içerir.|
|.ruby-version|Bu dosya varsayılan Ruby sürümünü içerir.|

### Model-View-Controller Temelleri

Rails kodu Model-View-Controller (MVC) mimarisi kullanılarak düzenlenmiştir. MVC ile, kodlarımızın çoğunun içinde çalıştığı üç ana yapı vardır:

* Model - Uygulamanızdaki verileri yönetir. Tipik olarak, veritabanı tablolarınızdır.
* View - Yanıtları (response) HTML, JSON, XML gibi farklı biçimlerde göstermeyi (render) yönetir.
* Controller - Her istek için kullanıcı etkileşimlerini ve  mantığı (logic) işler.

<picture class="flowdiagram">
  <source srcset="/assets/images/rails/getting_started/mvc_architecture_dark.jpg" media="(prefers-color-scheme:dark)">
  <img src="/assets/images/rails/getting_started/mvc_architecture_light.jpg">
</picture>

Şimdi MVC hakkında temel bir anlayışa sahip olduğumuza göre, Rails'te bunun nasıl kullanıldığını görelim.

Merhaba, Rails!
---------------

Uygulamamızın veritabanını oluşturarak ve Rails sunucumuzu ilk kez başlatarak kolay bir başlangıç yapalım.

Terminalinizde, `store` dizininde aşağıdaki komutları çalıştırın:

```bash
$ bin/rails db:create
```

Bu, başlangıçta uygulamanın veritabanını oluşturacaktır.

```bash
$ bin/rails server
```

NOT: Komutları bir uygulama dizini içinde çalıştırdığımızda `bin/rails` kullanmalıyız. Bu, uygulamanın Rails sürümünün kullanıldığından emin olunmasını sağlar.

Bu, statik dosyaları ve Rails uygulamanızı sunacak olan Puma adlı bir web sunucusunu başlatacaktır:

```bash
=> Booting Puma
=> Rails 8.1.0 application starting in development
=> Run `bin/rails server --help` for more startup options
Puma starting in single mode...
* Puma version: 6.4.3 (ruby 3.3.5-p100) ("The Eagle of Durango")
*  Min threads: 3
*  Max threads: 3
*  Environment: development
*          PID: 12345
* Listening on http://127.0.0.1:3000
* Listening on http://[::1]:3000
Use Ctrl-C to stop
```

Rails uygulamanızı görmek için tarayıcınızda http://localhost:3000 adresini açın. Varsayılan Rails karşılama sayfasını göreceksiniz:

![Rails welcome page](/assets/images/rails/getting_started/rails_welcome.png)

İşe yarıyor!

Bu sayfa, yeni bir Rails uygulaması için *smoke test* olup, bir sayfayı sunmak için perde arkasında her şeyin çalıştığından emin olunmasını sağlar.

Rails sunucusunu istediğiniz zaman durdurmak için terminalinizde `Ctrl-C` tuşuna basın.

### Geliştirme Aşamasında Otomatik Yükleme 

Geliştiricinin mutluluğu Rails'in temel felsefelerinden biridir ve bunu sağlamanın bir yolu da geliştirme sırasında kodun otomatik yeniden yüklenmesidir (reload).

Rails sunucusunu başlattığınızda, yeni dosyalar veya mevcut dosyalardaki değişiklikler algılanır ve otomatik olarak yüklenir veya gerektiğinde yeniden yüklenir. Bu, her değişiklikten sonra Rails sunucunuzu yeniden başlatmak zorunda kalmadan kodlamaya odaklanmanızı sağlar.

Rails uygulamalarının diğer programlama dillerinde gördüğünüz `require` deyimlerini (statement) nadiren kullandığını da fark edebilirsiniz. Rails, uygulama kodunuzu yazmaya odaklanabilmeniz için dosyaları otomatik olarak istemek üzere adlandırma kurallarını kullanır.

Daha fazla ayrıntı için [Otomatik Yükleme ve Yeniden Yükleme Sabitleri](../autoloading_and_reloading_constants/) bölümüne bakın.

Veritabanı Modeli Oluşturma
---------------------------

Active Record, ilişkisel veritabanlarını Ruby koduna eşleyen bir Rails özelliğidir.
Tablo ve kayıt oluşturma, güncelleme ve silme gibi veritabanı ile etkileşim için yapılandırılmış sorgu dilinin (SQL) oluşturulmasına yardımcı olur. Uygulamamız Rails için varsayılan olan SQLite kullanmaktadır.

Basit e-ticaret mağazamıza ürün eklemek için Rails uygulamamıza bir veritabanı tablosu ekleyerek başlayalım.

```bash
$ bin/rails generate model Product name:string
```

Bu komut Rails'e veritabanında `name` sütunu ve türü `string` olan `Product` adında bir model oluşturmasını söyler. Diğer sütun türlerini nasıl ekleyeceğinizi daha sonra öğreneceksiniz.

Terminalinizde aşağıdakileri göreceksiniz:

```bash
      invoke  active_record
      create    db/migrate/20240426151900_create_products.rb
      create    app/models/product.rb
      invoke    test_unit
      create      test/models/product_test.rb
      create      test/fixtures/products.yml
```

Bu komut birkaç şey yaparak şunları oluşturur:

1. `db/migrate` klasöründe bir şema değişikliği (migration).
2. `app/models/product.rb` içinde bir Active Record modeli.
3. Bu model için testler ve test fikstürleri.

NOT: Model adları *tekildir*, çünkü örneklenen bir model veritabanındaki tek bir kaydı temsil eder (yani, veritabanına eklemek için bir _product_ oluşturuyorsunuz).

### Veritabanı Değişiklikleri

Bir _migration_ veritabanımızda yapmak istediğimiz bir değişikliktir.

Migration'ları tanımlayarak, Rails'e veritabanındaki tabloları, sütunları veya diğer öznitelikleri eklemek, değiştirmek veya kaldırmak için veritabanını nasıl değiştireceğini söylüyoruz. Bu, geliştirme aşamasında (sadece bizim bilgisayarımızda) yaptığımız değişiklikleri takip etmemize yardımcı olur, böylece üretim ortamına (canlıda/çevrimiçiyken!) güvenli bir şekilde dağıtılabilirler.

Kod editörünüzde Rails'in bizim için oluşturduğu migration'ı açın, böylece migration'ın ne yaptığını görebiliriz. `db/migrate/<timestamp>_create_products.rb` şeklinde bir dosyada bulunur: 

```ruby
class CreateProducts < ActiveRecord::Migration[8.1]
  def change
    create_table :products do |t|
      t.string :name

      t.timestamps
    end
  end
end
```

Bu migration Rails'e `products` adında yeni bir veritabanı tablosu oluşturmasını söylüyor.

NOT: Yukarıdaki modelin aksine, Rails veritabanı tablo adlarını _çoğul_ yapar, çünkü veritabanı her modelin tüm örneklerini tutar (yani, _products_ veritabanı oluşturuyorsunuz).

Daha sonra `create_table` bloğu bu veritabanı tablosunda hangi sütunların ve türlerin tanımlanması gerektiğini belirler.

`t.timestamps` Rails'e `products` tablosunda `name` adında bir sütun oluşturmasını ve türünü `string` olarak ayarlamasını söyler.

`t.timestamps`, modellerinizde `created_at:datetime` ve `updated_at:datetime` adında iki sütun tanımlamak için bir kısayoldur. Bu sütunları Rails'teki çoğu Active Record modelinde görürsünüz ve kayıtlar oluşturulurken veya güncellenirken Active Record tarafından otomatik olarak ayarlanırlar.

### Değişiklikleri Çalıştırma
 
Artık veritabanında hangi değişikliklerin yapılacağını belirlediğinize göre, migration'ları çalıştırmak için aşağıdaki komutu kullanın:

```bash
$ bin/rails db:migrate
```

Bu komut yeni migration'ları kontrol eder ve bunları veritabanınıza uygular. Çıktısı şu şekilde görünür:

```bash
== 20240426151900 CreateProducts: migrating ===================================
-- create_table(:products)
   -> 0.0030s
== 20240426151900 CreateProducts: migrated (0.0031s) ==========================
```

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
    Eğer bir hata yaparsanız, son migration'ı geri almak için <code>bin/rails db:rollback</code> komutunu çalıştırabilirsiniz.
  </div>
</div>

Rails Konsolu
-------------

Artık products tablomuzu oluşturduğumuza göre, Rails'te onunla etkileşime geçebiliriz. Hadi deneyelim.

Bunun için *console* adı verilen bir Rails özelliğini kullanacağız. Konsol, Rails uygulamamızdaki kodumuzu test etmek için yararlı, etkileşimli bir araçtır.

```bash
$ bin/rails console
```

Aşağıdaki gibi bir komut istemi ile karşılaşacaksınız:

```irb
Loading development environment (Rails 8.1.0)
store(dev)>
```

Burada `Enter` tuşuna bastığımızda çalıştırılacak kodu yazabiliriz. Rails sürümünü yazdırmayı deneyelim:

```irb
store(dev)> Rails.version
=> "8.1.0"
```

İşe yarıyor!

Active Record Model Temelleri
-----------------------------

Rails model oluşturucusunu `Product` modelini oluşturmak için çalıştırdığımızda, `app/models/product.rb` adresinde bir dosya oluşturdu. Bu dosya, `products` veritabanı tablomuzla etkileşim için Active Record kullanan bir sınıf oluşturur.

```ruby
class Product < ApplicationRecord
end
```

Bu sınıfta hiç kod olmaması sizi şaşırtabilir. Rails bu modeli neyin tanımladığını nasıl biliyor?

`Product` modeli kullanıldığında, Rails sütun adları ve türleri için veritabanı tablosunu sorgulayacak ve bu nitelikler (attribute) için otomatik olarak kod oluşturacaktır. Rails bizi bu şablon kodu yazmaktan kurtarır ve bunun yerine uygulama mantığımıza odaklanabilmemiz için bu işi bizim yerimize perde arkasında halleder.

Rails'in Product modeli için hangi sütunları algıladığını görmek için Rails konsolunu kullanalım.

Çalıştırın:

```irb
store(dev)> Product.column_names
```

Bunu görmelisiniz:

```irb
=> ["id", "name", "created_at", "updated_at"]
```

Rails, veritabanından yukarıdaki sütun bilgilerini istedi ve bu bilgileri `Product' sınıfındaki öznitelikleri dinamik olarak tanımlamak için kullandı, böylece her birini manuel olarak sizin tanımlamanız gerekmez. Bu, Rails'in geliştirmeyi nasıl kolaylaştırdığının bir örneğidir.

### Kayıtları Oluşturma

Aşağıdaki kod ile yeni bir Product kaydı oluşturabiliriz:

```irb
store(dev)> product = Product.new(name: "T-Shirt")
=> #<Product:0x000000012e616c30 id: nil, name: "T-Shirt", created_at: nil, updated_at: nil>
```

`product` değişkeni `Product`'ın bir örneklenmesidir. Veritabanına kaydedilmemiştir ve bu nedenle bir ID, created_at veya updated_at gibi zaman damgaları yoktur.

Kaydı veritabanına yazmak için `save` çağrısı yapabiliriz.

```irb
store(dev)> product.save
  TRANSACTION (0.1ms)  BEGIN immediate TRANSACTION /*application='Store'*/
  Product Create (0.9ms)  INSERT INTO "products" ("name", "created_at", "updated_at") VALUES ('T-Shirt', '2024-11-09 16:35:01.117836', '2024-11-09 16:35:01.117836') RETURNING "id" /*application='Store'*/
  TRANSACTION (0.9ms)  COMMIT TRANSACTION /*application='Store'*/
=> true
```

`save` çağrıldığında, Rails bellekteki öznitelikleri alır ve bu kaydı veritabanına eklemek için bir `INSERT` SQL sorgusu oluşturur.

Rails ayrıca bellekteki nesneyi `created_at` ve `updated_at` zaman damgalarıyla birlikte veritabanı kaydı `id` ile günceller. Bunu `product` değişkenini yazdırarak görebiliriz.

```irb
store(dev)> product
=> #<Product:0x00000001221f6260 id: 1, name: "T-Shirt", created_at: "2024-11-09 16:35:01.117836000 +0000", updated_at: "2024-11-09 16:35:01.117836000 +0000">
```

`save`e benzer şekilde, bir Active Record nesnesini tek bir çağrıda oluşturmak ve kaydetmek için `create` kullanabiliriz.

```irb
store(dev)> Product.create(name: "Pants")
  TRANSACTION (0.1ms)  BEGIN immediate TRANSACTION /*application='Store'*/
  Product Create (0.4ms)  INSERT INTO "products" ("name", "created_at", "updated_at") VALUES ('Pants', '2024-11-09 16:36:01.856751', '2024-11-09 16:36:01.856751') RETURNING "id" /*application='Store'*/
  TRANSACTION (0.1ms)  COMMIT TRANSACTION /*application='Store'*/
=> #<Product:0x0000000120485c80 id: 2, name: "Pants", created_at: "2024-11-09 16:36:01.856751000 +0000", updated_at: "2024-11-09 16:36:01.856751000 +0000">
```

### Kayıtları Sorgulama

Active Record modelimizi kullanarak veritabanındaki kayıtlara da bakabiliriz.

Veritabanındaki tüm Product kayıtlarını bulmak için `all` metodunu kullanabiliriz.
Bu bir _sınıf_ metodudur, bu yüzden Product üzerinde kullanabiliriz (yukarıdaki `save` gibi product örneklemesi/örneği üzerinde çağıracağımız bir örnekleme metoduna karşılık gelir).

```irb
store(dev)> Product.all
  Product Load (0.1ms)  SELECT "products".* FROM "products" /* loading for pp */ LIMIT 11 /*application='Store'*/
=> [#<Product:0x0000000121845158 id: 1, name: "T-Shirt", created_at: "2024-11-09 16:35:01.117836000 +0000", updated_at: "2024-11-09 16:35:01.117836000 +0000">,
 #<Product:0x0000000121845018 id: 2, name: "Pants", created_at: "2024-11-09 16:36:01.856751000 +0000", updated_at: "2024-11-09 16:36:01.856751000 +0000">]
```

Bu, `products` tablosundaki tüm kayıtları yüklemek için bir `SELECT` SQL sorgusu oluşturur. Her kayıt otomatik olarak Product Active Record modelimizin bir örneğine dönüştürülür, böylece Ruby'den onlarla kolayca çalışabiliriz.

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
    <code>all</code> metodu; filtreleme, sıralama ve diğer veritabanı işlemlerini yürütme özelliklerine sahip veritabanı kayıtlarının Array benzeri bir koleksiyonu olan bir <code>ActiveRecord::Relation</code> nesnesini döndürür.
  </div>
</div>

### Kayıtları Filtreleme ve Sıralama

Veritabanımızdaki sonuçları filtrelemek istersek ne olur? Kayıtları bir sütuna göre filtrelemek için `where` kullanabiliriz.

```irb
store(dev)> Product.where(name: "Pants")
  Product Load (1.5ms)  SELECT "products".* FROM "products" WHERE "products"."name" = 'Pants' /* loading for pp */ LIMIT 11 /*application='Store'*/
=> [#<Product:0x000000012184d858 id: 2, name: "Pants", created_at: "2024-11-09 16:36:01.856751000 +0000", updated_at: "2024-11-09 16:36:01.856751000 +0000">]
```

Bu bir `SELECT` SQL sorgusu oluşturur, ancak `"Pants"` ile eşleşen bir `name`e sahip kayıtları filtrelemek için bir `WHERE` cümleciği de ekler. Bu aynı zamanda bir `ActiveRecord::Relation` döndürür çünkü birden fazla kayıt aynı isme sahip olabilir.

Kayıtları isme göre artan alfabetik sırada sıralamak için `order(name: :asc)` kullanabiliriz.

```irb
store(dev)> Product.order(name: :asc)
  Product Load (0.3ms)  SELECT "products".* FROM "products" /* loading for pp */ ORDER BY "products"."name" ASC LIMIT 11 /*application='Store'*/
=> [#<Product:0x0000000120e02a88 id: 2, name: "Pants", created_at: "2024-11-09 16:36:01.856751000 +0000", updated_at: "2024-11-09 16:36:01.856751000 +0000">,
 #<Product:0x0000000120e02948 id: 1, name: "T-Shirt", created_at: "2024-11-09 16:35:01.117836000 +0000", updated_at: "2024-11-09 16:35:01.117836000 +0000">]
```

### Kayıtları Bulma

Peki ya belirli bir kaydı bulmak istiyorsak?

Bunu, tek bir kaydı ID'ye göre aramak için `find` sınıf metodunu kullanarak yapabiliriz. Aşağıdaki kodu kullanarak metodu çağırın ve belirli bir ID girin:

```irb
store(dev)> Product.find(1)
  Product Load (0.2ms)  SELECT "products".* FROM "products" WHERE "products"."id" = 1 LIMIT 1 /*application='Store'*/
=> #<Product:0x000000012054af08 id: 1, name: "T-Shirt", created_at: "2024-11-09 16:35:01.117836000 +0000", updated_at: "2024-11-09 16:35:01.117836000 +0000">
```

Bu bir `SELECT` sorgusu oluşturur, ancak `id` sütunu için aktarılan `1` ile eşleşen bir `WHERE` belirtir. Ayrıca, yalnızca tek bir kayıt döndürmek için bir `LIMIT` ekler.

Bu kez, veritabanından yalnızca tek bir kayıt aldığımız için `ActiveRecord::Relation` yerine bir `Product` örneği alıyoruz.

### Kayıtları Güncelleme

Kayıtlar 2 şekilde güncellenebilir: `update` kullanılarak veya öznitelikler atanarak ve `save` çağrısı yapılarak.

Bir Product örneği üzerinde `update` çağrısı yapabilir ve veritabanına kaydedilecek yeni özelliklerin bir Hash'ini iletebiliriz. Bu; öznitelikleri atayacak, doğrulamaları (validations) çalıştıracak ve değişiklikleri tek bir metot çağrısında veritabanına kaydedecektir.

```irb
store(dev)> product = Product.find(1)
store(dev)> product.update(name: "Shoes")
  TRANSACTION (0.1ms)  BEGIN immediate TRANSACTION /*application='Store'*/
  Product Update (0.3ms)  UPDATE "products" SET "name" = 'Shoes', "updated_at" = '2024-11-09 22:38:19.638912' WHERE "products"."id" = 1 /*application='Store'*/
  TRANSACTION (0.4ms)  COMMIT TRANSACTION /*application='Store'*/
=> true
```

Bu, veritabanındaki "T-Shirt" ürününün adını "Shoes" olarak güncelledi.
Bunu `Product.all` dosyasını tekrar çalıştırarak görün.

```irb
store(dev)> Product.all
```

İki ürün göreceksiniz: Ayakkabı ve Pantolon.

```irb
  Product Load (0.3ms)  SELECT "products".* FROM "products" /* loading for pp */ LIMIT 11 /*application='Store'*/
=>
[#<Product:0x000000012c0f7300
  id: 1,
  name: "Shoes",
  created_at: "2024-12-02 20:29:56.303546000 +0000",
  updated_at: "2024-12-02 20:30:14.127456000 +0000">,
 #<Product:0x000000012c0f71c0
  id: 2,
  name: "Pants",
  created_at: "2024-12-02 20:30:02.997261000 +0000",
  updated_at: "2024-12-02 20:30:02.997261000 +0000">]
```

Alternatif olarak, öznitelikleri atayabilir, değişiklikleri doğrulamaya ve veritabanına kaydetmeye hazır olduğumuzda `save` çağrısı yapabiliriz.

"Ayakkabı" adını yeniden "T-Shirt" olarak değiştirelim.

```irb
store(dev)> product = Product.find(1)
store(dev)> product.name = "T-Shirt"
=> "T-Shirt"
store(dev)> product.save
  TRANSACTION (0.1ms)  BEGIN immediate TRANSACTION /*application='Store'*/
  Product Update (0.2ms)  UPDATE "products" SET "name" = 'T-Shirt', "updated_at" = '2024-11-09 22:39:09.693548' WHERE "products"."id" = 1 /*application='Store'*/
  TRANSACTION (0.0ms)  COMMIT TRANSACTION /*application='Store'*/
=> true
```

### Kayıtları Silme

Bir kaydı veritabanından silmek için `destroy` metodu kullanılabilir.

```irb
store(dev)> product.destroy
  TRANSACTION (0.1ms)  BEGIN immediate TRANSACTION /*application='Store'*/
  Product Destroy (0.4ms)  DELETE FROM "products" WHERE "products"."id" = 1 /*application='Store'*/
  TRANSACTION (0.1ms)  COMMIT TRANSACTION /*application='Store'*/
=> #<Product:0x0000000125813d48 id: 1, name: "T-Shirt", created_at: "2024-11-09 22:39:38.498730000 +0000", updated_at: "2024-11-09 22:39:38.498730000 +0000">
```

Bu işlem T-Shirt ürününü veritabanımızdan sildi. Bunu `Product.all` ile doğrulayarak yalnızca Pants ürününü döndürdüğünü görebiliriz.

```irb
store(dev)> Product.all
  Product Load (1.9ms)  SELECT "products".* FROM "products" /* loading for pp */ LIMIT 11 /*application='Store'*/
=>
[#<Product:0x000000012abde4c8
  id: 2,
  name: "Pants",
  created_at: "2024-11-09 22:33:19.638912000 +0000",
  updated_at: "2024-11-09 22:33:19.638912000 +0000">]
```

### Doğrulamalar

Active Record, veritabanına eklenen verilerin belirli kurallara uymasını sağlamanıza olanak tanıyan *doğrulamalar* (validations) sağlar.

Tüm ürünlerin bir `name`e sahip olması gerektiğinden emin olmak için Product modeline bir `presence` doğrulaması ekleyelim.

```ruby
class Product < ApplicationRecord
  validates :name, presence: true
end
```

Rails'in geliştirme sırasında değişiklikleri otomatik olarak yeniden yüklediğini hatırlayabilirsiniz. Ancak, kodda güncelleme yaptığınızda konsol çalışıyorsa, konsolu manuel olarak yenilemeniz gerekir. Şimdi bunu "reload!" komutunu çalıştırarak yapalım.

```irb
store(dev)> reload!
Reloading...
```

Rails konsolunda isimsiz bir Product oluşturmayı deneyelim.

```irb
store(dev)> product = Product.new
store(dev)> product.save
=> false
```

Bu kez `save`, `name` özniteliği belirtilmediği için `false` döndürür.

Rails, geçerli girdiden emin olmak için oluşturma, güncelleme ve kaydetme işlemleri sırasında otomatik olarak doğrulamaları çalıştırır. Doğrulamalar tarafından oluşturulan hataların bir listesini görmek için, örnekleme üzerinde `errors` çağrısı yapabiliriz.

```irb
store(dev)> product.errors
=> #<ActiveModel::Errors [#<ActiveModel::Error attribute=name, type=blank, options={}>]>
```

Bu, bize tam olarak hangi hataların mevcut olduğunu söyleyebilen bir `ActiveModel::Errors` nesnesi döndürür.

Ayrıca bizim için kullanıcı arayüzümüzde kullanabileceğimiz dostça hata mesajları da oluşturabilir.

```irb
store(dev)> product.errors.full_messages
=> ["Name can't be blank"]
```

Şimdi Products için bir web arayüzü oluşturalım.

Şimdilik konsolla işimiz bitti, bu yüzden `exit` komutunu çalıştırarak konsoldan çıkabilirsiniz.

Bir İsteğin Rails'teki Yolculuğu
--------------------------------

Rails'in "Merhaba" demesini sağlamak için en azından bir _route_, bir _action_ içeren bir _controller_ ve bir _view_ oluşturmanız gerekir. Route, bir isteği bir controller eylemiyle (action) eşler. Bir controller eylemi, isteği işlemek için gerekli çalışmaları gerçekleştirir ve view için verileri hazırlar. Bir view, verileri istenen biçimde görüntüler.

Uygulanma açısından: Route'lar bir Ruby [DSL (Domain-Specific Language)](https://en.wikipedia.org/wiki/Domain-specific_language) dili ile yazılmış kurallardır.Controller'lar Ruby sınıflarıdır ve genel metotları eylemlerdir. View'ler ise genellikle HTML ve Ruby karışımıyla yazılmış şablonlardır.

Kısaca bu şekilde, ancak bu adımların her birini daha ayrıntılı olarak inceleyeceğiz.

Route
-----

Rails'te route (rota), gelen bir HTTP isteğinin işlenmek üzere uygun controller'a ve eyleme nasıl yönlendirileceğini belirleyen URL'in bir parçasıdır. İlk olarak, URL'ler ve HTTP İstek metotları hakkında hızlı bir hatırlatma yapalım.

### URL'in Parçaları

Bir URL'in farklı parçalarını inceleyelim:

```
https://example.org/products?sale=true&sort=asc
```

Bu URL'de her parçanın bir adı vardır:

- `https` bir **protokol**dür.
- `example.org` bir **hizmet sağlayıcı**dır.
- `/products` bir **yol** adıdır.
- `?sale=true&sort=asc` bir **sorgu parametresi**dir.

### HTTP Metotları ve Amaçları

HTTP istekleri, sunucuya belirli bir URL için hangi eylemi gerçekleştirmesi gerektiğini söylemek için metotlar kullanır. İşte en yaygın metotlar:

- Bir `GET` isteği, sunucuya belirli bir URL için verileri almasını söyler (örn,
  bir sayfa yükleme veya bir kayıt getirme).
- Bir `POST` isteği, işlenmek üzere URL'ye veri gönderir (genellikle yeni bir kayıt oluşturur).
- Bir `PUT` veya `PATCH` isteği, mevcut bir kaydı güncellemek için bir URL'ye veri gönderir.
- Bir URL'ye yapılan `DELETE` isteği sunucuya bir kaydı silmesini söyler.

### Rails'te Route

Rails'teki bir `route` , bir HTTP metodu ile bir URL yolunu eşleştiren bir kod satırını ifade eder. Route ayrıca Rails'e bir isteğe hangi `controller` ve `action`ın yanıt vermesi gerektiğini söyler.

Rails'te bir route (rota) tanımlamak için kod editörünüze geri dönün ve
aşağıdaki route'u `config/routes.rb` dosyasına ekleyin.

```ruby
Rails.application.routes.draw do
  get "/products", to: "products#index"
end
```

Bu route Rails'e `/products` yoluna GET istekleri aramasını söyler. Bu örnekte, isteğin yönlendirileceği yer için `"products#index"`i belirttik.

Rails eşleşen bir istek gördüğünde, isteği `ProductsController` ve bu controller'ın içindeki `index` eylemine gönderecektir. Rails bu şekilde isteği işleyecek ve tarayıcıya bir yanıt döndürecektir.

Route'larımızda protokol, domain (alan adı) veya sorgu parametrelerini belirtmemize gerek olmadığını fark edeceksiniz. Bunun temel nedeni; protokol ve domain'in, isteğin sunucunuza ulaşmasını sağlamasıdır. Buradan, Rails isteği alır ve hangi route'ların tanımlandığına bağlı olarak isteğe yanıt vermek için hangi yolu kullanacağını bilir. Sorgu parametreleri, Rails'in isteğe uygulamak için kullanabileceği seçenekler gibidir, bu nedenle genellikle controller'da verileri filtrelemek için kullanılırlar.

<picture class="flowdiagram">
  <source srcset="/assets/images/rails/getting_started/routing_dark.jpg" media="(prefers-color-scheme:dark)">
  <img src="/assets/images/rails/getting_started/routing_light.jpg">
</picture>

Başka bir örneğe bakalım. Bu satırı önceki route'tan sonra ekleyin:

```ruby
post "/products", to: "products#create"
```

Burada, Rails'e "/products" adresine POST isteklerini almasını ve bunları `create' eylemini kullanarak `ProductsController' ile işlemesini söyledik.

Route'ların belirli kalıplara sahip URL'ler ile eşleşmesi de gerekebilir. Peki bu nasıl çalışır?

```ruby
get "/products/:id", to: "products#show"
```

Bu route'un içinde `:id` var. Buna `parametre` denir ve bu, daha sonra isteği işlemek için kullanılmak üzere URL'in bir bölümünü yakalar.

Bir kullanıcı `/products/1` öğesini ziyaret ederse, `:id` parametresi `1` olarak ayarlanır ve bu, controller eyleminde 1 ID'sine sahip Product kaydını aramak ve görüntülemek için kullanılabilir. `/products/2`, ID'si 2 olan Product'ı görüntüler ve bu böyle devam eder.

Route parametrelerinin Integer olması da gerekmez.

Örneğin, makaleler içeren bir blogunuz olabilir ve `/blog/hello-world` ile aşağıdaki route'u eşleştirebilirsiniz:

```ruby
get "/blog/:title", to: "blog#show"
```

Rails, `/blog/hello-world` içinden `hello-world` öğesini yakalayacaktır ve bu, eşleşen başlık ismine (title) sahip blog gönderisini aramak için kullanılabilir.

#### CRUD Route'ları

Bir kaynak için genellikle ihtiyaç duyacağınız 4 yaygın eylem vardır: Create (Oluştur), Read (Oku), Update (Güncelle), Delete (Sil) (CRUD). Bu da 8 tipik route'a karşılık gelir:

* Index - Tüm kayıtları gösterir
* New - Yeni bir kayıt oluşturmak için bir form oluşturur
* Create - Yeni form gönderimini işler, hataları ele alır ve kaydı oluşturur
* Show - Spesifik bir kaydı görüntülemek için işler
* Edit - Spesifik bir kaydı güncellemek için bir formu işler
* Update (full - tam) - Edit formu gönderimini yönetir, hataları ele alır ve tüm kaydı günceller ve genellikle bir PUT isteği tarafından tetiklenir.
* Update (partial - kısmi) - Edit formu gönderimini yönetir, hataları ele alır ve kaydın belirli özniteliklerini günceller ve genellikle bir PATCH isteği tarafından tetiklenir.
* Destroy - Spesifik bir kaydı silmeyi yönetir

Bu CRUD eylemleri için aşağıdaki şekilde route'lar ekleyebiliriz:

```ruby
get "/products", to: "products#index"

get "/products/new", to: "products#new"
post "/products", to: "products#create"

get "/products/:id", to: "products#show"

get "/products/:id/edit", to: "products#edit"
patch "/products/:id", to: "products#update"
put "/products/:id", to: "products#update"

delete "/products/:id", to: "products#destroy"
```

#### Kaynak Route'lar

Bu route'ları her seferinde yazmak gereksizdir, bu yüzden Rails bunları tanımlamak için bir kısayol sağlar. Aynı CRUD route'larının tümünü oluşturmak için yukarıdaki route'ları bu tek satırla değiştirin:

```ruby
resources :products
```

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
    Tüm bu CRUD eylemlerini istemiyorsanız, tam olarak neye ihtiyacınız olduğunu belirtirsiniz. Ayrıntılar için <a href="../routing/">Routing Kılavuzu</a> bölümüne göz atın.
  </div>
</div>

### Route Komutları

Rails, uygulamanızın yanıt verdiği tüm route'ları görüntüleyen bir komut sağlar.

Terminalinizde aşağıdaki komutu çalıştırın.

```bash
$ bin/rails routes
```

`resources :products` tarafından oluşturulan route'ların çıktısını aşağıdaki şekilde göreceksiniz:

```
                                  Prefix Verb   URI Pattern                                                                                       Controller#Action
                                products GET    /products(.:format)                                                                               products#index
                                         POST   /products(.:format)                                                                               products#create
                             new_product GET    /products/new(.:format)                                                                           products#new
                            edit_product GET    /products/:id/edit(.:format)                                                                      products#edit
                                 product GET    /products/:id(.:format)                                                                           products#show
                                         PATCH  /products/:id(.:format)                                                                           products#update
                                         PUT    /products/:id(.:format)                                                                           products#update
                                         DELETE /products/:id(.:format)                                                                           products#destroy
```

Ayrıca health checks gibi diğer yerleşik Rails özelliklerinden gelen route'ları da göreceksiniz.

Controller & Action
-------------------

Artık Products için route'lar tanımladığımıza göre, bu URL'lere gelen istekleri işlemek için controller ve action'ları uygulayalım.

Bu komut, bir index action'ı ile bir `ProductsController` oluşturacaktır. Route'ları zaten ayarladığımız için, bir flag (aşağıdaki --skip-routes) kullanarak bu kısmı atlayabiliriz.

```bash
$ bin/rails generate controller Products index --skip-routes
      create  app/controllers/products_controller.rb
      invoke  erb
      create    app/views/products
      create    app/views/products/index.html.erb
      invoke  test_unit
      create    test/controllers/products_controller_test.rb
      invoke  helper
      create    app/helpers/products_helper.rb
      invoke    test_unit
```

Bu komut controller için bir avuç dosya oluşturur:

* Controller'ın kendisini
* Oluşturduğumuz controller için bir views klasörü
* Controller oluştururken belirttiğimiz action için bir view dosyası
* Bu controller için bir test dosyası
* View'lerdeki mantığı ayıklamak için bir helper dosyası

Şimdi `app/controllers/products_controller.rb` dosyasında tanımlanan ProductsController'a bir göz atalım. Şuna benziyor:

```ruby
class ProductsController < ApplicationController
  def index
  end
end
```

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    Dosya adının <code>products_controller.rb</code> olduğunu ve bu dosyanın tanımladığı <code>ProductsController</code> sınıfının alt çizgili hali olduğunu fark edebilirsiniz. Bu kalıp, Rails'in diğer dillerde gördüğünüz gibi <code>require</code> kullanmak zorunda kalmadan kodu otomatik olarak yüklemesine yardımcı olur.
  </div>
</div>

Buradaki `index` metodu bir Action'dır. Boş bir metot olmasına rağmen, Rails varsayılan olarak eşleşen bir isme sahip bir şablon oluşturacaktır.

`index` action'ı `app/views/products/index.html.erb` dosyasını oluşturacaktır. Bu dosyayı kod editörümüzde açarsak, oluşturduğu HTML'i görürüz.

```erb
<h1>Products#index</h1>
<p>Find me in app/views/products/index.html.erb</p>
```

### İstek Oluşturma

Bunu tarayıcımızda görelim. İlk olarak, Rails sunucusunu başlatmak için terminalinizde `bin/rails server` komutunu çalıştırın. Ardından http://localhost:3000 adresini açın. Rails karşılama sayfasını göreceksiniz.

Tarayıcıda http://localhost:3000/products adresini açarsak, Rails product index'ini HTML olarak oluşturacaktır.

Tarayıcımız `/products` isteğinde bulundu ve Rails bu route'u `products#index` ile eşleştirdi. Rails isteği `ProductsController`a gönderdi ve `index` action'ını çağırdı. Bu action boş olduğu için, Rails eşleşen şablonu `app/views/products/index.html.erb` adresinde işledi ve tarayıcımıza döndürdü. Çok güzel!

`config/routes.rb` dosyasını açıp bu satırı eklersek eğer, Rails'e ana dizin route'unun Products index action'ını render etmesi/ekranda göstermesi gerektiğini söyleyebiliriz:

```ruby
root "products#index"
```

Şimdi http://localhost:3000 adresini ziyaret ettiğinizde, Rails Products#index'i oluşturacaktır.

### Örnek Değişkenler

Bunu bir adım daha ileri götürelim ve veritabanımızdan bazı kayıtları render edelim.

`index` action'ında bir veritabanı sorgusu ekleyelim ve bunu bir instance variable'a atayalım. Rails, view'lerle veri paylaşmak için instance variable'ları (@ ile başlayan değişkenler) kullanır.

```ruby
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end
end
```

`app/views/products/index.html.erb` dosyasında HTML'i şu ERB ile değiştirebiliriz:

```erb
<%= debug @products %>
```

ERB, [Embedded Ruby](https://docs.ruby-lang.org/en/master/ERB.html)'nin kısaltmasıdır ve Rails ile dinamik olarak HTML oluşturmak için Ruby kodu çalıştırmamızı sağlar. `<%= %>` etiketi ERB'ye içindeki Ruby kodunu çalıştırmasını ve dönüş değerini çıktı olarak vermesini söyler. Bizim durumumuzda bu, `@products`'ı alır, YAML'a dönüştürür ve YAML'ı çıktı olarak verir.

Şimdi tarayıcınızda http://localhost:3000/ adresini yenileyin, çıktının değiştiğini göreceksiniz. Gördüğünüz şey, veritabanınızdaki kayıtların YAML formatında görüntülenmesidir.

`debug` helper'ı, hata ayıklama konusunda yardımcı olmak için değişkenleri YAML formatında yazdırır. Örneğin, dikkat etmeyip çoğul `@products` yerine tekil `@product` yazdıysanız, debug helper'ı değişkenin controller'da doğru şekilde ayarlanmadığını belirlemenize yardımcı olabilir.

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
    Mevcut olan daha fazla helper görmek için <a href="../action_view_helpers/">Action View Helpers Kılavuzu</a>'na göz atın.
  </div>
</div>

Tüm product/ürün isimlerimizi render etmek için `app/views/products/index.html.erb` dosyasını güncelleyelim.

```erb
<h1>Products</h1>

<div id="products">
  <% @products.each do |product| %>
    <div>
      <%= product.name %>
    </div>
  <% end %>
</div>
```

ERB kullanarak, bu kod `@products` `ActiveRecord::Relation` nesnesindeki her ürün boyunca döngü gerçekleştirir ve ürün adını içeren bir `<div>` etiketi render eder.

Bu sefer yeni bir ERB etiketi de kullandık. `<% %>` Ruby kodunu değerlendirir ancak dönüş değerini çıktı olarak vermez. Bu, HTML'imizde istemediğimiz bir array çıktısı verecek olan `@products.each`'in çıktısını yok sayar.

### CRUD Eylemleri

Birbirinden ayrı ürünlere erişebilmemiz gerekiyor. Bu, bir kaynağı okumak için CRUD'daki R'dir.

`resources :products` route'umuzla bireysel ürünler için route'u zaten tanımladık. Bu, `products#show`'a işaret eden `/products/:id` route'unu oluşturur.

Şimdi bu action'ı `ProductsController`'a eklememiz ve çağrıldığında ne olacağını tanımlamamız gerekiyor.

### Ürünleri Bireysel Olarak Gösterme

Products Controller'ı açın ve `show` action'ını şu şekilde ekleyin:

```ruby
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end

  def show
    @product = Product.find(params[:id])
  end
end
```

Buradaki `show` action'ı *tekil* `@product` tanımlar çünkü veritabanından tek bir kayıt yüklüyor, yani başka bir deyişle söylemek gerekirse: Bu tek bir ürünü göster, demek. `index`'te çoğul `@products` kullanırız çünkü birden fazla ürün yüklüyoruz.

Veritabanını sorgulamak için istek parametrelerine erişmek amacıyla `params` kullanırız. Bu durumda, `/products/:id` route'umuzdaki `:id`'yi kullanıyoruz. `/products/1` adresini ziyaret ettiğimizde, params hash'i `{id: 1}` içerir ve bu da `show` action'ımızın veritabanından ID'si `1` olan Product'ı yüklemek için `Product.find(1)` çağırmasıyla sonuçlanır.

Sırada show action'ı için bir view'e ihtiyacımız var. Rails isimlendirme kurallarını takip ederek, `ProductsController`, `app/views` içindeki `products` alt klasöründe view'leri bekler.

`show` action'ı `app/views/products/show.html.erb`de bir dosya bekler. Bu dosyayı editörümüzde oluşturalım ve aşağıdaki içeriği ekleyelim:

```erb
<h1><%= @product.name %></h1>

<%= link_to "Back", products_path %>
```

Index sayfasının her ürün için show sayfasına link vermesi faydalı olur, böylece yönlendirme için onlara tıklayabiliriz. `app/views/products/index.html.erb` view'i güncelleyerek `show` action'ının path'i için bir anchor etiketi kullanarak bu yeni sayfaya link verebiliriz.

```erb#6,8
<h1>Products</h1>

<div id="products">
  <% @products.each do |product| %>
    <div>
      <a href="/products/<%= product.id %>">
        <%= product.name %>
      </a>
    </div>
  <% end %>
</div>
```

Bu sayfayı tarayıcınızda yenileyin. Çalıştığını göreceksiniz, ancak daha iyisini yapabiliriz.

Rails, path'ler ve URL'ler oluşturmak için helper metotları sağlar. `bin/rails routes` komutunu çalıştırdığınızda Prefix sütununu göreceksiniz. Bu prefix, Ruby kodu ile URL oluşturmak için kullanabileceğiniz helper'larla eşleşir.

```
                                  Prefix Verb   URI Pattern                                                                                       Controller#Action
                                products GET    /products(.:format)                                                                               products#index
                                 product GET    /products/:id(.:format)                                                                           products#show
```

Bu route prefix'leri bize aşağıdaki gibi helper'lar verir:

* `products_path` `"/products"` oluşturur
* `products_url` `"http://localhost:3000/products"` oluşturur
* `product_path(1)` `"/products/1"` oluşturur
* `product_url(1)` `"http://localhost:3000/products/1"` oluşturur

`_path` tarayıcının mevcut domain için olduğunu anladığı göreceli (relative) bir path döndürür.

`_url` protokol, host ve port dahil tam bir URL döndürür.

URL helper'ları tarayıcı dışında görüntülenecek e-postaları render etmek için faydalıdır.

`link_to` helper'ıyla birleştirildiğinde, anchor etiketleri oluşturabilir ve bunu Ruby'de temiz bir şekilde yapmak için URL helper'ını kullanabiliriz. `link_to` link (`product.name`) için görüntülecek içeriği  ve `href` özelliği için link verilecek path veya URL'i (`product`) kabul eder.

Bu helper'ları kullanmak için refactor edelim:

```erb#6
<h1>Products</h1>

<div id="products">
  <% @products.each do |product| %>
    <div>
      <%= link_to product.name, product_path(product.id) %>
    </div>
  <% end %>
</div>
```

### Ürün Oluşturma

Şimdiye kadar ürünleri Rails konsolda oluşturmak zorunda kaldık, ancak bunu tarayıcıda çalışır hale getirelim.

Create için iki action oluşturmamız gerekiyor:

1. Ürün bilgilerini toplamak için yeni ürün formu
2. Ürünü kaydetmek ve hataları kontrol etmek için controller'da create action'ı

Controller action'larımızla başlayalım.

```ruby
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end

  def show
    @product = Product.find(params[:id])
  end

  def new
    @product = Product.new
  end
end
```

`new` action'ı form alanlarını görüntülemek için kullanacağımız yeni bir `Product` örneği oluşturur.

`app/views/products/index.html.erb` dosyasını new action'ına link verecek şekilde güncelleyebiliriz.

```erb#3
<h1>Products</h1>

<%= link_to "New product", new_product_path %>

<div id="products">
  <% @products.each do |product| %>
    <div>
      <%= link_to product.name, product_path(product.id) %>
    </div>
  <% end %>
</div>
```

Bu yeni `Product` için formu render etmek amacıyla `app/views/products/new.html.erb` dosyasını oluşturalım.

```erb
<h1>New product</h1>

<%= form_with model: @product do |form| %>
  <div>
    <%= form.label :name %>
    <%= form.text_field :name %>
  </div>

  <div>
    <%= form.submit %>
  </div>
<% end %>
```

Bu view'de ürün oluşturmak için HTML formu oluşturmak amacıyla Rails `form_with` helper'ını kullanıyoruz. Bu helper; CSRF token'lar, sağlanan `model:` temelinde URL oluşturma ve hatta submit butonu text'ini modele göre uyarlama gibi şeyleri işlemek için bir *form builder* kullanır.

Bu sayfayı tarayıcınızda açıp View Source yaparsanız, formun HTML'i şöyle görünecektir:

```html
<form action="/products" accept-charset="UTF-8" method="post">
  <input type="hidden" name="authenticity_token" value="UHQSKXCaFqy_aoK760zpSMUPy6TMnsLNgbPMABwN1zpW-Jx6k-2mISiF0ulZOINmfxPdg5xMyZqdxSW1UK-H-Q" autocomplete="off">

  <div>
    <label for="product_name">Name</label>
    <input type="text" name="product[name]" id="product_name">
  </div>

  <div>
    <input type="submit" name="commit" value="Create Product" data-disable-with="Create Product">
  </div>
</form>
```

Form builder güvenlik için bir CSRF token eklemiş, formu UTF-8 desteği için yapılandırmış, input field isimlerini ayarlamış ve hatta submit butonu için disabled durumu eklemiştir.

Form builder'a yeni bir `Product` örneği geçirdiğimiz için, otomatik olarak yeni bir kayıt oluşturmak için varsayılan route olan `/products`'a `POST` isteği gönderecek şekilde yapılandırılmış bir form oluşturdu.

Bunu işlemek için öncelikle controller'ımızda `create` action'ını uygulamamız gerekiyor.

```ruby#14-26
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end

  def show
    @product = Product.find(params[:id])
  end

  def new
    @product = Product.new
  end

  def create
    @product = Product.new(product_params)
    if @product.save
      redirect_to @product
    else
      render :new, status: :unprocessable_entity
    end
  end

  private
    def product_params
      params.expect(product: [ :name ])
    end
end
```

#### Güçlü Parametreler

`create` action'ı form tarafından gönderilen veriyi işler, ancak güvenlik için filtrelenmesi gerekir. İşte `product_params` metodunun devreye girdiği yer burasıdır.

`product_params`'ta Rails'e, params'ı incelemesini ve değer olarak bir parametre array'i ile `:product` adında bir anahtar (key) olduğundan emin olmasını söylüyoruz. Products için izin verilen tek parametre `:name`'dir ve Rails diğer tüm parametreleri yok sayacaktır. Bu, uygulamamızı hack'lemeye çalışabilecek kötü niyetli kullanıcılardan korur.

#### Hata Yönetimi

Bu params'ları yeni `Product`'a atadıktan sonra, onu veritabanına kaydetmeyi deneyebiliriz. `@product.save` Active Record'a doğrulamaları çalıştırmasını ve kaydı veritabanına kaydetmesini söyler.

`save` başarılı olursa, yeni ürüne yönlendirmek istiyoruz. `redirect_to`'ya bir Active Record nesnesi verildiğinde, Rails o kaydın show action'ı için bir path oluşturur.

```ruby
redirect_to @product
```

`@product` bir `Product` örneği olduğu için, Rails model adını çoğullaştırır ve yönlendirme yapmak için `"/products/2"` üretmek amacıyla nesnenin ID'sini path'e dahil eder.

`save` başarısız olduğunda ve kayıt geçerli olmadığında, kullanıcının geçersiz veriyi düzeltebilmesi için formu yeniden render etmek istiyoruz. `else` ile Rails'e `render :new` geçiyoruz. Rails `Products` controller'ında olduğumuzu bilir, bu yüzden `app/views/products/new.html.erb` dosyasını render etmelidir. `create`'te `@product` değişkenini ayarladığımız için, o şablonu render edebiliriz ve form veritabanına kaydedilememiş olsa bile `Product` verimizle doldurulacaktır.

Ayrıca tarayıcıya bu POST isteğinin başarısız olduğunu ve buna göre işlem yapması gerektiğini söylemek için HTTP durumunu 422 Unprocessable Entity (İşlenemeyen Varlık) olarak ayarlıyoruz.

### Ürünleri Düzenleme

Kayıtları düzenleme süreci kayıt oluşturmaya çok benzer. `new` ve `create` action'ları yerine `edit` ve `update` ile bunları yapacağız.

Bunları controller'da aşağıdaki şekilde uygulayalım:

```ruby#23-34
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end

  def show
    @product = Product.find(params[:id])
  end

  def new
    @product = Product.new
  end

  def create
    @product = Product.new(product_params)
    if @product.save
      redirect_to @product
    else
      render :new, status: :unprocessable_entity
    end
  end

  def edit
    @product = Product.find(params[:id])
  end

  def update
    @product = Product.find(params[:id])
    if @product.update(product_params)
      redirect_to @product
    else
      render :edit, status: :unprocessable_entity
    end
  end

  private
    def product_params
      params.expect(product: [ :name ])
    end
end
```

Daha sonra `app/views/products/show.html.erb` dosyasına bir "Edit" linki ekleyebiliriz:

```erb#4
<h1><%= @product.name %></h1>

<%= link_to "Back", products_path %>
<%= link_to "Edit", edit_product_path(@product) %>
```

#### Before Action'lar

`edit` ve `update` işlemleri `show` gibi mevcut bir veritabanı kaydı gerektirdiğinden bunu `before_action` içine kopyalayabiliriz.

Bir `before_action`, action'lar arasında paylaşılan kodu çekmenize ve action'dan *önce* çalıştırmanıza olanak tanır. Yukarıdaki controller kodunda `@product = Product.find(params[:id])` üç farklı metotta tanımlanmıştır. Bu sorguyu `set_product` adlı bir before action'a çıkarmak her action için kodumuzu temizler.

Bu, DRY (Don't Repeat Yourself - Kendini Tekrar Etme) felsefesinin çalışan güzel bir örneğidir.

```ruby#2,8-9,24-25,27-33,36-38
class ProductsController < ApplicationController
  before_action :set_product, only: %i[ show edit update ]

  def index
    @products = Product.all
  end

  def show
  end

  def new
    @product = Product.new
  end

  def create
    @product = Product.new(product_params)
    if @product.save
      redirect_to @product
    else
      render :new, status: :unprocessable_entity
    end
  end

  def edit
  end

  def update
    if @product.update(product_params)
      redirect_to @product
    else
      render :edit, status: :unprocessable_entity
    end
  end

  private
    def set_product
      @product = Product.find(params[:id])
    end

    def product_params
      params.expect(product: [ :name ])
    end
end
```

#### Partial Çıkarma

Yeni ürün oluşturmak için zaten bir form yazdık. Bunu edit ve update için yeniden kullanabilsek güzel olmaz mıydı? Bir view'i birden fazla yerde yeniden kullanmanıza olanak tanıyan "partials" adlı bir özellik kullanarak bunu yapabiliriz.

Formu `app/views/products/_form.html.erb` adlı bir dosyaya taşıyabiliriz. Dosya adı bunun bir partial olduğunu belirtmek için alt çizgi ile başlar.

Ayrıca herhangi bir instance variable'ı, partial'ı render ettiğimizde tanımlayabileceğimiz bir local variable (yerel değişken) ile değiştirmek istiyoruz. Bunu `@product`'ı `product` ile değiştirerek yapacağız.

```erb#1
<%= form_with model: product do |form| %>
  <div>
    <%= form.label :name %>
    <%= form.text_field :name %>
  </div>

  <div>
    <%= form.submit %>
  </div>
<% end %>
```

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
    Local variable'lar kullanmak partial'ların aynı sayfada her seferinde farklı bir değerle birden fazla kez yeniden kullanılmasına olanak tanır. Bu, index sayfası gibi öğe listelerini render ederken işe yarar.
  </div>
</div>

Bu partial'ı `app/views/products/new.html.erb` view'inde kullanmak için formu bir render çağrısıyla değiştirebiliriz:

```erb#3
<h1>New product</h1>

<%= render "form", product: @product %>
<%= link_to "Cancel", products_path %>
```

Form partial sayesinde edit view'i  neredeyse tamamen aynı şey oluyor. `app/views/products/edit.html.erb` dosyasını aşağıdakilerle oluşturalım:

```erb#3
<h1>Edit product</h1>

<%= render "form", product: @product %>
<%= link_to "Cancel", @product %>
```

View partial'ları hakkında daha fazla bilgi edinmek için [Action View Kılavuzu](../action_view_overview/)'na göz atın.

### Ürünleri Silme

Uygulamamız gereken son özellik ise ürünleri silmek. `DELETE /products/:id` isteklerini işlemek için `ProductsController`'ımıza bir `destroy` action'ı ekleyeceğiz.

`before_action :set_product`'a `destroy` eklemek, `@product` instance variable'ını diğer action'lar için yaptığımız şekilde ayarlamamızı sağlar.

```ruby#2,35-38
class ProductsController < ApplicationController
  before_action :set_product, only: %i[ show edit update destroy ]

  def index
    @products = Product.all
  end

  def show
  end

  def new
    @product = Product.new
  end

  def create
    @product = Product.new(product_params)
    if @product.save
      redirect_to @product
    else
      render :new, status: :unprocessable_entity
    end
  end

  def edit
  end

  def update
    if @product.update(product_params)
      redirect_to @product
    else
      render :edit, status: :unprocessable_entity
    end
  end

  def destroy
    @product.destroy
    redirect_to products_path
  end

  private
    def set_product
      @product = Product.find(params[:id])
    end

    def product_params
      params.expect(product: [ :name ])
    end
end
```

Bunun çalışması için, `app/views/products/show.html.erb` dosyasına bir "Delete" butonu eklememiz gerekiyor:

```erb#5
<h1><%= @product.name %></h1>

<%= link_to "Back", products_path %>
<%= link_to "Edit", edit_product_path(@product) %>
<%= button_to "Delete", @product, method: :delete, data: { turbo_confirm: "Are you sure?" } %>
```

`button_to`, içinde "Delete" yazılı bir buton bulunan bir form oluşturur. Bu butona tıklandığında, controller'ımızdaki `destroy` action'ını tetikleyen `/products/:id` adresine `DELETE` isteği oluşturan formu gönderir.

`turbo_confirm` data özelliği, Turbo JavaScript kütüphanesine formu göndermeden önce kullanıcıdan onay istemesini söyler. Bunu kısa bir süre sonra daha ayrıntılı şekilde inceleyeceğiz.

Kimlik Doğrulama Ekleme
-----------------------

Herkes ürünleri düzenleyebilir veya silebilir, ki bu güvenli değil. Ürünleri yönetmek için bir kullanıcının kimlik doğrulaması (authentication) yapmış olmasını gerektirerek biraz güvenlik ekleyelim.

Rails, kullanabileceğimiz bir authentication generator ile birlikte gelir. User ve Session modelleri ile uygulamamıza giriş yapmak için gerekli controller'ları ve view'leri oluşturur.

Terminalinize geri dönün ve aşağıdaki komutu çalıştırın:

```bash
$ bin/rails generate authentication
```

Ardından veritabanına User ve Session tablolarını eklemek için migrate ile değişiklikleri uygulayın.

```bash
$ bin/rails db:migrate
```

Bir User oluşturmak için Rails konsolunu açın.

```bash
$ bin/rails console
```

Rails konsolda User oluşturmak için `User.create!` metodunu kullanın. Örnek yerine kendi e-posta ve şifrenizi kullanmaktan çekinmeyin.

```irb
store(dev)> User.create! email_address: "you@example.org", password: "s3cr3t", password_confirmation: "s3cr3t"
```

Generator tarafından eklenen `bcrypt` gem'ini alması için Rails sunucunuzu yeniden başlatın. BCrypt, kimlik doğrulaması için şifreleri güvenli bir şekilde hash'lemek için kullanılır.

```bash
$ bin/rails server
```

Herhangi bir sayfayı ziyaret ettiğinizde, Rails kullanıcı adı ve şifre soracaktır. User kaydını oluştururken kullandığınız e-posta ve şifreyi girin.

http://localhost:3000/products/new adresini ziyaret ederek deneyin.

Doğru kullanıcı adı ve şifreyi girerseniz, geçiş yapmanıza izin verecektir. Tarayıcınız ayrıca bu kimlik bilgilerini gelecekteki istekler için saklayacaktır, böylece her sayfa görüntülemesinde yazmanız gerekmez.

### Log Out Ekleme

Uygulamadan çıkış yapmak için `app/views/layouts/application.html.erb` dosyasının üstüne bir buton ekleyebiliriz. Bu layout, header veya footer gibi her sayfaya dahil etmek istediğiniz HTML'i koyduğunuz yerdir.

`<body>` içine "Home"a bir link ve "Log out" butonu ile küçük bir `<nav>` bölümü ekleyin ve `yield`'i `<main>` etiketi ile sarın.

```erb#5-8,10,12
<!DOCTYPE html>
<html>
  <!-- ... -->
  <body>
    <nav>
      <%= link_to "Home", root_path %>
      <%= button_to "Log out", session_path, method: :delete if authenticated? %>
    </nav>

    <main>
      <%= yield %>
    </main>
  </body>
</html>
```

Bu, yalnızca kullanıcı kimliği doğrulanmışsa Log out butonunu görüntüleyecektir. Tıklandığında, kullanıcının çıkış yapmasını sağlayacak olan session path'ine DELETE isteği gönderecektir.

### Doğrulanmamış Erişime İzin Verme

Ancak store uygulamamızın product index ve show sayfaları herkese erişilebilir olmalıdır. Varsayılan olarak, Rails authentication generator tüm sayfaları yalnızca kimliği doğrulanan kullanıcılarla sınırlandıracaktır.

Misafirlerin ürünleri görüntülemesine izin vermek için controller'ımızda doğrulanmamış erişime (unauthenticated access) izin verebiliriz.

```ruby#2
class ProductsController < ApplicationController
  allow_unauthenticated_access only: %i[ index show ]
  # ...
end
```

Çıkış yapın ve products index ve show sayfalarını ziyaret ederek kimlik doğrulaması olmadan erişilebilir olduklarını görün.

### Linkleri Yalnızca Doğrulanmış Kullanıcılar için Gösterme

Yalnızca giriş yapmış kullanıcılar ürün oluşturabileceği için, `app/views/products/index.html.erb` view'ini yalnızca kullanıcı doğrulanmış ise yeni ürün linkini görüntüleyecek şekilde değiştirebiliriz.

```erb
<%= link_to "New product", new_product_path if authenticated? %>
```

Log out butonuna tıklayınca New linkinin gizlendiğini göreceksiniz. http://localhost:3000/session/new adresinde yeniden giriş yaptıktan sonra index sayfasında New linkini göreceksiniz.

İsteğe bağlı olarak, kullanıcı doğrulaması yapılmamışsa "Login" linki eklemek için navbar'a bu route için bir link ekleyebilirsiniz.

```erb
<%= link_to "Login", new_session_path unless authenticated? %>
```

Ayrıca `app/views/products/show.html.erb` view'indeki "Edit" ve "Delete" linklerini yalnızca doğrulama yapılmışsa görüntülenecek şekilde güncelleyebilirsiniz.

```erb#4,7
<h1><%= @product.name %></h1>

<%= link_to "Back", products_path %>
<% if authenticated? %>
  <%= link_to "Edit", edit_product_path(@product) %>
  <%= button_to "Delete", @product, method: :delete, data: { turbo_confirm: "Are you sure?" } %>
<% end %>
```

Ürünleri Önbelleğe Alma
-----------------------

Bazen bir sayfanın belirli bölümlerini önbelleğe almak (cache) performansı artırabilir. Rails bu süreci, varsayılan olarak gelen veritabanı destekli bir cache store olan Solid Cache ile basitleştirir.

`cache` metodunu kullanarak HTML'i önbellekte saklayabiliriz. `app/views/products/show.html.erb` dosyasındaki header'ı cache'leyelim.

```erb#1,3
<% cache @product do %>
  <h1><%= @product.name %></h1>
<% end %>
```

`cache`'e `@product` geçirerek Rails, ürün için benzersiz bir cache anahtarı oluşturur. Active Record nesneleri `"products/1"` gibi bir String döndüren bir `cache_key` metoduna sahiptir. View'lerdeki `cache` helper'ı, bunu template digest (şablon özeti) ile birleştirerek bu HTML için benzersiz bir anahtar oluşturur.

Geliştirme sırasında önbelleğe almayı etkinleştirmek için terminalinizde aşağıdaki komutu çalıştırın.

```bash
$ bin/rails dev:cache
```

Bir ürünün show action'ını ziyaret ettiğinizde (`/products/2` gibi), Rails sunucu loglarınızda yeni cache satırlarını göreceksiniz:

```bash
Read fragment views/products/show:a5a585f985894cd27c8b3d49bb81de3a/products/1-20240918154439539125 (1.6ms)
Write fragment views/products/show:a5a585f985894cd27c8b3d49bb81de3a/products/1-20240918154439539125 (4.0ms)
```

Bu sayfayı ilk kez açtığımızda, Rails bir cache anahtarı oluşturacak ve cache store'da var olup olmadığını soracaktır. Bu `Read fragment` satırıdır.

Bu, sayfanın ilk görüntülenmesi olduğu için cache mevcut değildir, bu yüzden HTML oluşturulur ve önbelleğe yazılır. Bunu loglardaki `Write fragment` satırı olarak görebiliriz.

Sayfayı yeniledikten sonra logların artık `Write fragment` içermediğini göreceksiniz.

```bash
Read fragment views/products/show:a5a585f985894cd27c8b3d49bb81de3a/products/1-20240918154439539125 (1.3ms)
```

Cache girişi son istek tarafından yazıldığı için Rails, ikinci istekte cache girişini bulur. Rails ayrıca, kayıtlar güncellendiğinde cache anahtarını değiştirir, böylece asla eski cache verisini render etmediğinden emin olur.

[Rails ile Önbelleğe Alma](../caching_with_rails/) kılavuzunda daha fazla bilgi edinin.

Action Text ile Zengin Metin Alanları
-------------------------------------

Birçok uygulama, gömülü öğeler (yani multimedya öğeleri) içeren zengin metinlere (rich text) ihtiyaç duyar ve Rails, Action Text ile bu işlevselliği kullanıma hazır olarak sunar.

Action Text'i kullanmak için önce indirin:

```bash
$ bin/rails action_text:install
$ bundle install
$ bin/rails db:migrate
```

Tüm yeni özeliklerin yüklendiğinden emin olmak için Rails sunucunuzu yeniden başlatın.

Şimdi, ürünümüze rich text açıklama (description) alanı ekleyelim.

İlk olarak, `Product` modeline aşağıdakini ekleyin:

```ruby#2
class Product < ApplicationRecord
  has_rich_text :description
  validates :name, presence: true
end
```

`app/views/products/_form.html.erb` içindeki submit butonundan önce açıklamayı düzenlemek için zengin metin alanı içerecek şekilde form artık güncellenebilir.

```erb#4-7
<%= form_with model: product do |form| %>
  <%# ... %>
  <div>
    <%= form.label :description, style: "display: block" %>
    <%= form.rich_textarea :description %>
  </div>
  <div>
    <%= form.submit %>
  </div>
<% end %>
```

Controller'ımızın da form gönderildiğinde bu yeni parametreye izin vermesi gerekiyor, bu yüzden `app/controllers/products_controller.rb` içinde izin verilen parametreleri description'ı içerecek şekilde güncelleyeceğiz:

```ruby#3
    # Sadece güvenilir parametrelerin listesine izin ver.
    def product_params
      params.expect(product: [ :name, :description ])
    end
```

Ayrıca show view'ini de `app/views/products/show.html.erb` içindeki açıklamayı görüntüleyecek şekilde güncellememiz gerekiyor:

```erb#3
<% cache @product do %>
  <h1><%= @product.name %></h1>
  <%= @product.description %>
<% end %>
```

Rails tarafından oluşturulan cache anahtarı, view değiştirildiğinde de değişir. Bu, cache'in view şablonunun en son sürümüyle senkronize kalmasını sağlar.

Yeni bir ürün oluşturun ve ona kalın ve italik metin içeren bir açıklama ekleyin. Show sayfasının biçimlendirilmiş metni gösterdiğini ve ürünü düzenlerken metin alanında bu zengin metni koruduğunu göreceksiniz.

Daha fazla bilgi edinmek için [Action Text](../action_text_overview/) sayfasına göz atın.

Active Storage ile Dosya Yükleme
--------------------------------

Action Text, Rails'in dosya yüklemeyi kolaylaştıran Active Storage adlı bir başka özelliği üzerine inşa edilmiştir.

Bir ürünü düzenlemeyi ve bir görseli zengin metin editörüne sürüklemeyi deneyin, ardından kaydı güncelleyin. Rails'in bu görseli yüklediğini ve zengin metin editörü içinde işlediğini göreceksiniz. Harika, değil mi?!

Active Storage'ı doğrudan da kullanabiliriz. `Product` modeline öne çıkan bir görsel ekleyelim.

```ruby#2
class Product < ApplicationRecord
  has_one_attached :featured_image
  has_rich_text :description
  validates :name, presence: true
end
```

Ardından, submit butonundan önce product formumuza bir dosya yükleme alanı ekleyebiliriz:

```erb#4-7
<%= form_with model: product do |form| %>
  <%# ... %>
  <div>
    <%= form.label :featured_image, style: "display: block" %>
    <%= form.file_field :featured_image, accept: "image/*" %>
  </div>
  <div>
    <%= form.submit %>
  </div>
<% end %>
```

`app/controllers/products_controller.rb` içinde `:featured_image`'i izin verilen parametre olarak ekleyin:

```ruby#3
    # Sadece güvenilir parametrelerin listesine izin ver.
    def product_params
      params.expect(product: [ :name, :description, :featured_image ])
    end
```

Son olarak, `app/views/products/show.html.erb` içinde ürünümüz için öne çıkan görseli görüntülemek istiyoruz. Aşağıdakini en üste ekleyin.

```erb
<%= image_tag @product.featured_image if @product.featured_image.attached? %>
```

Bir ürün için görsel yüklemeyi deneyin. Kaydettikten sonra show sayfasında görselin görüntülendiğini göreceksiniz.

Daha fazla ayrıntı için [Active Storage](../active_storage_overview/) sayfasına göz atın.

Uluslararasılaştırma (I18n)
---------------------------

Rails, uygulamanızı diğer dillere çevirmeyi kolaylaştırır.

View'lerimizdeki `translate` veya `t` helper'ı, isme göre bir çeviri arar ve mevcut locale için metni döndürür.

`app/views/products/index.html.erb` dosyasında, header etiketini bir çeviri kullanacak şekilde güncelleyelim.

```erb
<h1><%= t "hello" %></h1>
```

Sayfayı yenilediğimizde, header metni olarak `Hello world` görüyoruz. Peki bu nereden geldi?

Varsayılan dil İngilizce olduğu için Rails, locale altında eşleşen bir anahtar için `config/locales/en.yml` (`rails new` sırasında oluşturulan bir dosya) dosyasına bakar.

```yaml
en:
  hello: "Hello world"
```

Editörümüzde Türkçe için yeni bir locale dosyası oluşturalım ve `config/locales/tr.yml` dosyasına bir çeviri ekleyelim.

```yaml
tr:
  hello: "Merhaba dünya"
```

Rails'e hangi locale'i kullanacağını söylememiz gerekiyor. En basit seçenek URL'de bir locale parametresi aramaktır. Bunu `app/controllers/application_controller.rb` dosyasında aşağıdaki şekilde yapabiliriz:

```ruby#4,6-9
class ApplicationController < ActionController::Base
  # ...

  around_action :switch_locale

  def switch_locale(&action)
    locale = params[:locale] || I18n.default_locale
    I18n.with_locale(locale, &action)
  end
end
```

Bu, her istekte çalışacak ve parametrelerde `locale` arayacak veya varsayılan locale'e geri dönecektir. İstek için locale'i ayarlar ve tamamlandıktan sonra sıfırlar.

* http://localhost:3000/products?locale=en adresini ziyaret edin, İngilizce çeviriyi göreceksiniz.
* http://localhost:3000/products?locale=tr adresini ziyaret edin, Türkçe çeviriyi göreceksiniz.
* http://localhost:3000/products adresini locale parametresi olmadan ziyaret edin, İngilizce'ye geri dönecektir.

Index header'ını `"Hello world"` yerine gerçek bir çeviri kullanacak şekilde güncelleyelim.

```erb
<h1><%= t ".title" %></h1>
```

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
    <code>title</code>'ın önündeki <code>.</code>'yı fark ettiniz mi? Bu, Rails'e göreceli (relative) locale araması kullanmasını söyler. Göreceli aramalar, controller ve action'ı otomatik olarak anahtara dahil eder, böylece onları her seferinde yazmanıza gerek kalmaz. <code>.title</code> için İngilizce locale ile  <code>en.products.index.title</code> araması yapacaktır.
  </div>
</div>

`config/locales/en.yml` dosyasında controller, view ve çeviri adımızla eşleşecek şekilde `products` ve `index` altına `title` anahtarını eklemek istiyoruz.

```yaml
en:
  hello: "Hello world"
  products:
    index:
      title: "Products"
```

Türkçe locales dosyasında da aynı şeyi yapabiliriz:

```yaml
tr:
  hello: "Merhaba dünya"
  products:
    index:
      title: "Ürünler"
```

Artık İngilizce locale'i görüntülerken "Products", Türkçe locale'i görüntülerken "Ürünler" göreceksiniz.

[Rails Uluslararasılaştırma (I18n) API](../i18n/) hakkında daha fazla bilgi edinin.

Stok Bildirimleri Ekleme
------------------------

E-ticaret mağazalarının yaygın bir özelliği, bir ürün tekrar stokta olduğunda bildirim almak için e-posta aboneliğidir. Rails'in temellerini gördüğümüze göre, bu özelliği mağazamıza ekleyelim.

### Temel Envanter Takibi

İlk olarak, stoğu takip edebilmek için Product modeline bir envanter sayısı ekleyelim. Bu migration'ı aşağıdaki komutla oluşturabiliriz:

```bash
$ bin/rails generate migration AddInventoryCountToProducts inventory_count:integer
```

Ardından migration'ı çalıştıralım.

```bash
$ bin/rails db:migrate
```

`app/views/products/_form.html.erb` dosyasındaki product formuna envanter sayısını eklememiz gerekecek.

```erb#4-7
<%= form_with model: product do |form| %>
  <%# ... %>

  <div>
    <%= form.label :inventory_count, style: "display: block" %>
    <%= form.number_field :inventory_count %>
  </div>

  <div>
    <%= form.submit %>
  </div>
<% end %>
```

Ayrıca controller'da  izin verilen parametrelere `:inventory_count`'un eklenmesi gerekiyor.

```ruby#2
    def product_params
      params.expect(product: [ :name, :description, :featured_image, :inventory_count ])
    end
```

Envanter sayımızın asla negatif bir sayı olmamasını doğrulamak da yararlı olacaktır, bu yüzden modelimize bunun için bir validation da ekleyelim.

```ruby#6
class Product < ApplicationRecord
  has_one_attached :featured_image
  has_rich_text :description

  validates :name, presence: true
  validates :inventory_count, numericality: { greater_than_or_equal_to: 0 }
end
```

Bu değişikliklerle artık mağazamızdaki ürünlerin envanter sayısını güncelleyebiliriz.

### Ürünlere Abone Ekleme

Kullanıcılara bir ürünün tekrar stokta olduğunu bildirmek için, bu aboneleri takip etmemiz gerekiyor.

Bu e-posta adreslerini saklamak ve bunları ilgili ürünle ilişkilendirmek için Subscriber adlı bir model oluşturalım.

```bash
$ bin/rails generate model Subscriber product:belongs_to email
```

Ardından yeni migration'ı çalıştırın:

```bash
$ bin/rails db:migrate
```

Yukarıda `product:belongs_to` ekleyerek Rails'e aboneler ve ürünlerin bire-çok ilişkisi olduğunu söyledik, yani bir Subscriber tek bir Product instance'ına "ait".

Ancak bir Product birçok aboneye sahip olabilir, bu yüzden Product modelimize `has_many :subscribers, dependent: :destroy` ekleyerek bu iki model arasındaki ilişkinin ikinci kısmını ekliyoruz. Bu, Rails'e iki veritabanı tablosu arasındaki sorguları nasıl birleştireceğini söyler.

```ruby#2
class Product < ApplicationRecord
  has_many :subscribers, dependent: :destroy
  has_one_attached :featured_image
  has_rich_text :description

  validates :name, presence: true
  validates :inventory_count, numericality: { greater_than_or_equal_to: 0 }
end
```

Şimdi bu aboneleri oluşturmak için bir controller'a ihtiyacımız var. Bunu `app/controllers/subscribers_controller.rb` dosyasında aşağıdaki kodla oluşturalım:

```ruby
class SubscribersController < ApplicationController
  allow_unauthenticated_access
  before_action :set_product

  def create
    @product.subscribers.where(subscriber_params).first_or_create
    redirect_to @product, notice: "You are now subscribed."
  end

  private

  def set_product
    @product = Product.find(params[:product_id])
  end

  def subscriber_params
    params.expect(subscriber: [ :email ])
  end
end
```

Yönlendirmemiz Rails flash'inde bir notice ayarlar. Flash, bir sonraki sayfada gösterilecek mesajları saklamak için kullanılır.

Flash mesajını görüntülemek için notice'i `app/views/layouts/application.html.erb` dosyasında body içine ekleyelim:

```erb#4
<html>
  <!-- ... -->
  <body>
    <div class="notice"><%= notice %></div>
    <!-- ... -->
  </body>
</html>
```

Kullanıcıların belirli bir ürüne abone olması için, iç içe route kullanacağız. Böylece abonenin hangi ürüne ait olduğunu biliyor olacağız. `config/routes.rb` dosyasında `resources :products`'ı aşağıdaki şekilde değiştirin:

```ruby
  resources :products do
    resources :subscribers, only: [ :create ]
  end
```

Product show sayfasında envanter olup olmadığını kontrol edebilir ve stok miktarını görüntüleyebiliriz. Bunun dışında, tekrar stokta olduğunda bildirim almak için abone olma formuyla birlikte stokta yok mesajı görüntüleyebiliriz.

`app/views/products/_inventory.html.erb` adresinde yeni bir partial oluşturun ve aşağıdakini ekleyin:

```erb
<% if product.inventory_count? %>
  <p><%= product.inventory_count %> in stock</p>
<% else %>
  <p>Out of stock</p>
  <p>Email me when available.</p>

  <%= form_with model: [product, Subscriber.new] do |form| %>
    <%= form.email_field :email, placeholder: "you@example.com", required: true %>
    <%= form.submit "Submit" %>
  <% end %>
<% end %>
```

Ardından `app/views/products/show.html.erb` dosyasını `cache` bloğundan sonra bu partial'ı render edecek şekilde güncelleyin.

```erb
<%= render "inventory", product: @product %>
```

### Stok E-posta Bildirimleri

Action Mailer, Rails'in e-posta göndermenizi sağlayan bir özelliğidir. Bir ürün tekrar stokta olduğunda aboneleri bilgilendirmek için kullanacağız.

Aşağıdaki komutla bir mailer oluşturabiliriz:

```bash
$ bin/rails g mailer Product in_stock
```

Bu, bir `in_stock` metoduyla `app/mailers/product_mailer.rb` adresinde bir sınıf oluşturur.

Bu metodu bir abonenin e-posta adresine posta göndermek için güncelleyin.

```ruby#7-10
class ProductMailer < ApplicationMailer
  # .subject, config/locales/en.yml adresindeki I18n dosyanızda
  # aşağıdaki arama ile ayarlanabilir:
  #
  #   en.product_mailer.in_stock.subject
  #
  def in_stock
    @product = params[:product]
    mail to: params[:subscriber].email
  end
end
```

Mailer generator ayrıca views klasörümüzde iki e-posta şablonu oluşturur: Biri HTML için, diğeri Text için. Bunları ürüne bir mesaj ve bağlantı içerecek şekilde güncelleyebiliriz.

`app/views/product_mailer/in_stock.html.erb` dosyasını şu şekilde değiştirin:

```erb
<h1>Good news!</h1>

<p><%= link_to @product.name, product_url(@product) %> is back in stock.</p>
```

Ve `app/views/product_mailer/in_stock.text.erb` dosyasını da:

```erb
Good news!

<%= @product.name %> is back in stock.
<%= product_url(@product) %>
```

Mailer'larda `product_path` yerine `product_url` kullanıyoruz çünkü e-posta istemcilerinin bağlantıya tıklandığında tarayıcıda açmak için tam URL'i bilmesi gerekiyor.

Rails konsolunu açıp göndereceğimiz bir ürün ve aboneyi yükleyerek bir e-postayı test edebiliriz:

```irb
store(dev)> product = Product.first
store(dev)> subscriber = product.subscribers.find_or_create_by(email: "subscriber@example.org")
store(dev)> ProductMailer.with(product: product, subscriber: subscriber).in_stock.deliver_later
```

Loglarda bir e-posta yazdırdığını göreceksiniz.

```
ProductMailer#in_stock: processed outbound mail in 63.0ms
Delivered mail 66a3a9afd5d4a_108b04a4c41443@local.mail (33.1ms)
Date: Fri, 26 Jul 2024 08:50:39 -0500
From: from@example.com
To: subscriber@example.com
Message-ID: <66a3a9afd5d4a_108b04a4c41443@local.mail>
Subject: In stock
Mime-Version: 1.0
Content-Type: multipart/alternative;
 boundary="--==_mimepart_66a3a9afd235e_108b04a4c4136f";
 charset=UTF-8
Content-Transfer-Encoding: 7bit


----==_mimepart_66a3a9afd235e_108b04a4c4136f
Content-Type: text/plain;
 charset=UTF-8
Content-Transfer-Encoding: 7bit

Good news!

T-Shirt is back in stock.
http://localhost:3000/products/1


----==_mimepart_66a3a9afd235e_108b04a4c4136f
Content-Type: text/html;
 charset=UTF-8
Content-Transfer-Encoding: 7bit

<!-- BEGIN app/views/layouts/mailer.html.erb --><!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <style>
      /* Email styles need to be inline */
    </style>
  </head>

  <body>
    <!-- BEGIN app/views/product_mailer/in_stock.html.erb --><h1>Good news!</h1>

<p><a href="http://localhost:3000/products/1">T-Shirt</a> is back in stock.</p>
<!-- END app/views/product_mailer/in_stock.html.erb -->
  </body>
</html>
<!-- END app/views/layouts/mailer.html.erb -->
----==_mimepart_66a3a9afd235e_108b04a4c4136f--

Performed ActionMailer::MailDeliveryJob (Job ID: 5e2bd5f2-f54f-4088-ace3-3f6eb15aaf46) from Async(default) in 111.34ms
```

Bu e-postaları tetiklemek için Product modelinde envanter sayısı 0'dan pozitif bir sayıya değiştiğinde e-posta göndermek için bir callback kullanabiliriz.

```ruby#9-19
class Product < ApplicationRecord
  has_many :subscribers, dependent: :destroy
  has_one_attached :featured_image
  has_rich_text :description

  validates :name, presence: true
  validates :inventory_count, numericality: { greater_than_or_equal_to: 0 }

  after_update_commit :notify_subscribers, if: :back_in_stock?

  def back_in_stock?
    inventory_count_previously_was.zero? && inventory_count > 0
  end

  def notify_subscribers
    subscribers.each do |subscriber|
      ProductMailer.with(product: self, subscriber: subscriber).in_stock.deliver_later
    end
  end
end
```

`after_update_commit`, değişiklikler veritabanına kaydedildikten sonra tetiklenen bir Active Record callback'idir. `if: :back_in_stock?`, callback'e yalnızca `back_in_stock?` metodu true döndürürse çalışmasını söyler.

Active Record, özniteliklerdeki değişiklikleri takip eder, bu yüzden `back_in_stock?`, `inventory_count_previously_was` kullanarak `inventory_count`'un önceki değerini kontrol eder. Ardından bunu mevcut envanter sayısıyla karşılaştırarak ürünün tekrar stokta olup olmadığını belirleyebiliriz.

`notify_subscribers`, bu belirli ürün için tüm aboneler için `subscribers` tablosunu sorgulamak üzere Active Record ilişkisini kullanır ve ardından her birine gönderilecek `in_stock` e-postasını kuyruğa alır.

### Bir Concern Çıkarma

Product modeli artık bildirimleri işlemek için makul miktarda koda sahip. Kodumuzu daha iyi organize etmek için bunu bir `ActiveSupport::Concern`'e çıkarabiliriz. Bir Concern, bunları kullanmayı kolaylaştırmak için bazı syntactic sugar içeren bir Ruby modülüdür. (Not: Syntactic sugar; bir programlama dilinde belirli işlevleri daha okunabilir ve kullanıcı dostu hale getirmek için eklenen sözdizimi öğeleridir.)

İlk olarak Notifications modülünü oluşturalım.

`app/models/product/notifications.rb` adresinde aşağıdaki içerikle bir dosya oluşturun:

```ruby
module Product::Notifications
  extend ActiveSupport::Concern

  included do
    has_many :subscribers, dependent: :destroy
    after_update_commit :notify_subscribers, if: :back_in_stock?
  end

  def back_in_stock?
    inventory_count_previously_was.zero? && inventory_count > 0
  end

  def notify_subscribers
    subscribers.each do |subscriber|
      ProductMailer.with(product: self, subscriber: subscriber).in_stock.deliver_later
    end
  end
end
```

Bir sınıfa bir modül dahil ettiğinizde, `included` bloğunun içindeki herhangi bir kod o sınıfın parçasıymış gibi çalışır. Aynı zamanda, modülde tanımlanan metotlar o sınıfın nesnelerinde (instance'larında) çağırabileceğiniz normal metotlar haline gelir.

Bildirim tetikleyen kod Notification modülüne çıkarıldığına göre, Product modeli Notifications modülünü dahil edecek şekilde basitleştirilebilir.

```ruby#2
class Product < ApplicationRecord
  include Notifications

  has_one_attached :featured_image
  has_rich_text :description

  validates :name, presence: true
  validates :inventory_count, numericality: { greater_than_or_equal_to: 0 }
end
```

Concern'ler Rails uygulamanızın özelliklerini düzenlemenin harika bir yoludur. Product'a daha fazla özellik ekledikçe, sınıf karışık hale gelecektir. Bunun yerine, aboneleri işleme ve bildirimlerin nasıl gönderildiğine dair tüm işlevselliği içeren `Product::Notifications` gibi bağımsız bir modüle her özelliği çıkarmak için Concern'leri kullanabiliriz.

Kodu concern'lere çıkarmak ayrıca özellikleri yeniden kullanılabilir hale getirmeye yardımcı olur. Örneğin, abone bildirimlerine ihtiyaç duyan yeni bir model tanıtabiliriz. Bu modül aynı işlevselliği sağlamak için birden fazla modelde kullanılabilir.

### Abonelik İptal Bağlantıları

Bir abone bir noktada aboneliğini iptal etmek isteyebilir, bu yüzden bunu sonraki adımda oluşturalım.

Öncelikle, e-postalara dahil edeceğimiz URL'de abonelik iptali için bir route'a ihtiyacımız var.

```ruby
  resource :unsubscribe, only: [ :show ]
```

Active Record'un farklı amaçlar için veritabanı kayıtlarını bulmak üzere benzersiz token'lar oluşturabilen `generates_token_for` adlı bir özelliği vardır. Bunu e-postanın abonelik iptal URL'inde kullanmak üzere benzersiz bir abonelik iptal token'ı oluşturmak için kullanabiliriz.

```ruby#3
class Subscriber < ApplicationRecord
  belongs_to :product
  generates_token_for :unsubscribe
end
```

Controller'ımız önce URL'deki token'dan Subscriber kaydını arayacaktır. Abone bulunduğunda, kaydı yok edecek ve ana sayfaya yönlendirecektir. `app/controllers/unsubscribes_controller.rb` oluşturun ve aşağıdaki kodu ekleyin:

```ruby
class UnsubscribesController < ApplicationController
  allow_unauthenticated_access
  before_action :set_subscriber

  def show
    @subscriber&.destroy
    redirect_to root_path, notice: "Unsubscribed successfully."
  end

  private

  def set_subscriber
    @subscriber = Subscriber.find_by_token_for(:unsubscribe, params[:token])
  end
end
```

Son olarak, e-posta şablonlarımıza abonelik iptal bağlantısını ekleyelim.

`app/views/product_mailer/in_stock.html.erb` dosyasına bir `link_to` ekleyin:

```erb#5
<h1>Good news!</h1>

<p><%= link_to @product.name, product_url(@product) %> is back in stock.</p>

<%= link_to "Unsubscribe", unsubscribe_url(token: params[:subscriber].generate_token_for(:unsubscribe)) %>
```

`app/views/product_mailer/in_stock.text.erb` dosyasına URL'i düz metin olarak ekleyin:

```erb#6
Good news!

<%= @product.name %> is back in stock.
<%= product_url(@product) %>

Unsubscribe: <%= unsubscribe_url(token: params[:subscriber].generate_token_for(:unsubscribe)) %>
```

Abonelik iptal bağlantısına tıklandığında, abone kaydı veritabanından silinecektir. Controller ayrıca geçersiz veya süresi dolmuş token'ları herhangi bir hata vermeden güvenli bir şekilde işler.

Başka bir e-posta göndermek ve loglardaki abonelik iptal bağlantısını test etmek için Rails konsolunu kullanın.

CSS ve JavaScript Ekleme
------------------------

CSS ve JavaScript, web uygulamaları geliştirmenin temelini oluşturur, bu yüzden bunları Rails ile nasıl kullanacağımızı öğrenelim.

### Propshaft

Rails'in asset pipline'ı Propshaft olarak adlandırılır. CSS, JavaScript, görseller ve diğer asset'leri alır ve tarayıcınıza sunar. Üretim ortamında, Propshaft sayfalarınızı daha hızlı hale getirmek için önbelleklenebilmeleri adına asset'lerinizin her sürümünü takip eder. Bunun nasıl çalıştığı hakkında daha fazla bilgi edinmek için [Asset Pipeline](../asset_pipeline/) kılavuzuna göz atın.

`app/assets/stylesheets/application.css` dosyasını değiştirip fontmuzu sans-serif olarak değiştirelim.

```css
body {
  font-family: Arial, Helvetica, sans-serif;
  padding: 1rem;
}

nav {
  justify-content: flex-end;
  display: flex;
  font-size: 0.875em;
  gap: 0.5rem;
  max-width: 1024px;
  margin: 0 auto;
  padding: 1rem;
}

nav a {
  display: inline-block;
}

main {
  max-width: 1024px;
  margin: 0 auto;
}

.notice {
  color: green;
}

section.product {
  display: flex;
  gap: 1rem;
  flex-direction: row;
}

section.product img {
  border-radius: 8px;
  flex-basis: 50%;
  max-width: 50%;
}
```

Ardından bu yeni stilleri kullanmak için `app/views/products/show.html.erb` dosyasını güncelleyeceğiz.

```erb#1,3,6,18-19
<p><%= link_to "Back", products_path %></p>

<section class="product">
  <%= image_tag @product.featured_image if @product.featured_image.attached? %>

  <section class="product-info">
    <% cache @product do %>
      <h1><%= @product.name %></h1>
      <%= @product.description %>
    <% end %>

    <%= render "inventory", product: @product %>

    <% if authenticated? %>
      <%= link_to "Edit", edit_product_path(@product) %>
      <%= button_to "Delete", @product, method: :delete, data: { turbo_confirm: "Are you sure?" } %>
    <% end %>
  </section>
</section>
```

Sayfanızı yenileyin. CSS'in uygulandığını göreceksiniz.

### Import Map'ler

Rails varsayılan olarak JavaScript için import haritalarını kullanır. Bu, hiçbir derleme adımı olmadan modern JavaScript modülleri yazmanıza olanak tanır.

JavaScript pinlerini `config/importmap.rb` dosyasında bulabilirsiniz. Bu dosya, tarayıcıda importmap etiketini oluşturmak için kullanılan JavaScript paket adlarını kaynak dosyayla eşleştirir.

```ruby
# ./bin/importmap çalıştırarak npm paketlerini pinleyin

pin "application"
pin "@hotwired/turbo-rails", to: "turbo.min.js"
pin "@hotwired/stimulus", to: "stimulus.min.js"
pin "@hotwired/stimulus-loading", to: "stimulus-loading.js"
pin_all_from "app/javascript/controllers", under: "controllers"
pin "trix"
pin "@rails/actiontext", to: "actiontext.esm.js"
```

<div class="guide-alert guide-alert-info">
  <div class="guide-alert-icon">💡</div>
  <div class="guide-alert-content">
    Her pin, bir JavaScript paket adını (örn. <code>"@hotwired/turbo-rails"</code>) belirli bir dosya veya URL'ye (örn. <code>"turbo.min.js"</code>) eşleştirir. <code>pin_all_from</code> bir dizindeki tüm dosyaları (örn. <code>app/javascript/controllers</code>) bir ad alanına (örn. <code>"controllers"</code>) eşleştirir.
  </div>
</div>

Import map'ler, modern JavaScript özelliklerini desteklerken kurulumu temiz ve minimal tutar.

Peki, import map'imizde zaten bulunan bu JavaScript dosyaları nelerdir? Bunlar, Rails'in varsayılan olarak kullandığı Hotwire adlı bir frontend framework'üdür.

### Hotwire

Hotwire, sunucu tarafında oluşturulan HTML'den tam olarak yararlanmak için tasarlanmış bir JavaScript framework'üdür. 3 temel bileşenden oluşur:

1. [**Turbo**](https://turbo.hotwired.dev/) navigation, form gönderimleri,
   sayfa bileşenleri ve herhangi bir özel JavaScript yazmadan güncellemeleri yönetir.
2. [**Stimulus**](https://stimulus.hotwired.dev/) sayfaya işlevsellik eklemek
   için kendi özel JavaScript'inize ihtiyaç duyduğunuzda bir framework sağlar.
3. [**Native**](https://native.hotwired.dev/) web uygulamanızı gömüp
   onu yerel mobil özelliklerle aşamalı olarak geliştirerek hibrit mobil uygulamalar yapmanıza olanak tanır.

Henüz herhangi bir JavaScript yazmadık, ancak frontend'de Hotwire kullanıyorduk. Örneğin, ürün eklemek ve düzenlemek için oluşturduğunuz form Turbo tarafından destekleniyordu.

[Asset Pipeline](../asset_pipeline/) ve [Rails'te JavaScript ile Çalışma](../working_with_javascript_in_rails/) kılavuzlarında daha fazla bilgi edinin.

Test
----

Rails güçlü bir test paketi ile gelir. Bir ürün stokta olduğunda doğru sayıda e-posta gönderildiğinden emin olmak için bir test yazalım.

### Fixture

Rails kullanarak bir model oluşturduğunuzda, `test/fixtures` dizinine karşılık gelen bir fixture dosyasını otomatik olarak oluşturur.

Fixture'ler, testler çalıştırılmadan önce test veritabanınızı dolduran önceden tanımlanmış veri kümeleridır. Kayıtları, hatırlaması kolay isimlerle tanımlamanıza olanak tanır ve testlerinizde bunlara erişimi kolaylaştırır.

Bu dosya varsayılan olarak boş olacaktır - testleriniz için fixture'lerle doldurmanız gerek.

Aşağıdakilerle `test/fixtures/products.yml` konumundaki product fixture dosyasını güncelleyelim:

```yaml
tshirt:
  name: T-Shirt
  inventory_count: 15
```

Ve aboneler için, `test/fixtures/subscribers.yml` dosyasına şu iki fixture'ü ekleyelim:

```yaml
david:
  product: tshirt
  email: david@example.org

chris:
  product: tshirt
  email: chris@example.org
```

Burada `Product` fixture'üne name ile başvurabildiğimizi fark edeceksiniz. Rails bunu bizim için veritabanında otomatik olarak ilişkilendirir, böylece testlerde kayıt ID'leri ve ilişkileri yönetmek zorunda kalmayız.

Bu fixture'ler test paketimizi çalıştırdığımızda otomatik olarak veritabanına eklenecektir.

### E-postaları Test Etme

`test/models/product_test.rb` dosyasına bir test ekleyelim:

```ruby
require "test_helper"

class ProductTest < ActiveSupport::TestCase
  include ActionMailer::TestHelper

  test "sends email notifications when back in stock" do
    product = products(:tshirt)

    # Set product out of stock
    product.update(inventory_count: 0)

    assert_emails 2 do
      product.update(inventory_count: 99)
    end
  end
end
```

Bu testin ne yaptığını açıklayalım.

İlk olarak, test sırasında gönderilen e-postaları izleyebilmek için Action Mailer test helper'ları dahil ediyoruz.

`tshirt` fixture'ü `products()` fixture helper'ı kullanılarak yüklenir ve bu kayıt için Active Record nesnesini döndürür. Her fixture, her çalışmada veritabanı ID'leri farklı olabileceğinden fixture'lere name ile başvurmayı kolaylaştırmak için test paketinde bir helper oluşturur.

Ardından tişörtün stokta olmadığından emin olmak için envanterini 0'a güncelleriz.

Sonra, blok içindeki kod tarafından 2 e-posta oluşturulduğundan emin olmak için `assert_emails` kullanırız. E-postaları tetiklemek için, blok içinde ürünün envanter sayısını güncelleriz. Bu, Product modelindeki `notify_subscribers` callback'ini tetikleyerek e-posta gönderir. Bu işlem tamamlandığında, `assert_emails` e-postaları sayar ve beklenen sayıyla eşleştiğinden emin olur.

Test paketini `bin/rails test` ile veya dosya adını geçerek tek bir test dosyasını çalıştırabiliriz.

```bash
$ bin/rails test test/models/product_test.rb
Running 1 tests in a single process (parallelization threshold is 50)
Run options: --seed 3556

# Running:

.

Finished in 0.343842s, 2.9083 runs/s, 5.8166 assertions/s.
1 runs, 2 assertions, 0 failures, 0 errors, 0 skips
```

Testimiz geçti!

Rails ayrıca `test/mailers/product_mailer_test.rb` dosyasında `ProductMailer` için örnek bir test oluşturdu. Onun da testi geçmesi için güncelleyelim.

```ruby
require "test_helper"

class ProductMailerTest < ActionMailer::TestCase
  test "in_stock" do
    mail = ProductMailer.with(product: products(:tshirt), subscriber: subscribers(:david)).in_stock
    assert_equal "In stock", mail.subject
    assert_equal [ "david@example.org" ], mail.to
    assert_equal [ "from@example.com" ], mail.from
    assert_match "Good news!", mail.body.encoded
  end
end
```

Şimdi tüm test paketini çalıştıralım ve tüm testlerin geçtiğinden emin olalım.

```bash
$ bin/rails test
Running 2 tests in a single process (parallelization threshold is 50)
Run options: --seed 16302

# Running:

..

Finished in 0.665856s, 3.0037 runs/s, 10.5128 assertions/s.
2 runs, 7 assertions, 0 failures, 0 errors, 0 skips
```

Bunu, uygulama özelliklerinin tam kapsamını içeren bir test paketi oluşturmaya devam etmek için başlangıç noktası olarak kullanabilirsiniz.

[Rails Uygulamalarını Test Etme](../testing/) hakkında daha fazla bilgi edinin.

RuboCop ile Tutarlı Biçimlendirilmiş Kod
----------------------------------------

Kod yazarken bazen tutarsız biçimlendirme kullanabiliriz. Rails, kodumuzun tutarlı biçimde formatlanmasına yardımcı olan RuboCop adlı bir linter ile gelir.

Kodumuzun tutarlılığını kontrol etmek için şunu çalıştırabiliriz:

```bash
$ bin/rubocop
```

Bu, herhangi bir ihlali yazdıracak ve ne olduklarını söyleyecektir.

```bash
Inspecting 53 files
.....................................................

53 files inspected, no offenses detected
```

RuboCop, `--autocorrect` flag'ini (veya kısa versiyonu `-a`) kullanarak ihlalleri otomatik olarak düzeltebilir.

```
$ bin/rubocop -a
```

Güvenlik
--------

Rails, uygulamanızla ilgili güvenlik sorunlarını kontrol etmek için Brakeman gem'ini içerir - oturum ele geçirme, oturum sabitleme veya yeniden yönlendirme gibi saldırılara yol açabilecek güvenlik açıkları.

Terminalinize `bin/brakeman` yazarak çalıştırın. Bu, uygulamanızı analiz edip bir rapor çıkaracaktır.

```bash
$ bin/brakeman
Loading scanner...
...

== Overview ==

Controllers: 6
Models: 6
Templates: 15
Errors: 0
Security Warnings: 0

== Warning Types ==


No warnings found
```

[Rails Uygulamalarının Güvenliği](../security/) hakkında daha fazla bilgi edinin.

GitHub Actions ile Sürekli Entegrasyon
--------------------------------------

Rails uygulamaları; rubocop, brakeman ve test paketimizi çalıştıran önceden yazılmış bir GitHub Actions yapılandırması içeren bir `.github` klasörü oluşturur.

Kodumuzu GitHub Actions'ın etkin olduğu bir GitHub deposuna push ettiğimizde, otomatik olarak bu adımları çalıştıracak ve her biri için başarı veya başarısızlık durumlarını rapor edecektir. Bu, kod değişikliklerimizi kusurlar ve sorunlar açısından izlememize ve çalışmamız için tutarlı kalite sağlamamıza olanak tanır.

Üretim Ortamına Dağıtım
---------------

Ve şimdi eğlenceli kısma geldik: Uygulamanızı dağıtıma (deploy) çıkaralım.

Rails, uygulamamızı doğrudan bir sunucuya dağıtmak için kullanabileceğimiz [Kamal](https://kamal-deploy.org) adlı bir dağıtım aracı ile gelir. Kamal, uygulamanızı çalıştırmak ve sıfır kesinti ile dağıtmak için Docker konteynerlerini kullanır.

Rails varsayılan olarak, Kamal'ın Docker image derlemek için kullanacağı üretime hazır bir Dockerfile ile gelir ve tüm bağımlılıkları ve yapılandırmalarıyla uygulamanızın konteynerleştirilmiş bir sürümünü oluşturur. Bu Dockerfile, üretim ortamında asset'leri verimli bir şekilde sıkıştırmak ve sunmak için [Thruster](https://github.com/basecamp/thruster) kullanır.

Kamal ile dağıtıma çıkmak için şunlara ihtiyacımız var:

- 1GB veya daha fazla RAM'e sahip Ubuntu LTS çalıştıran bir sunucu. Sunucu
  düzenli güvenlik ve hata düzeltmeleri alabilmesi için Uzun Vadeli Destek (Long-Term Support - LTS) sürümüne sahip Ubuntu işletim sistemini çalıştırmalıdır. Hetzner, DigitalOcean ve diğer hosting hizmetleri başlamak için sunucular sağlar.
- Bir [Docker Hub](https://hub.docker.com) hesabı ve erişim token'ı. Docker Hub,
  sunucuda indirilebilmesi ve çalıştırılabilmesi için uygulamanın image'ini saklar.

Docker Hub'da, uygulama image'iniz için bir [Depo oluşturun](https://hub.docker.com/repository/create). Depo adı olarak "store" kullanın.

`config/deploy.yml` dosyasını açın. `192.168.0.1`'i sunucunuzun IP adresi, `your-user`'ı ise Docker Hub kullanıcı adınızla değiştirin.

```yaml
# Uygulamanızın adı. Konteynerleri benzersiz şekilde yapılandırmak için kullanılır.
service: store

# Konteyner image'in adı.
image: your-user/store

# Bu sunuculara dağıt.
servers:
  web:
    - 192.168.0.1

# Image host için kimlik bilgileri.
registry:
  # Docker Hub kullanmıyorsanız kayıt sunucusunu belirtin
  # server: registry.digitalocean.com / ghcr.io / ...
  username: your-user
```

`proxy:` bölümü altında, uygulamanız için SSL'i etkinleştirmek üzere bir alan adı (domain) ekleyebilirsiniz. DNS kaydınızın sunucuya işaret ettiğinden emin olun. Kamal, alan adı için SSL sertifikası vermek üzere LetsEncrypt kullanacaktır.

```yaml
proxy:
  ssl: true
  host: app.example.com
```

Kamal'ın uygulamanız için Docker image push edebilmesi için Docker'ın web sitesinde Read & Write izinleri olan [bir erişim token'ı oluşturun](https://app.docker.com/settings/personal-access-tokens/create).

Ardından Kamal'ın bulabilmesi için terminalde erişim token'ını dışa aktarın.

```bash
export KAMAL_REGISTRY_PASSWORD=your-access-token
```

Sunucunuzu kurmak ve uygulamanızı ilk kez dağıtıma çıkarmak için aşağıdaki komutu çalıştırın.

```bash
$ bin/kamal setup
```

Tebrikler! Yeni Rails uygulamanız artık canlıda/üretim ortamında!

Yeni Rails uygulamanızı çalışırken görmek için tarayıcınızı açın ve sunucunuzun IP adresini girin. Store projenizin çalışır durumda olduğunu görmelisiniz.

Bundan sonra, uygulamanızda değişiklik yapıp bunları üretime çıkarmak istediğinizde, şunu çalıştırabilirsiniz:

```bash
$ bin/kamal deploy
```

### Üretim Ortamına Kullanıcı Ekleme

Üretim ortamında ürün oluşturmak ve düzenlemek için üretim veritabanında bir User kaydına ihtiyacımız var.

Rails konsolunda bir üretim ortamı açmak için Kamal'ı kullanabilirsiniz.

```bash
$ bin/kamal console
```

```ruby
store(prod)> User.create!(email_address: "you@example.org", password: "s3cr3t", password_confirmation: "s3cr3t")
```

Artık bu e-posta ve şifre ile üretim ortamına giriş yapabilir ve ürünleri yönetebilirsiniz.

### Solid Queue ile Arka Plan İşleri

Arka plan işleri, görevleri kullanıcı deneyimini kesintiye uğratmadan ayrı bir süreçte arka planda asenkronize olarak çalıştırmanıza olanak tanır. 10.000 alıcıya stok e-postaları gönderdiğinizi hayal edin. Bu biraz zaman alabilir, bu yüzden Rails uygulamasını responsive (duyarlı) tutmak için bu görevi bir arka plan işine yükleyebiliriz.

Geliştirme ortamında Rails, ActiveJob ile arka plan işlerini işlemek için `:async` kuyruk adaptörünü kullanır. Async, bekleyen işleri bellekte depolar ancak yeniden başlatıldığında bekleyen işleri kaybeder. Bu, geliştirme ortamı için harikadır ancak üretim ortamı için değil.

Arka plan işlerini daha sağlam hale getirmek için Rails, üretim ortamları için `solid_queue` kullanır. Solid Queue işleri veritabanında saklar ve bunları ayrı bir süreçte yürütür.

Solid Queue, `config/deploy.yml` dosyasına `SOLID_QUEUE_IN_PUMA: true` ortam değişkeni kullanılarak üretim Kamal dağıtımımız için etkinleştirilmiştir. Bu, web sunucumuz Puma'ya Solid Queue sürecini otomatik olarak başlatıp durdurmasını söyler.

E-postalar Action Mailer'ın `deliver_later` metoduyla gönderildiğinde, bu e-postalar HTTP isteğini geciktirmemek için arka planda gönderilmek üzere Active Job'a gönderilecektir. Üretim ortamında Solid Queue ile e-postalar arka planda gönderilecek, gönderilmezse otomatik olarak yeniden denenecek ve yeniden başlatmalar sırasında işler veritabanında güvenli tutulacaktır.

<picture class="flowdiagram">
  <source srcset="/assets/images/rails/getting_started/background_jobs_dark.jpg" media="(prefers-color-scheme:dark)">
  <img src="/assets/images/rails/getting_started/background_jobs_light.jpg">
</picture>

Sırada Ne Var?
--------------

İlk Rails uygulamanızı oluşturup dağıtıma çıkardığınız için tebrikler!

Öğrenmeye devam etmek için özellik eklemeye ve güncellemeleri dağıtıma çıkarmaya devam etmenizi öneririz. İşte bazı fikirler:

* CSS ile tasarımı geliştirin
* Ürün Değerlendirmeleri ekleyin
* Uygulamayı başka bir dile çevirmeyi tamamlayın
* Ödemeler için bir ödeme akışı ekleyin
* Kullanıcıların ürünleri kaydetmesi için istek listeleri ekleyin
* Ürün görselleri için bir carousel ekleyin

Ayrıca diğer Ruby on Rails Kılavuzlarını okuyarak daha fazlasını öğrenmenizi öneririz:

* [Active Record Temelleri](../active_record_basics/)
* [Rails'te Layout ve Render](../layouts_and_rendering/)
* [Rails Uygulamalarını Test Etme](../testing/)
* [Rails Uygulamalarında Hata Ayıklama](../debugging_rails_applications/)
* [Rails Uygulamalarının Güvenliği](../security/)

Mutlu geliştirmeler!
**RESMİ KILAVUZA BU ADRES ÜZERİNDEN ULAŞABİLİRSİNİZ: <https://guides.rubyonrails.org>**

--------------------------------------------------------------------------------

Rails ile Başlarken
=====================

Bu kılavuz Ruby on Rails'i nasıl kurup çalıştıracağınızı anlatmaktadır.

Bu kılavuzu okuduktan sonra şunları öğreneceksiniz:

* Rails'i nasıl kuracağınızı, yeni bir Rails uygulaması nasıl oluşturacağınızı ve uygulamanızı bir veritabanına nasıl bağlayacağınızı.
* Rails uygulamasının genel yapısını.
* MVC (Model, View, Controller) ve RESTful tasarımının temel ilkelerini.
* Rails uygulamasının başlangıç parçalarını hızlı bir şekilde nasıl oluşturacağınızı.
* Kamal kullanarak uygulamanızı bir ürüne nasıl dağıtacağınızı (deploy edeceğinizi).


Giriş
------------
Ruby on Rails'e hoş geldiniz! Bu kılavuzda, Rails ile web uygulamaları geliştirmenin temel konularını ele alacağız. Bu kılavuzu takip etmek için Rails'te herhangi bir deneyime ihtiyacınız yok.

Rails, Ruby programlama dili için geliştirilmiş bi web çerçevesidir. Rails, Ruby'nin birçok özelliğinin avantajını barındırmaktadır, bu yüzden sizlere Ruby'nin temellerini öğrenmenizi *şiddetle* öneririz. Böylece bu kılavuzda karşılacağınız bazı temel terimleri ve kelimeleri anlıyor olacaksınız.

- [Resmi Ruby Websitesi](https://www.ruby-lang.org/en/documentation/)
- [Ücretsiz Programlama Kitaplarını İçeren Liste](https://github.com/EbookFoundation/free-programming-books/blob/main/books/free-programming-books-langs.md#ruby)

Rails'in Felsefesi
----------------


Rails, Ruby programlama dilinde yazılmış bir web uygulaması geliştirme çerçevesidir. Her geliştiricinin başlangıç için ihtiyaç duyduğu şeyleri varsayarak web uygulamalarını programlamayı kolaylaştırmak için tasarlanmıştır. Diğer birçok dil ve çerçeveye göre daha az kod yazarak daha fazlasını başarmanızı sağlar. Deneyimli Rails geliştiricileri, web uygulaması geliştirmeyi daha eğlenceli hale getirdiğini de belirtmektedir.

Rails, inatçı bir yazılımdır. İşleri yapmanın “en iyi” bir yolu olduğu varsayımından hareket eder ve bu yolu teşvik etmek için tasarlanmıştır - ve bazı durumlarda alternatiflerden caydırır. “Rails Yöntemi”ni öğrenirseniz, muhtemelen üretkenliğinizde muazzam bir artış göreceksiniz. Diğer dillerden edindiğiniz eski alışkanlıkları Rails geliştirirken ısrarla sürdürür ve başka yerlerde öğrendiğiniz kalıpları kullanmaya çalışırsanız, daha az mutlu bir deneyim yaşayabilirsiniz.

Rails felsefesi iki ana kılavuz ilke içerir:

- **Kendini Tekrar Etme:** DRY (Don't Repeat Yourself), yazılım geliştirmenin bir ilkesidir ve “Her bilgi parçası; sistem içinde tek, açık ve kesin bir şekilde temsil edilmelidir” der. Aynı bilgiyi tekrar tekrar yazmayarak kodumuz daha kolay bakımı yapılabilir, daha genişletilebilir ve daha az hata içerir hale gelir.
- **Yapılandırma Üzerine Kurallar:** Rails, bir web uygulamasında birçok şeyin en iyi şekilde nasıl yapılacağına dair fikirlere sahiptir ve bunları sonsuz yapılandırma dosyaları aracılığıyla kendiniz tanımlamanızı gerektirmek yerine, varsayılan olarak bu kurallar kümesini kullanır.

Yeni Bir Rails Uygulaması Geliştirme
------------------------

`store` adında bir proje geliştireceğiz, Rails'in yerleşik özelliklerinden birkaçını gösteren basit bir e-ticaret sitesi olacak.

İPUCU: Dolar işareti `$` ile başlayan tüm komutlar terminalde çalıştırılmalıdır.

### Ön Koşullar

Bu proje için şunlar gerekli:

* Ruby 3.2 veya daha üst sürümü
* Rails 8.1.0 veya daha üst sürümü
* Bir kod editörü

Ruby ve/veya Rails'i indirmeniz gerekiyorsa, bu tutorialı takip edin: [Ruby on Rails İndirme Kılavuzu](install_ruby_on_rails.html)

Rails'in doğru sürümünün yüklü olduğunu doğrulayalım. Mevcut sürümü görüntülemek için bir terminal açın ve aşağıdakileri çalıştırın. Bir sürüm numarası görmelisiniz:

```bash
$ rails --version
Rails 8.1.0
```

Rails 8.1.0 veya daha yüksek bir sürüm olmalı.

### İlk Rails Uygulamanızı Oluşturmak


Rails, hayatınızı kolaylaştırmak için çeşitli komutlar sunar. Tüm komutları görmek için `rails --help` komutunu çalıştırın.

`rails new` komutu, yeni bir Rails uygulamasının temelini oluşturur, bu yüzden buradan başlayalım.

`store` uygulamamızı oluşturmak için terminalinizde aşağıdaki komutu çalıştırın:

```bash
$ rails new store
```

NOT: Rails'in oluşturduğu uygulamayı flag kullanarak özelleştirebilirsiniz.
Bu seçenekleri görmek için `rails new --help` komutunu çalıştırın.

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
|config/|Uygulamanızın yönlendirmeleri (route), veritabanı ve daha fazlası için yapılandırma içerir. Bu konu [Rails Uygulamalarının Yapılandırılması](configuring.html) bölümünde daha ayrıntılı olarak ele alınmıştır.|
|db/|Mevcut veritabanı şemanızın yanı sıra veritabanı değişikliklerini (migrations) de içerir.|
|Dockerfile|Docker için yapılandırma dosyasıdır.|
|Gemfile<br>Gemfile.lock|Bu dosyalar, Rails uygulamanız için hangi gem bağımlılıklarının gerekli olduğunu belirtmenizi sağlar. Bu dosyalar [Bundler](https://bundler.io) gem'i tarafından kullanılır.|
|lib/|Uygulamanız için genişletilmiş modüller.|
|log/|Uygulama log dosyaları.|
|public/|Statik dosyaları ve derlenmiş assetleri içerir. Uygulamanız çalışırken, bu dizin olduğu gibi gösterilecektir.|
|Rakefile|Bu dosya komut satırından çalıştırılabilecek görevleri bulur ve yükler. Görev tanımları Rails'in bileşenleri boyunca tanımlanmıştır. `Rakefile`ı değiştirmek yerine, uygulamanızın `lib/tasks` dizinine dosyalar ekleyerek kendi görevlerinizi eklemelisiniz.|
|README.md|Bu, uygulamanız için kısa bir kullanım kılavuzudur. Uygulamanızın ne yaptığını, nasıl kurulacağını vb. başkalarına anlatmak için bu dosyayı düzenlemelisiniz.|
|script/|Tek seferlik veya genel amaçlı [script](https://github.com/rails/rails/blob/main/railties/lib/rails/generators/rails/script/USAGE) ve [benchmark](https://github.com/rails/rails/blob/main/railties/lib/rails/generators/rails/benchmark/USAGE)  içerir.|
|storage/|Disk Hizmeti (Disk Service) için SQLite veritabanlarını ve Active Storage dosyalarını içerir. Bu konu [Active Storage](active_storage_overview.html) bölümünde ele alınmıştır.|
|test/|Birim testleri (unit test), fikstürler ve diğer test aparatları. Bunlar [Rails Uygulamalarını Test Etme](testing.html) bölümünde ele alınmıştır.|
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

Rails kodu Model-View-Controller (MVC) mimarisi kullanılarak düzenlenmiştir. MVC ile kodlarımızın çoğunun içinde çalıştığı üç ana yapı vardır:

* Model - Uygulamanızdaki verileri yönetir. Tipik olarak, veritabanı tablolarınızdır.
* View - Yanıtları (response) HTML, JSON, XML gibi farklı biçimlerde göstermeyi (render) yönetir.
* Controller - Her istek için kullanıcı etkileşimlerini ve  mantığı (logic) işler.

<picture class="flowdiagram">
  <source srcset="/assets/images/getting_started/mvc_architecture_dark.jpg" media="(prefers-color-scheme:dark)">
  <img src="/assets/images/getting_started/mvc_architecture_light.jpg">
</picture>

Şimdi MVC hakkında temel bir anlayışa sahip olduğumuza göre, Rails'te bunun nasıl kullanıldığını görelim.

Merhaba, Rails!
-------------

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

![Rails welcome page](/assets/images/getting_started/rails_welcome.png)

İşe yarıyor!

Bu sayfa, yeni bir Rails uygulaması için *smoke test* olup, bir sayfayı sunmak için perde arkasında her şeyin çalıştığından emin olunmasını sağlar.

Rails sunucusunu istediğiniz zaman durdurmak için terminalinizde `Ctrl-C` tuşuna basın.
**RESMİ KILAVUZA BU ADRES ÜZERİNDEN ULAŞABİLİRSİNİZ: <https://guides.rubyonrails.org>**

--------------------------------------------------------------------------------

Action Controller
=====================

Bu kılavuzda, denetleyicilerin nasıl çalıştığını ve uygulamanızdaki istek döngüsüne nasıl uyum sağladığını öğreneceksiniz.

Bu kılavuzu okuduktan sonra şunları yapmayı öğreneceksiniz:

* Bir denetleyici aracılığıyla isteğin akışını takip etmek.
* Denetleyicinize aktarılan parametrelere erişmek.
* Güçlü Parametreler ve izin değerleri kullanmak.
* Verileri çerez, oturum ve flash bellekte depolamak.
* İstek işleme sırasında kodu yürütmek için eylem geri çağırmalarıyla çalışmak.
* İstek ve Yanıt Nesnelerini kullanmak.

Giriş
------------

Action Controller is the C in the Model View Controller
([MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller))
pattern. After the [router](routing.html) has matched a controller to an
incoming request, the controller is responsible for processing the request and
generating the appropriate output.
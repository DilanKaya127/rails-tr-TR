**BU DOSYAYI GITHUB ÜZERİNDEN OKUMAYINIZ, KILAVUZA BU ADRES ÜZERİNDEN ULAŞABİLİRSİNİZ: <https://guides.rubyonrails.org>.**

Action Cable'a Genel Bakış
=====================

Bu kılavuzda Action Cable'ın nasıl çalıştığını ve WebSocketleri kullanarak
gerçek zamanlı özellikleri Rails uygulamanıza nasıl dahil edeceğinizi öğreneceksiniz.

Bu kılavuzu okuduktan sonra şunları bileceksiniz:

* Action Cable'ın ne olduğunu, backend ve frontende nasıl entegre edeceğinizi 
* Action Cable'ı nasıl kuracağınızı
* Kanalları (channels) nasıl kuracağınızı
* Action Cable'ı çalıştırmak için dağıtım ve mimari kurulumunu

--------------------------------------------------------------------------------

Action Cable Nedir? 
---------------------

Action Cable, Rails uygulamanıza
[WebSocket](https://en.wikipedia.org/wiki/WebSocket)'i sorunsuz bir şekilde entegre eder.
Performans ve ölçeklenebilirliği sağlarken aynı zamanda gerçek zamanlı özelliklerin,
Rails uygulamanızın geri kalanında olduğu gibi aynı stil ve formda Ruby'de yazılmasını sağlar. 
Hem istemci taraflı Javascript çerçevesi (framework) hem sunucu taraflı Ruby çerçevesi
sağlayan bir full-stack aracıdır. Active Record veya tercih ettiğiniz bir ORM ile 
yazılmış kendi tüm domain modelinize erişiminiz vardır.

Terminoloji
-----------

Action Cable, HTTP'nin istek-yanıt protokolü yerine WebSocket kullanır.
Hem Action Cable hem WebSocket daha az bilinen bazı terminolojiler sunar:

### Connections (Bağlantılar)

*Bağlantılar* istemci-sunucu ilişkisinin temelini oluşturur. Tek bir Action Cable sunucusu birden fazla bağlantının üstesinden gelebilir. Her bir WebSocket bağlantısı için bir bağlantı örneği vardır. Tek bir kullanıcı eğer birden fazla tarayıcı sekmesi veya cihaz kullanıyorsa uygulamanıza açık birden fazla Websocket'e sahip olabilir. 

### Consumers (Tüketiciler)

Bir WebSocket bağlantısının istemcisine *tüketici* denir. Action Cable'da tüketici, istemci-taraflı Javascript çerçevesi tarafından oluşturulur.

### Channels (Kanallar)

Her bir tüketici sırayla birden fazla *kanala* abone olabilir. Her kanal, bir controllerın tipik bir MVC kurulumunda yaptığına benzer bir şekilde mantıksal bir iş birimini kapsar. Örneğin, `ChatChannel` ve `AppearancesChannel` adında kanallarınız olsun. Bir tüketici bu kanallardan birine veya her ikisine de abone olabilir. Bir tüketicinin en azından bir kanala abone olması gerekir.

### Subscribers (Aboneler)

Tüketici bir kanala abone olduğunda *abone* gibi davranır. Abone ile kanal arasındaki bağlantıya ise abonelik denir. Bir tüketici belirli bir kanala istediği kadar abone olabilir. Örneğin, bir tüketici birden fazla chat odasına aynı anda abone olabilir. (Fiziksel bir kullanıcının bağlantınıza her bir sekme veya açık cihaz için birden fazla tüketicisinin olabildiğini unutmayın.)

### Pub/Sub (Yayınla/Abone Ol)

[Pub/Sub](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) Yayınla/Abone Ol paradigması; bilgi gönderenlerin (yayıncıların), belirli alıcıları belirtmeden, soyut bir alıcılar sınıfına (abonelere) verileri gönderdiği bir mesaj kuyruğu paradigmasını ifade eder. Action Cable, sunucu ile çok sayıda istemci arasında iletişim kurmak için bu yaklaşımı kullanır.

### Broadcastings (Yayınlar)

Bir yayın; yayıncının ilettiği her şeyin, o isimle yayın yapılan yayını dinleyen kanal abonelerine doğrudan gönderildiği bir yayınla/abone ol (pub/sub) bağlantısıdır. Her kanal, sıfır veya daha fazla yayını dinleyebilir.

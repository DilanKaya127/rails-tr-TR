**BU DOSYAYI GITHUB ÜZERİNDEN OKUMAYINIZ, KILAVUZA BU ADRES ÜZERİNDEN ULAŞABİLİRSİNİZ: <https://guides.rubyonrails.org>.**

Action Cable'a Genel Bakış
=====================

Bu kılavuzda Action Cable'ın nasıl çalıştığını ve WebSocketleri kullanarak
gerçek zamanlı özellikleri Rails uygulamanıza nasıl dahil edeceğinizi öğreneceksiniz.

Bu kılavuzu okuduktan sonra şunları bileceksiniz:

* Action Cable'ın ne olduğunu, backend ve frontende nasıl entegre edeceğinizi 
* How to set up Action Cable'ı nasıl kuracağınızı
* Kanalları (channels) nasıl kuracağınızı
* Action Cable'ı çalıştırmak için Dağıtım (deoployment) ve Mimari (architecture) kurulumunu

--------------------------------------------------------------------------------

Action Cable Nedir? 
---------------------

Action Cable, Rails uygulamanıza
[WebSocket](https://en.wikipedia.org/wiki/WebSocket)'i sorunsuz bir şekilde entegre eder.
Performans ve ölçeklenebilirliği sağlarken aynı zamanda gerçek zamanlı özelliklerin,
Rails uygulamanızın geri kalanında olduğu gibi aynı stil ve formda Ruby'de yazılmasını sağlar. 
Hem stemci taraflı Javascript çerçevesi (framework) hem sunucu taraflı Ruby çerçevesi (framework)
sağlayan bir tam-yığın (full-stack) aracıdır. Active Record veya tercih ettiğiniz bir ORM ile 
yazılmış kendi tüm domain modelinize erişiminiz vardır.

Terminoloji
-----------

Action Cable, HTTP'nin istek-yanıt protokolü yerine WebSocket kullanır.
Hem Action Cable hem WebSocket daha az bilinen bazı terminolojiler sunar:

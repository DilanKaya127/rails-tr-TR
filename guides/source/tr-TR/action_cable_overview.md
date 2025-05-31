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

Terimler
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

[Yayınla/Abone Ol](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) paradigması; bilgi gönderenlerin (yayıncıların), belirli alıcıları belirtmeden, soyut bir alıcılar sınıfına (abonelere) verileri gönderdiği bir mesaj kuyruğu paradigmasını ifade eder. Action Cable, sunucu ile çok sayıda istemci arasında iletişim kurmak için bu yaklaşımı kullanır.

### Broadcastings (Yayınlar)

Bir yayın; yayıncının ilettiği her şeyin, o isimle yayın yapılan yayını dinleyen kanal abonelerine doğrudan gönderildiği bir yayınla/abone ol (pub/sub) bağlantısıdır. Her kanal, sıfır veya daha fazla yayını dinleyebilir.

## Server-Side Components (Sunucu Taraflı Bileşenler)

### Connections (Bağlantılar)

Sunucu tarafından kabul edilen her WebSocket için bir bağlantı nesnesi oluşturulur. Bu nesne, bundan sonra oluşturulacak tüm *kanal aboneliklerinin* ebeveyni (parent) haline gelir. Bağlantının kendisi, kimlik doğrulama ve yetkilendirme dışında herhangi bir spesifik uygulama mantığı ile ilgilenmez. Bir WebSocket bağlantısının istemcisine bağlantı *tüketicisi* (connection consumer) denir. Bireysel bir kullanıcı her bir açık tarayıcı sekmesi, penceresi veya cihazı için tüketici-bağlantı çifti oluşturur.

Bağlantılar, [`ActionCable::Connection::Base`][] sınıfını genişleten (extend) `ApplicationCable::Connection` sınıfının örnekleridir. `ApplicationCable::Connection`da gelen bağlantıyı yetkilendirirsiniz ve kullanıcı tanımlanabiliyorsa bağlantı kurmaya devam edersiniz.

### Connection Setup (Bağlantı Kurulumu)

```ruby
# app/channels/application_cable/connection.rb
module ApplicationCable
  class Connection < ActionCable::Connection::Base
    identified_by :current_user

    def connect
      self.current_user = find_verified_user
    end

    private
      def find_verified_user
        if verified_user = User.find_by(id: cookies.encrypted[:user_id])
          verified_user
        else
          reject_unauthorized_connection
        end
      end
  end
end
```

Buradaki [`identified_by`][], daha sonra spesifik bir bağlantıyı bulmak için kullanılabilen bağlantı tanımlayıcısını (identifier) belirtir. Unutmayın, tanımlayıcı olarak işaretlenen her şey, bağlantı dışında oluşturulan herhangi bir kanal örneği üzerinde otomatik olarak aynı isimle bir temsilci oluşturur.

Bu örnek, uygulamanızın başka bir yerinde kullanıcının çoktan bir kimlik doğrulaması gerçekleştirmiş olduğunuzu ve başarılı bir kimlik doğrulamasının kullanıcı ID'si ile şifrelenmiş bir çerez (cookie) ayarlayacağını varsayar. 

Yeni bir bağlantı girişiminde bulunulduğunda çerez otomatik olarak bağlantı örneğine gönderilir, ve siz bunu `current_user`ı ayarlamak için kullanırsınız. Aynı mevcut kullanıcı tarafından tanımladığınız bağlantıyla, daha sonra belirli bir kullanıcı tarafından açılan tüm açık bağlantıları geri alabilmeyi (veya kullanıcı silinir ya da yetkisiz hale gelirse potansiyel olarak tüm bağlantıları kesebilmeyi) sağlarsınız. 

Eğer yetkilendirme yaklaşımınız bir oturum kullanmayı kapsıyorsai oturumu depolamak için çerezleri kullanırsınız. Oturum çerezinizin adı `_session` ve kullanıcı ID'niz  `user_id` ise bu yaklaşımı kullanabilirsiniz:

```ruby
verified_user = User.find_by(id: cookies.encrypted["_session"]["user_id"])
```

[`ActionCable::Connection::Base`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Base.html
[`identified_by`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Identification/ClassMethods.html#method-i-identified_by

### Exception Handling (İstisna Yönetimi)

Varsayılan olarak, işlenmemiş (unhandled) istisnalar yakalanır ve Rails'in kayıt tutucusunda (logger) tutulur. Eğer bu istisnaları global olarak yakalamak ve örneğin harici bir hata (bug) takip servisine bildirmek isterseniz, [`rescue_from`][] ile bunu yapabilirsiniz:

```ruby
# app/channels/application_cable/connection.rb
module ApplicationCable
  class Connection < ActionCable::Connection::Base
    rescue_from StandardError, with: :report_error

    private
      def report_error(e)
        SomeExternalBugtrackingService.notify(e)
      end
  end
end
```

[`rescue_from`]: https://api.rubyonrails.org/classes/ActiveSupport/Rescuable/ClassMethods.html#method-i-rescue_from

#### Connection Callbacks (Bağlantı Geri Çağrıları)

[`ActionCable::Connection::Callbacks`][] abone olma, abonelik iptali veya bir eylem gerçekleştirme gibi istemciye komut gönderilirken çağrılan callback hook'ları sağlar:

* [`before_command`][]
* [`after_command`][]
* [`around_command`][]

[`ActionCable::Connection::Callbacks`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Callbacks.html
[`after_command`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Callbacks/ClassMethods.html#method-i-after_command
[`around_command`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Callbacks/ClassMethods.html#method-i-around_command
[`before_command`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Callbacks/ClassMethods.html#method-i-before_command

### Channels (Kanalalr)

Bir *kanal*, bir controllerın tipik bir MVC kurulumunda yaptığına benzer bir şekilde mantıksal bir iş birimini kapsar. Varsayılan olarak Rails, kanal oluşturucuyu (channel generator) ilk kez kullandığınızda, kanallarınız arasında paylaşılan mantığı kapsüllemek için bir ebeveyn sınıf olan `ApplicationCable::Channel` (ki bu sınıf `ActionCable::Channel::Base` sınıfını genişletir) oluşturur.

#### Parent Channel Setup (Ebeveyn Kanalı Kurma)

```ruby
# app/channels/application_cable/channel.rb
module ApplicationCable
  class Channel < ActionCable::Channel::Base
  end
end
```

Kendi kanal sınıflarınız şu örnekler gibi görünebilir:

```ruby
# app/channels/chat_channel.rb
class ChatChannel < ApplicationCable::Channel
end
```

```ruby
# app/channels/appearance_channel.rb
class AppearanceChannel < ApplicationCable::Channel
end
```

[`ActionCable::Channel::Base`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Base.html

Bir tüketici, bu kanallardan birine veya her ikisine birden abone olabilir.

#### Subscriptions (Abonelikler)


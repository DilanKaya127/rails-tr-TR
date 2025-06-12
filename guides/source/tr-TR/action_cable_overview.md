**RESMİ KILAVUZA BU ADRES ÜZERİNDEN ULAŞABİLİRSİNİZ: <https://guides.rubyonrails.org>**

---------------------------------------------------------------------------------------

Action Cable'a Genel Bakış
==========================

Bu kılavuzda Action Cable'ın nasıl çalıştığını ve WebSocketleri kullanarak
gerçek zamanlı özellikleri Rails uygulamanıza nasıl dahil edeceğinizi öğreneceksiniz.

Bu kılavuzu okuduktan sonra şunları bileceksiniz:

* Action Cable'ın ne olduğunu, backend ve frontende nasıl entegre edeceğinizi 
* Action Cable'ı nasıl kuracağınızı
* Kanalları nasıl kuracağınızı
* Action Cable'ı çalıştırmak için dağıtım ve mimari kurulumunu

Action Cable Nedir? 
-------------------

Action Cable, Rails uygulamanıza
[WebSocket](https://en.wikipedia.org/wiki/WebSocket)'i sorunsuz bir şekilde entegre eder.
Performans ve ölçeklenebilirliği sağlarken aynı zamanda gerçek zamanlı özelliklerin,
Rails uygulamanızın geri kalanında olduğu gibi aynı stil ve formda Ruby'de yazılmasını sağlar. 
Hem istemci taraflı Javascript framework'ü hem sunucu taraflı Ruby framework'ü
sağlayan bir full-stack aracıdır. Active Record veya tercih ettiğiniz bir ORM ile yazılmış kendi tüm domain modelinize erişiminiz vardır.

Terimler
--------

Action Cable, HTTP'nin istek-yanıt protokolü yerine WebSocket kullanır.
Hem Action Cable hem WebSocket daha az bilinen bazı terminolojiler sunar:

### Bağlantılar

*Bağlantılar (Connections)* istemci-sunucu ilişkisinin temelini oluşturur. Tek bir Action Cable sunucusu birden fazla bağlantının üstesinden gelebilir. Her bir WebSocket bağlantısı için bir bağlantı örneği vardır. Tek bir kullanıcı eğer birden fazla tarayıcı sekmesi veya cihaz kullanıyorsa uygulamanıza açık birden fazla Websocket'e sahip olabilir. 

### Tüketiciler

Bir WebSocket bağlantısının istemcisine *tüketici (consumer)* denir. Action Cable'da tüketici, istemci-taraflı Javascript framework'ü tarafından oluşturulur.

### Kanallar

Her bir tüketici sırayla birden fazla *kanala (channel)* abone olabilir. Her kanal, bir controllerın tipik bir MVC kurulumunda yaptığına benzer bir şekilde mantıksal bir iş birimini kapsar. Örneğin, `ChatChannel` ve `AppearancesChannel` adında kanallarınız olsun. Bir tüketici bu kanallardan birine veya her ikisine de abone olabilir. Bir tüketicinin en azından bir kanala abone olması gerekir.

### Aboneler

Tüketici bir kanala abone olduğunda *abone (subscriber)* gibi davranır. Abone ile kanal arasındaki bağlantıya ise abonelik denir. Bir tüketici belirli bir kanala istediği kadar abone olabilir. Örneğin, bir tüketici birden fazla chat odasına (room) aynı anda abone olabilir. (Fiziksel bir kullanıcının bağlantınıza açık olan her bir sekme veya cihaz için bir tane olmak üzere birden fazla tüketicisinin olabildiğini unutmayın.)

### Yayınla/Abone Ol

[Yayınla/Abone Ol (Pub/Sub)](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) paradigması; bilgi gönderenlerin (yayıncıların), belirli alıcıları belirtmeden, soyut bir alıcılar sınıfına (abonelere) verileri gönderdiği bir mesaj kuyruğu paradigmasını ifade eder. Action Cable, sunucu ile çok sayıda istemci arasında iletişim kurmak için bu yaklaşımı kullanır.

### Yayınlar

Bir yayın (broadcasting); yayıncının ilettiği her şeyin, o isimle yayın yapılan yayını dinleyen kanal abonelerine doğrudan gönderildiği bir yayınla/abone ol (pub/sub) bağlantısıdır. Her kanal, sıfır veya daha fazla yayını dinleyebilir.

## Sunucu Taraflı Bileşenler

### Bağlantılar

Sunucu tarafından kabul edilen her WebSocket için bir bağlantı nesnesi oluşturulur. Bu nesne, bundan sonra oluşturulacak tüm *kanal aboneliklerinin* ebeveyni (parent) haline gelir. Bağlantının kendisi, kimlik doğrulama ve yetkilendirme dışında herhangi bir spesifik uygulama mantığı ile ilgilenmez. Bir WebSocket bağlantısının istemcisine bağlantı *tüketicisi* denir. Bireysel bir kullanıcı her bir açık tarayıcı sekmesi, penceresi veya cihazı için tüketici-bağlantı çifti oluşturur.

Bağlantılar, [`ActionCable::Connection::Base`][] sınıfını genişleten (extend) `ApplicationCable::Connection` sınıfının örnekleridir. `ApplicationCable::Connection`da gelen bağlantıyı yetkilendirirsiniz ve kullanıcı tanımlanabiliyorsa bağlantı kurmaya devam edersiniz.

#### Bağlantı Kurulumu

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

#### İstisna Yönetimi

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

#### Bağlantı Callback'leri

[`ActionCable::Connection::Callbacks`][] abone olma, abonelik iptali veya bir eylem gerçekleştirme gibi istemciye komut gönderilirken çağrılan callback hook'ları sağlar:

* [`before_command`][]
* [`after_command`][]
* [`around_command`][]

[`ActionCable::Connection::Callbacks`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Callbacks.html
[`after_command`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Callbacks/ClassMethods.html#method-i-after_command
[`around_command`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Callbacks/ClassMethods.html#method-i-around_command
[`before_command`]: https://api.rubyonrails.org/classes/ActionCable/Connection/Callbacks/ClassMethods.html#method-i-before_command

### Kanallar

Bir *kanal*, bir controllerın tipik bir MVC kurulumunda yaptığına benzer bir şekilde mantıksal bir iş birimini kapsar. Varsayılan olarak Rails, kanal oluşturucuyu (channel generator) ilk kez kullandığınızda, kanallarınız arasında paylaşılan mantığı kapsüllemek için bir ebeveyn sınıf olan `ApplicationCable::Channel` (ki bu sınıf `ActionCable::Channel::Base` sınıfını genişletir/extend eder) oluşturur.

#### Ebeveyn Kanal Kurulumu

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

#### Abonelikler

Tüketiciler kanallara abone olarak "abone" durumuna gelirler. Bağlantılarına abonelik denir. Üretilen mesajlar daha sonra kanal tüketicisi tarafından gönderilen bir tanımlayıcıya göre bu kanal aboneliklerine yönlendirilir.

```ruby
# app/channels/chat_channel.rb
class ChatChannel < ApplicationCable::Channel
  # Tüketici başarılı bir şekilde
  # bu kanala abone olduğunda çağrılır.
  def subscribed
  end
end
```

#### İstisna Yönetimi

`ApplicationCable::Connection`da olduğu gibi, ortaya çıkan istisnaları işlemek için belirli bir kanalda [`rescue_from`][]'u da kullanabilirsiniz:

```ruby
# app/channels/chat_channel.rb
class ChatChannel < ApplicationCable::Channel
  rescue_from "MyError", with: :deliver_error_message

  private
    def deliver_error_message(e)
      # broadcast_to(...)
    end
end
```

#### Kanal Callback'leri

[`ActionCable::Channel::Callbacks`][], bir kanalın yaşam döngüsü sırasında çağrılan callback hook'larını sağlar:

* [`before_subscribe`][]
* [`after_subscribe`][] ([`on_subscribe`][] takma adıyla anılır)
* [`before_unsubscribe`][]
* [`after_unsubscribe`][] ([`on_unsubscribe`][] takma adıyla anılır)

[`ActionCable::Channel::Callbacks`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Callbacks.html
[`after_subscribe`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Callbacks/ClassMethods.html#method-i-after_subscribe
[`after_unsubscribe`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Callbacks/ClassMethods.html#method-i-after_unsubscribe
[`before_subscribe`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Callbacks/ClassMethods.html#method-i-before_subscribe
[`before_unsubscribe`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Callbacks/ClassMethods.html#method-i-before_unsubscribe
[`on_subscribe`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Callbacks/ClassMethods.html#method-i-on_subscribe
[`on_unsubscribe`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Callbacks/ClassMethods.html#method-i-on_unsubscribe

## İstemci Taraflı Bileşenler

### Bağlantılar

Tüketiciler kendi taraflarında bir bağlantı örneğine ihtiyaç duyarlar. Bu, Rails tarafından varsayılan olarak oluşturulan aşağıdaki JavaScript kullanılarak kurulabilir:

#### Tüketici Bağlantısı

```js
// app/javascript/channels/consumer.js
// Action Cable, Rails'te WebSocket ilebaşa çıkmak için bir framework sağlar.
// WebSocket özelliklerinin bulunduğu yeni kanalları
// `bin/rails generate channel` komutunu kullanarak oluşturabilirsiniz.

import { createConsumer } from "@rails/actioncable"

export default createConsumer()
```

Bu, varsayılan olarak sunucunuzdaki `/cable` ile bağlantı kuracak bir tüketici hazırlayacaktır. İlgi duyduğunuz en az bir aboneliği ayrıca belirtmediğiniz sürece bağlantı kurulmayacaktır.

Tüketici, isteğe bağlı olarak bağlanılacak URL'i belirten bir bağımsız değişken alabilir. Bu bir string veya WebSocket açıldığında çağrılacak string döndüren bir fonksiyon olabilir.

```js
// Bağlanmak için farklı bir URL belirtin 
createConsumer('wss://example.com/cable')
// veya HTTP üzerinden websocket kullanırken:
createConsumer('https://ws.example.com/cable')

// URL'i dinamik olarak oluşturmak için
// bir fonksiyon kullanın
createConsumer(getWebSocketURL)

function getWebSocketURL() {
  const token = localStorage.get('auth-token')
  return `wss://example.com/cable?token=${token}`
}
```

#### Aboneler

Bir tüketici, belirli bir kanala abonelik oluşturarak abone olur:

```js
// app/javascript/channels/chat_channel.js
import consumer from "./consumer"

consumer.subscriptions.create({ channel: "ChatChannel", room: "Best Room" })

// app/javascript/channels/appearance_channel.js
import consumer from "./consumer"

consumer.subscriptions.create({ channel: "AppearanceChannel" })
```

Bu, aboneliği oluştururken, alınan verilere yanıt vermek için gereken işlevsellik daha sonra açıklanacaktır.

Bir tüketici belirli bir kanala istediği sayıda abone olabilir. Örneğin, bir tüketici aynı anda birden fazla sohbet odasına abone olabilir:

```js
// app/javascript/channels/chat_channel.js
import consumer from "./consumer"

consumer.subscriptions.create({ channel: "ChatChannel", room: "1st Room" })
consumer.subscriptions.create({ channel: "ChatChannel", room: "2nd Room" })
```

## İstemci-Sunucu Etkileşimleri

### Yayın Akışları

*Yayın akışları (Streams)*, kanalların yayınlanan içeriği (yayınları) abonelerine yönlendirdiği mekanizmayı sağlar. Örneğin, aşağıdaki kod, `:room` parametresinin değeri `"Best Room"` olduğunda `chat_Best Room` adlı yayına abone olmak için [`stream_from`][] kullanır:

```ruby
# app/channels/chat_channel.rb
class ChatChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chat_#{params[:room]}"
  end
end
```

Ardından, Rails uygulamanızın başka bir yerinde [`broadcast`][] çağrısı yaparak böyle bir odaya yayın yapabilirsiniz:

```ruby
ActionCable.server.broadcast("chat_Best Room", { body: "This Room is Best Room." })
```

Bir modelle ilişkili bir yayın akışınız varsa, yayın adı kanal ve modelden oluşturulabilir. Örneğin, aşağıdaki kod `posts:Z2lkOi8vVGVzdEFwcC9Qb3N0LzE` gibi bir yayına abone olmak için [`stream_for`][] kullanır. Burada `Z2lkOi8vVGVzdEFwc9Qb3N0LzE`, Post modelinin GlobalID'sidir.

```ruby
class PostsChannel < ApplicationCable::Channel
  def subscribed
    post = Post.find(params[:id])
    stream_for post
  end
end
```

Daha sonra [`broadcast_to`][] çağrısı yaparak bu kanala yayın yapabilirsiniz:

```ruby
PostsChannel.broadcast_to(@post, @comment)
```

[`broadcast`]: https://api.rubyonrails.org/classes/ActionCable/Server/Broadcasting.html#method-i-broadcast
[`broadcast_to`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Broadcasting/ClassMethods.html#method-i-broadcast_to
[`stream_for`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Streams.html#method-i-stream_for
[`stream_from`]: https://api.rubyonrails.org/classes/ActionCable/Channel/Streams.html#method-i-stream_from

### Yayınlar

Bir *yayın*, bir yayıncı tarafından iletilen herhangi bir şeyin doğrudan adı geçen yayını yayınlayan kanal abonelerine yönlendirildiği bir yayınla/abone ol bağlantısıdır. Her kanal sıfır veya daha fazla yayın yayınlıyor olabilir.

Yayınlar tamamen çevrimiçi bir kuyruktur ve zamana bağlıdır. Eğer bir tüketici yayın akışında değilse (belirli bir kanala abone değilse), daha sonra bağlanması durumunda yayını alamayacaktır.

### Abonelikler

Bir tüketici bir kanala abone olduğunda, bir abone olarak hareket eder. Bu bağlantıya abonelik denir. Gelen mesajlar daha sonra kablo tüketicisi (cable consumer) tarafından gönderilen bir tanımlayıcıya (identifier) dayalı olarak bu kanal aboneliklerine yönlendirilir.

```js
// app/javascript/channels/chat_channel.js
import consumer from "./consumer"

consumer.subscriptions.create({ channel: "ChatChannel", room: "Best Room" }, {
  received(data) {
    this.appendLine(data)
  },

  appendLine(data) {
    const html = this.createLine(data)
    const element = document.querySelector("[data-chat-room='Best Room']")
    element.insertAdjacentHTML("beforeend", html)
  },

  createLine(data) {
    return `
      <article class="chat-line">
        <span class="speaker">${data["sent_by"]}</span>
        <span class="body">${data["body"]}</span>
      </article>
    `
  }
})
```

### Kanallara Parametre Geçirme

Bir abonelik oluştururken parametreleri istemci tarafından sunucu tarafına geçirebilirsiniz. Örneğin:

```ruby
# app/channels/chat_channel.rb
class ChatChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chat_#{params[:room]}"
  end
end
```

`subscriptions.create`'e ilk argüman olarak geçirilen bir nesne, kablo kanalındaki params hash'i olur. `channel` anahtar sözcüğü gereklidir:

```js
// app/javascript/channels/chat_channel.js
import consumer from "./consumer"

consumer.subscriptions.create({ channel: "ChatChannel", room: "Best Room" }, {
  received(data) {
    this.appendLine(data)
  },

  appendLine(data) {
    const html = this.createLine(data)
    const element = document.querySelector("[data-chat-room='Best Room']")
    element.insertAdjacentHTML("beforeend", html)
  },

  createLine(data) {
    return `
      <article class="chat-line">
        <span class="speaker">${data["sent_by"]}</span>
        <span class="body">${data["body"]}</span>
      </article>
    `
  }
})
```

```ruby
# Uygulamanızda bir yerde,
# belki de bir NewCommentJob'dan bu çağrılır.
ActionCable.server.broadcast(
  "chat_#{room}",
  {
    sent_by: "Paul",
    body: "This is a cool chat app."
  }
)
```

### Bir Mesajı Yeniden Yayınlama

Yaygın bir kullanım durumu, bir istemci tarafından gönderilen bir mesajın diğer bağlı istemcilere *yeniden yayınlanmasıdır*.

```ruby
# app/channels/chat_channel.rb
class ChatChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chat_#{params[:room]}"
  end

  def receive(data)
    ActionCable.server.broadcast("chat_#{params[:room]}", data)
  end
end
```

```js
// app/javascript/channels/chat_channel.js
import consumer from "./consumer"

const chatChannel = consumer.subscriptions.create({ channel: "ChatChannel", room: "Best Room" }, {
  received(data) {
    // data => { sent_by: "Paul", body: "This is a cool chat app." }
  }
})

chatChannel.send({ sent_by: "Paul", body: "This is a cool chat app." })
```

Yeniden yayın, mesajı gönderen istemci de dahil olmak üzere tüm bağlı istemciler tarafından alınacaktır. Parametrelerin kanala abone olduğunuzda olduğu gibi aynı olduğunu unutmayın.

## Full-Stack Örnekler

Aşağıdaki kurulum adımları her iki örnek için de ortaktır:

  1. [Bağlantınızı kurun](#baglanti-kurulumu).
  2. [Ebeveyn kanalı kurun](#ebeveyn-kanal-kurulumu).
  3. [Tüketicinizi bağlayın](#tuketici-baglantisi).

### Örnek 1: Kullanıcı Görünümleri

İşte bir kullanıcının çevrimiçi olup olmadığını ve hangi sayfada olduğunu izleyen basit bir kanal örneği. (Bu, çevrimiçi olduklarında kullanıcı adının yanında yeşil bir nokta göstermek gibi varlık özellikleri oluşturmak için kullanışlıdır.)

Sunucu tarafı görünüm kanalını oluşturun:

```ruby
# app/channels/appearance_channel.rb
class AppearanceChannel < ApplicationCable::Channel
  def subscribed
    current_user.appear
  end

  def unsubscribed
    current_user.disappear
  end

  def appear(data)
    current_user.appear(on: data["appearing_on"])
  end

  def away
    current_user.away
  end
end
```

Bir abonelik başlatıldığında `subscribed` callback'i tetiklenir ve bu fırsatı "mevcut kullanıcı gerçekten göründü" demek için kullanırız. Bu görünme/kaybolma API'yı Redis, bir veritabanı veya başka bir şey tarafından desteklenebilir.

İstemci tarafı görünüm kanalı aboneliğini oluşturun:

```js
// app/javascript/channels/appearance_channel.js
import consumer from "./consumer"

consumer.subscriptions.create("AppearanceChannel", {
  // Abonelik oluşturulduğunda bir kez çağrılır.
  initialized() {
    this.update = this.update.bind(this)
  },

  // Abonelik sunucuda kullanıma hazır olduğunda çağrılır.
  connected() {
    this.install()
    this.update()
  },

  // WebSocket bağlantısı kapatıldığında çağrılır.
  disconnected() {
    this.uninstall()
  },

  // Abonelik sunucu tarafından reddedildiğinde çağrılır.
  rejected() {
    this.uninstall()
  },

  update() {
    this.documentIsActive ? this.appear() : this.away()
  },

  appear() {
    // Sunucuda `AppearanceChannel#appear(data)` öğesini çağırır.
    this.perform("appear", { appearing_on: this.appearingOn })
  },

  away() {
    // Sunucuda `AppearanceChannel#away` öğesini çağırır.
    this.perform("away")
  },

  install() {
    window.addEventListener("focus", this.update)
    window.addEventListener("blur", this.update)
    document.addEventListener("turbo:load", this.update)
    document.addEventListener("visibilitychange", this.update)
  },

  uninstall() {
    window.removeEventListener("focus", this.update)
    window.removeEventListener("blur", this.update)
    document.removeEventListener("turbo:load", this.update)
    document.removeEventListener("visibilitychange", this.update)
  },

  get documentIsActive() {
    return document.visibilityState === "visible" && document.hasFocus()
  },

  get appearingOn() {
    const element = document.querySelector("[data-appearing-on]")
    return element ? element.getAttribute("data-appearing-on") : null
  }
})
```

#### İstemci-Sunucu Etkileşimi

1. **İstemci**, `createConsumer()` aracılığıyla **Sunucu**ya bağlanır.
  (`consumer.js`). **Sunucu** bu   bağlantıyı `current_user` ile tanımlar.

2. **İstemci**, `consumer.subscriptions.create({ channel: "AppearanceChannel" })` aracılığıyla 
  görünüm kanalına abone olur. (`appearance_channel.js`)

3. **Sunucu** görünüm kanalı için yeni bir abonelik başlatıldığını fark eder ve `current_user` üzerinde `appear` metodunu çağırarak `subscribed` callback'ini çalıştırır. (`appearance_channel.rb`)

4. **İstemci** bir abonelik kurulduğunu fark eder ve `connected` 
  (`appearance_channel.js`) öğesini çağırır, bu da `install` ve `appear` öğelerini çağırır. `appear` sunucuda `AppearanceChannel#appear(data)` öğesini çağırır ve `{ appearing_on: this.appearingOn }` şeklinde bir veri hash'i sağlar. Bu mümkündür çünkü sunucu tarafı kanal örneği, sınıfta bildirilen tüm genel metotları (callback'ler hariç) otomatik olarak açığa çıkarır, böylece bunlara bir aboneliğin `perform` metodu aracılığıyla uzak yordam çağrıları/uzaktan fonksiyon çağrısı olarak erişilebilir.

5. **Sunucu**, `current_user` (`appearance_channel.rb`) tarafından tanımlanan
  bağlantı için görünüm kanalında `appear` action'ı için istek alır. **Sunucu** veri hash'inden `:appearing_on` anahtarına sahip veriyi alır ve `current_user.appear` dosyasına aktarılan `:on` anahtarının değeri olarak ayarlar.

### Örnek 2: Yeni Web Bildirimlerinin Alınması

Görünüm örneği, sunucu işlevselliğinin WebSocket bağlantısı üzerinden istemci tarafı çağrısına maruz bırakılmasıyla ilgiliydi. Ancak WebSocket ile ilgili en güzel şey, bunun iki yönlü bir yol olmasıdır. Şimdi, sunucunun istemciye bir action çağrısında bulunduğu bir örnek gösterelim.

Bu, ilgili yayın akışlarına yayın yaptığınızda istemci tarafı web bildirimlerini tetiklemenizi sağlayan bir web bildirim kanalıdır:

Sunucu tarafı web bildirimleri kanalını oluşturun:

```ruby
# app/channels/web_notifications_channel.rb
class WebNotificationsChannel < ApplicationCable::Channel
  def subscribed
    stream_for current_user
  end
end
```

İstemci tarafı web bildirimleri kanal aboneliğini oluşturun:

```js
// app/javascript/channels/web_notifications_channel.js
// Web bildirimleri gönderme hakkını
// zaten talep ettiğinizi varsayan istemci tarafı.
import consumer from "./consumer"

consumer.subscriptions.create("WebNotificationsChannel", {
  received(data) {
    new Notification(data["title"], { body: data["body"] })
  }
})
```

Uygulamanızın başka bir yerinden bir web bildirim kanalı örneğine içerik yayınlayın:

```ruby
# Uygulamanızda bir yerde,
# belki de bir NewCommentJob'dan bu çağrılır.
WebNotificationsChannel.broadcast_to(
  current_user,
  title: "New things!",
  body: "All the news fit to print"
)
```

`WebNotificationsChannel.broadcast_to` çağrısı, her kullanıcı için ayrı bir yayın adı altında geçerli abonelik bağdaştırıcısının abone ol/yayınla kuyruğuna bir mesaj yerleştirir. ID'si 1 olan bir kullanıcı için yayın adı `web_notifications:1` olur.

Kanala, `web_notifications:1` adresine gelen her şeyi `received` callback'ini çağırarak doğrudan istemciye aktarması talimatı verilmiştir. Argüman olarak geçirilen veri; sunucu tarafındaki yayın çağrısına ikinci parametre olarak gönderilen hash, kablo boyunca yolculuk için JSON olarak kodlanmış ve `received` olarak gelen veri argümanı için paketten çıkarılmış veridir.

### Daha Kapsamlı Örnekler

Bir Rails uygulamasında Action Cable'ın nasıl kurulacağına ve kanalların nasıl ekleneceğine dair tam bir örnek için [rails/actioncable-examples](https://github.com/rails/actioncable-examples) deposuna bakın.

## Yapılandırma

Action Cable'ın iki gerekli yapılandırması (configuration) vardır: Bir abonelik bağdaştırıcısı ve izin verilen istek kökenleri (Not: Allowed request origins; Sunucunun kabul edeceği istemci isteklerinin hangi alan adlarından (origin) gelebileceğini tanımlar.).

### Abonelik Bağdaştırıcısı

Action Cable varsayılan olarak `config/cable.yml` içinde bir yapılandırma dosyası arar. Dosya, her Rails ortamı için bir bağdaştırıcı belirtmelidir. Bağdaştırıcılar hakkında ek bilgi için [Bağımlılıklar](#bagimliliklar) bölümüne bakın.

```yaml
development:
  adapter: async

test:
  adapter: test

production:
  adapter: redis
  url: redis://10.10.3.153:6381
  channel_prefix: appname_production
```

#### Bağdaştırıcı Yapılandırması

Aşağıda son kullanıcılar için mevcut olan abonelik bağdaştırıcılarının bir listesi bulunmaktadır.

##### Asenkron Bağdaştırıcı

Asenkron bağdaştırıcı geliştirme/test için tasarlanmıştır, üretimde kullanılmamalıdır.

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
    Asenkron bağdaştırıcı yalnızca aynı süreç içinde çalışır, bu nedenle kablo güncellemelerini bir konsoldan manuel olarak tetiklemek ve sonuçları tarayıcıda görmek için, bunu <code>bin/rails console</code> ile başlatılan bir terminalden değil, web konsolundan (geliştirme sürecinin içinde çalışan) yapmanız gerekir! Web konsolunun görünmesini sağlamak için herhangi bir action'a veya herhangi bir ERB şablon view'e <code>console</code> ekleyin.
  </div>
</div>

##### Solid Cable Bağdaştırıcısı

Solid Cable bağdaştırıcısı, Active Record kullanan veritabanı destekli bir çözümdür. MySQL, SQLite ve PostgreSQL ile test edilmiştir. `bin/rails solid_cable:install` çalıştırıldığında otomatik olarak `config/cable.yml` kurulacak ve `db/cable_schema.rb` oluşturulacaktır. Bundan sonra, `config/database.yml` dosyasını veritabanınıza göre ayarlayarak manuel olarak güncellemeniz gerekir.Bkz. [Solid Cable Kurulumu](https://github.com/rails/solid_cable?tab=readme-ov-file#installation).

##### Redis Bağdaştırıcısı

Redis bağdaştırıcısı, kullanıcıların Redis sunucusuna işaret eden bir URL sağlamasını gerektirir.
Ek olarak, birden fazla uygulama için aynı Redis sunucusunu kullanırken kanal adı çakışmalarını önlemek için bir `channel_prefix` sağlanabilir. Daha fazla ayrıntı için [Redis Pub/Sub](https://redis.io/docs/manual/pubsub/#database--scoping) belgelerine bakın.

Redis bağdaştırıcısı SSL/TLS bağlantılarını da destekler. Gerekli SSL/TLS parametreleri, YAML yapılandırma dosyasındaki `ssl_params` anahtarında geçirilebilir.

```yaml
production:
  adapter: redis
  url: rediss://10.10.3.153:tls_port
  channel_prefix: appname_production
  ssl_params:
    ca_file: "/path/to/ca.crt"
```

`ssl_params`a verilen seçenekler doğrudan `OpenSSL::SSL::SSLContext#set_params` metoduna aktarılır ve SSL bağlamının herhangi bir geçerli niteliği olabilir.
Mevcut diğer nitelikler için lütfen [OpenSSL::SSL::SSLContext](https://docs.ruby-lang.org/en/master/OpenSSL/SSL/SSLContext.html) belgelerine bakın.

Bir güvenlik duvarının arkasındaki redis bağdaştırıcısı için self-signed sertifikalar kullanıyorsanız ve sertifika kontrolünü atlamayı tercih ediyorsanız, ssl `verify_mode`, `OpenSSL::SSL::VERIFY_NONE` olarak ayarlanmalıdır.

<div class="guide-alert guide-alert-danger">
  <div class="guide-alert-icon">❗</div>
  <div class="guide-alert-content">
    Güvenlik etkilerini kesinlikle anlamadığınız sürece üretime çıkarken <code>VERIFY_NONE</code>kullanılması önerilmez. Redis bağdaştırıcısı için bu seçeneği ayarlamak için yapılandırma <code>ssl_params' { verify_mode: <%= OpenSSL::SSL::VERIFY_NONE %> }</code> şeklinde olmalıdır.
  </div>
</div>

##### PostgreSQL Bağdaştırıcısı

PostgreSQL bağdaştırıcısı, bağlantı için Active Record'un bağlantı havuzunu ve dolayısıyla uygulamanın `config/database.yml' veritabanı yapılandırmasını kullanır.
Bu durum gelecekte değişebilir. [#27214](https://github.com/rails/rails/issues/27214)

<div class="guide-alert guide-alert-warning">
  <div class="guide-alert-icon">📝</div>
  <div class="guide-alert-content">
   PostgreSQL'in <code>NOTIFY</code> (bildirim göndermek için kullanılan komut) üzerinde <a href="https://www.postgresql.org/docs/current/sql-notify.html">8000 byte sınırı</a> vardır ve bu, büyük yüklerle (payload) uğraşırken bir kısıtlama olabilir.
  </div>
</div>

### İzin Verilen İstek Kökenleri

Action Cable yalnızca sunucu yapılandırmasına bir array olarak aktarılan belirtilen kökenlerden (origin) gelen istekleri kabul edecektir. Kökenler, eşleşme için bir kontrolün gerçekleştirileceği string'lerin veya düzenli ifadelerin örnekleri olabilir.

```ruby
config.action_cable.allowed_request_origins = ["https://rubyonrails.com", %r{http://ruby.*}]
```

Herhangi bir kökenden gelen istekleri devre dışı bırakmak ve izin vermek için:

```ruby
config.action_cable.disable_request_forgery_protection = true
```

Varsayılan olarak Action Cable, geliştirme ortamında çalışırken localhost:3000 adresinden gelen tüm isteklere izin verir.

### Tüketici Yapılandırması

URL'i yapılandırmak için HTML layout HEAD'inize [`action_cable_meta_tag`][] çağrısı ekleyin. Bu, genellikle ortam yapılandırma dosyalarında [`config.action_cable.url`][] aracılığıyla ayarlanan bir URL veya yol kullanır.

[`config.action_cable.url`]: ../configuring/#action-cable-url-yapilandirmasi
[`action_cable_meta_tag`]: https://api.rubyonrails.org/classes/ActionCable/Helpers/ActionCableHelper.html#method-i-action_cable_meta_tag

### İş Parçacığı Havuzu Yapılandırması

İş parçacığı havuzu, bağlantı callback'lerini ve kanal action'larını sunucunun ana iş parçacığından ayrı olarak çalıştırmak için kullanılır. Action Cable, uygulamanın iş parçacığı havuzunda aynı anda işlenen iş parçacığı sayısını yapılandırmasına olanak tanır.

```ruby
config.action_cable.worker_pool_size = 4
```

Ayrıca, sunucunuzun iş parçacığınızla en az aynı sayıda veritabanı bağlantısı sağlaması gerektiğini unutmayın. Varsayılan iş parçacığı havuzu boyutu 4 olarak ayarlanmıştır, bu da en az 4 veritabanı bağlantısını kullanılabilir hale getirmeniz gerektiği anlamına gelir. Bunu `config/database.yml` içinde `pool` niteliği aracılığıyla değiştirebilirsiniz.

### İstemci Tarafı Log Kaydı

İstemci tarafı log kaydı varsayılan olarak devre dışıdır. Bunu `ActionCable.logger.enabled` değerini true olarak ayarlayarak etkinleştirebilirsiniz.

```js
import * as ActionCable from '@rails/actioncable'

ActionCable.logger.enabled = true
```

### Diğer Yapılandırmalar

Yapılandırılacak diğer yaygın seçenek ise bağlantı başına log kaydediciye uygulanan günlük etiketleridir. İşte etiketleme sırasında varsa kullanıcı hesabı kimliğini, yoksa "no-account" kullanan bir örnek:

```ruby
config.action_cable.log_tags = [
  -> request { request.env["user_account_id"] || "no-account" },
  :action_cable,
  -> request { request.uuid }
]
```

Tüm yapılandırma seçeneklerinin tam listesi için `ActionCable::Server::Configuration` sınıfına bakın.

## Bağımsız Kablo Sunucularını Çalıştırma

Action Cable, Rails uygulamanızın bir parçası olarak ya da bağımsız bir sunucu olarak çalışabilir. Geliştirme aşamasında, Rails uygulamanızın bir parçası olarak çalıştırmak genellikle iyidir, ancak üretim ortamında bağımsız olarak çalıştırmalısınız.

### Uygulamada

Action Cable, Rails uygulamanızla birlikte çalışabilir. Örneğin, `/websocket` üzerindeki WebSocket isteklerini dinlemek için, bu yolu [`config.action_cable.mount_path`][] olarak belirtin:

```ruby
# config/application.rb
class Application < Rails::Application
  config.action_cable.mount_path = "/websocket"
end
```

Layout'ta [`action_cable_meta_tag`][] çağrılmışsa kablo sunucusuna bağlanmak için `ActionCable.createConsumer()` işlevini kullanabilirsiniz. Aksi takdirde, `createConsumer` için ilk argüman olarak bir yol belirtilir (örneğin, `ActionCable.createConsumer("/websocket")`).

Oluşturduğunuz her sunucu örneği ve sunucunuzun ortaya çıkardığı her iş parçacığı için yeni bir Action Cable örneğiniz olacaktır, ancak Redis veya PostgreSQL bağdaştırıcısı mesajları bağlantılar arasında senkronize tutar.

[`config.action_cable.mount_path`]: ../configuring/#action-cable-baglanti-yolu-yapilandirmasi
[`action_cable_meta_tag`]: https://api.rubyonrails.org/classes/ActionCable/Helpers/ActionCableHelper.html#method-i-action_cable_meta_tag

### Standalone

Kablo sunucuları normal uygulama sunucunuzdan ayrılabilir. Hâlâ bir Rack uygulamasıdır, ancak kendi Rack uygulamasıdır. Önerilen temel kurulum aşağıdaki gibidir:

```ruby
# cable/config.ru
require_relative "../config/environment"
Rails.application.eager_load!

run ActionCable.server
```

Ardından sunucuyu başlatmak için:

```bash
$ bundle exec puma -p 28080 cable/config.ru
```

Bu, 28080 numaralı bağlantı noktasında bir kablo sunucusu başlatır. Rails'e bu sunucuyu kullanmasını söylemek için yapılandırmanızı güncelleyin:

```ruby
# config/environments/development.rb
Rails.application.configure do
  config.action_cable.mount_path = nil
  config.action_cable.url = "ws://localhost:28080" # üretime çıkarken wss:// kullanın
end
```

Son olarak, [tüketiciyi doğru şekilde yapılandırdığınızdan](#tuketici-yapilandirmasi) emin olun .

### Notlar

WebSocket sunucusunun oturuma erişimi yoktur, ancak çerezlere erişimi vardır. Bu, kimlik doğrulama işlemini gerçekleştirmeniz gerektiğinde kullanılabilir. Bu [makalede](https://greg.molnar.io/blog/actioncable-devise-authentication/) Devise ile bunu yapmanın bir yolunu görebilirsiniz .

## Bağımlılıklar

Action Cable, yayınla/abone ol iç bileşenlerini işlemek için bir abonelik bağdaştırıcısı arabirimi (interface) sağlar. Varsayılan olarak; asenkron, satır içi, PostgreSQL ve Redis bağdaştırıcıları dahil edilmiştir. Yeni Rails uygulamalarında varsayılan bağdaştırıcı asenkron (`async`) bağdaştırıcısıdır.

Ruby tarafı [websocket-driver](https://github.com/faye/websocket-driver-ruby), [nio4r](https://github.com/celluloid/nio4r) ve [concurrent-ruby](https://github.com/ruby-concurrency/concurrent-ruby) üzerine inşa edilmiştir.

## Dağıtım

Action Cable, WebSockets ve iş parçacıklarının bir kombinasyonu tarafından desteklenmektedir. Hem framework tesisatı hem de kullanıcı tarafından belirtilen kanal çalışması Ruby'nin yerel iş parçacığı desteği kullanılarak dahili olarak ele alınır. Bu, herhangi bir iş parçacığı güvenliği günahı işlemediğiniz sürece mevcut tüm Rails modellerinizi sorunsuz bir şekilde kullanabileceğiniz anlamına gelir.

Action Cable sunucusu Rack socket hijacking API'ını uygular, böylece uygulama sunucusunun çok iş parçacıklı olup olmadığına bakılmaksızın bağlantıları dahili olarak yönetmek için çok iş parçacıklı bir modelin kullanılmasına izin verir.

Bu sebeple Action Cable, Unicorn, Puma ve Passenger gibi popüler sunucularla çalışır.

## Test

Action Cable işlevselliğinizi nasıl test edeceğinize ilişkin ayrıntılı talimatları [Test](../testing/#action-cable-test) kılavuzu bölümünde bulabilirsiniz.
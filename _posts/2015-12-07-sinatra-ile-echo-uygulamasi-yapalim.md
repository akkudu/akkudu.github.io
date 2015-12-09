---
layout: post
date:   2015-12-07
title:  "Sinatra ile echo uygulaması yapalım"
categories: turkce
author: Andaç Karay Kudu
---

[Bir önceki yazım](http://akkudu.com/turkce/sinatra-ile-tanistiniz-mi.html)da
sinatradan bahsetmiştim. Sinatra'nın sitesindeki örnekten yola çıkarak
bir [hello world](https://en.wikipedia.org/wiki/Hello_world_program) web
uygulaması hazırlamıştık.

Sırada echo uygulaması var. Yani web uygulamasının sayfadaki formu
kullanarak gönderdiğimiz mesajı bize göstermesini istiyoruz.

Öncelikle bir forma ihtiyacımız var. Sade bir form işimizi görecektir.

{% highlight html %}
<form method="POST">
  <input type="text" name="message">
</form>
{% endhighlight %}

Formu [HTML](https://en.wikipedia.org/wiki/HTML) dili ile hazırladığımıza
göre HTML dilinin kurallarına uymaya da özen gösterelim.

{% highlight erb %}
<!DOCTYPE html>
<html>
  <head>
    <title>echo</title>
  </head>
  <body>
    <form method="POST">
      <input type="text" name="message">
    </form>
  </body>
</html>
{% endhighlight %}

Tabi ki gönderdiğimiz mesajı da görmek istiyoruz.

HTML kodu içerisinde ruby kodu çalıştırmak için
[erb](https://en.wikipedia.org/wiki/ERuby) (embedded / gömülü ruby)
etiketlerine (tag) ihtiyacımız var. HTML içerisinde <% ve %> etiketleri
arasına yazdığımız her şey ruby dili ile yorumlanır. Eğer kodun
çıktısını görmek istiyorsak <% yerine <%= kullanmamız yeterli
olacaktır.

{% highlight erb %}
<!DOCTYPE html>
<html>
  <head>
    <title>echo</title>
  </head>
  <body>
    <form method="POST">
      <input type="text" name="message">
    </form>
    <p><%= @message %></p>
  </body>
</html>
{% endhighlight %}

Sinatra, [tilt](https://github.com/rtomayko/tilt) kütüphanesi sayesinde
pek çok şablon dilini [destekliyor](http://www.sinatrarb.com/intro.html#Views%20/%20Templates).
Tilt, Sinatranın gem pakedinin bağımlılıkları içerisinde olduğundan
sinatra ile birlikte kuruluyor. Bu nedenle ek bir işlem yapmamıza gerek yok.

Biz erb kullanmaya karar verdiğimiz için erb desteğini programımıza
dahil edeceğiz.

Artık programımızın koduna geçebiliriz.

{% highlight rb %}
# echo.rb
require 'sinatra'
require 'tilt/erb'

get '/' do
  erb :index
end

post '/' do
  @message = params[:message]
  erb :index
end
{% endhighlight %}

Formumuzda methodu POST olarak belirlediğimiz için gelen POST isteğini
karşılamak için bir post metoduna ihtiyacımız var (HTTP ve istek
metodları hakkında bilgi edinmek için wikipediadaki
[ilgili başlığa](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods)
bir göz atabilirsiniz).

Sinatra, web isteklerinden gelen değerlere erişebilmemiz için params
adında bir değişene sahip. Bu değerlere params değişkeni içerisinden
adları ile ulaşabiliyoruz.

Dikkat ederseniz form içerisindeki giriş (input) alanımızın adı
"message" ve bu alana yazdığımız değere de params[:message] ile erişiyoruz.
Bu mesajı şablon içerisine gömdüğümüz ruby koduna aktarabilmek için
@message adındaki bir değişkene atıyoruz.

İstersek HTML şablonumuzu "views" dizini içerisine "index.erb" adıyla
kaydedip kullanabiliriz. Bu defa (uygulamayı basit tutmak adına) ayrı
bir şablon dosyası kullanmak yerine, şablonu program kodunun sonuna
ekleyeceğiz.

{% highlight rb %}
# echo.rb
require 'sinatra'
require 'tilt/erb'

get '/' do
  erb :index
end

post '/' do
  @message = params[:message]
  erb :index
end

__END__
@@ index
<!DOCTYPE html>
<html>
  <head>
    <title>echo</title>
  </head>
  <body>
    <form method="POST">
      <input type="text" name="message">
    </form>
    <p><%= @message %></p>
  </body>
</html>
{% endhighlight %}

Uygulamamızı çalıştıralım.

{% highlight bash %}
$ ruby echo.rb
{% endhighlight %}

#### Sonuç

Tabii ki [http://localhost:4567](http://localhost:4567) adresinden
echo uygulamamıza erişebiliriz.

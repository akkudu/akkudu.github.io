---
layout: post
date:   2015-12-08
title:  "Sinatra uygulamamıza hafıza ekleyelim"
categories: turkce
author: Andaç Karay Kudu
---

Bir önceki yazımda sinatra ile bir [echo uygulaması](/turkce/sinatra-ile-echo-uygulamasi-yapalim.html)
yapmıştık. Bu defa uygulamamızın mesajları hatırlamasını sağlayacağız.

Bunun için gelen mesajları bir dizide saklayacağız. Mesajlar dizini de
sinatra [ayarlar](http://www.sinatrarb.com/intro.html#Configuration)ında
belirteceğiz.

{% highlight rb %}
# memory.rb
require 'sinatra'
require 'tilt/erb'

configure do
  set :memory, []
end

get '/' do
  @messages = settings.memory
  erb :index
end

post '/' do
  @messages = settings.memory << params[:message]
  redirect '/'
end

__END__
@@ index
<!DOCTYPE html>
<html>
  <head>
    <title>messages</title>
  </head>
  <body>
    <form method="POST">
      <input type="text" name="message">
    </form>
    <p>
      <ul>
        <% @messages.each do |message|  %>
          <li><%= message %></li>
        <% end %>
      </UL>
    </p>
  </body>
</html>
{% endhighlight %}

Uygulamamızı çalıştıralım.

{% highlight bash %}
$ ruby memory.rb
{% endhighlight %}

#### Sonuç

[http://localhost:4567](http://localhost:4567) adresindeki uygulamamız
gönderdiğimiz mesaları hatırlıyor.

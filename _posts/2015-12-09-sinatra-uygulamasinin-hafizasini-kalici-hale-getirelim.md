---
layout: post
date:   2015-12-09
title:  "Sinatra uygulamamızın hafızasını kalıcı hale getirelim"
categories: turkce
author: Andaç Karay Kudu
---

Daha önce [uygulamamıza hafıza eklemiştik](/turkce/sinatra-uygulamamiza-hafiza-ekleyelim.html) .
Fakat programımız her durdurduğunda hafızası siliniyor. Hafızanın kalıcı
olmasını sağlamak için bir dosya kullanacağız. Mesajları da [JSON](http://json.org/)
formatında saklayacağız.

{% highlight rb %}
# file_memory.rb
require 'sinatra'
require 'tilt/erb'
require 'json'

configure do
  set :memory, 'messages.json'
end

get '/' do
  @messages = JSON.parse(open(settings.memory).read)
  erb :index
end

post '/' do
  messages = JSON.parse(open(settings.memory).read)
  messages << JSON.parse( { body: params[:message] }.to_json )
  File.write settings.memory, JSON.pretty_generate(messages, indent: '  ')
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
          <li><%= message["body"] %></li>
        <% end %>
      </ul>
    </p>
  </body>
</html>
{% endhighlight %}

Uygulama çalıştığında mesajları okuması için bir hafıza dosyasına
ihtiyaç duyuyor. Boş dizi içeren bir dosya hazırlayalım.

{% highlight bash %}
$ echo [] > messages.json
{% endhighlight %}

Yukarıdaki komutu messages.json dosyasını boşaltmak için de kullanabiliriz.

Uygulamamızı çalıştıralım

{% highlight bash %}
$ ruby file_memory.rb
{% endhighlight %}


#### Sonuç

[http://localhost:4567](http://localhost:4567) adresinde çalışan
uygulamamız artık mesajlarımızı unutmuyor.

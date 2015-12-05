---
layout: post
date:   2015-12-06
title:  "Sinatra ile tanıştınız mı?"
categories: turkce
author: Andaç Karay Kudu
---

[Sinatra](http://www.sinatrarb.com/), web uygulamaları yazmak için basit
ve kullanışlı bir araç.

Sinatra projesinin web sitesinin açılış sayfasındaki örneğe göre üç
adımda bir web uygulaması hazırlayabiliyoruz.

#### 1. Adım: Uygulamayı yazalım.

{% highlight rb %}
# hi.rb
require 'sinatra'

get '/' do
  "Hello World!"
end
{% endhighlight %}

#### 2. Adım: Sinatra kuralım.

{% highlight bash %}
$ gem install sinatra
{% endhighlight %}

#### 3. Adım: Uygulamayı çalıştıralım.

{% highlight bash %}
$ ruby hi.rb
== Sinatra has taken the stage ...
>> Listening on 0.0.0.0:4567
{% endhighlight %}

#### Sonuç

[http://localhost:4567](http://localhost:4567) adresinden uygulamaya
ulaşabiliriz.

---
layout: post
date:   2015-12-10
title:  "Uygulamalarımızın bağımlılıklarını yönetelim"
categories: turkce
author: Andaç Karay Kudu
---

Daha önce sinatra ile bir kaç örnek uygulama hazırladık.
Bu uygulamalarımız sinatra başta olmak üzere çeşitli kütüpheneler
kullanıyorlar. Bu kütüphaneler, uygulamalarımızın çalışması için ihtiyaç
duyduğu bağımlılıklardır. Bu yazıda, uygulamalarımızın bağımlılıklarını
nasıl yönettiğimize bir göz atacağız.

Ruby dilinde kütüphaneler gibi dış kaynaklar [rubygem](https://rubygems.org/) formatında
paketleniyor. Bu paketleri gem komutu ile kurup kaldırabiliyoruz.

Uygulamamız geliştikçe ihtiyaç duyduğumuz kütüphanelerin sayısı da haliyle
artıyor. Bu paketlerin tek tek elle kurulması, uygulamayı başka bir
bilgisayarda çalıştırmak istediğimizde bize sıkıntılı anlar yaşatabilir.

Ayrıca farklı uygulamalar, kimi paketlerin farklı versiyonları ile
çalışıyor olabilir. Diyelik ki; bir uygulamamız, bir paketin yeni
versiyonu ile uyumlu değil. Böyle bir durumda söz konusu uygulamanın,
paketin eski versiyonunu kullanmasını isteyebiliriz.

Bunlar ve burada değinmediğimiz diğer bağımlılık problemlerinin çözümü
için ise [bundler](http://bundler.io/) kullanıyoruz.

Öncelikle, çalışma ortamımıza bundler paketini kuralım.

{% highlight bash %}
$ gem install bundler
{% endhighlight %}

Artık bundler komutunu kullanabiliriz (Bundler paketi ile birlikte
bundler ve bundle adlarında iki uygulama geliyor, bu iki uygulama
arasında isimleri dışında hiç bir fark yok).

Bundler kullanmak için programımızın olduğu dizinde, bağımlılıkları
listelediğimiz, Gemfile adında bir dosyaya ihtiyacımız var. Bundler
komutu ile bu dosyayı oluşturabiliriz.

{% highlight bash %}
$ bundle init
Writing new Gemfile to /codes/blog_codes/file_memory/Gemfile
{% endhighlight %}

Gemfile dosyasının içeriğine baktığımızda, paketlerin nereden kurulacağını
(kaynağını) gösteren bir satır ile başladığını görüyoruz. Bu satırın
altına uygulamamızın ihtiyaç duyduğu paketleri ekleyebiliriz.

{% highlight bash %}
# A sample Gemfile
source "https://rubygems.org"

# gem "rails"
{% endhighlight %}

Bu dosyayı bir önceki programımız için düzenlemeden önce küçük bir
uyarıya ihtiyaç var. JSON kütüphanesi, [ruby standart kütüphaneleri](http://ruby-doc.org/stdlib-2.2.3/)
arasında yer aldığı için ruby kurulumu sırasında kuruluyor, bu nedenle
Gemfile dosyasına yazmak zorunda değiliz. Fakat biz, json kütüphanesinin
güncel sürümünü kullanmak istediğimiz için Gemfile dosyasına ekliyoruz.

{% highlight bash %}
source "https://rubygems.org"

gem "sinatra"
gem "json"
{% endhighlight %}

Artık bundler yardımı ile paketleri kurabiliriz.

{% highlight bash %}
$ bundle install
Resolving dependencies...
Using json 1.8.3
Using rack 1.6.4
Using rack-protection 1.5.3
Using tilt 2.0.1
Using sinatra 1.4.6
Using bundler 1.10.6
Bundle complete! 2 Gemfile dependencies, 6 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed.
{% endhighlight %}

Komutun çıktısında, uygulamamızın bağımlılıkları olan sinatra ve json
kütüphanelerinin yanısıra, bu kütüphanelerin bağımlılıklarının da
kurulduğunu görüyoruz.

Bu komut çalıştırıldığında, ilgili kütüphaneleri ve bu kütüphanelerinin
bağımlılıklarını bir ağaç yapısında saklandığı Gemfile.lock adında bir de
dosya oluşturuyor ve bu bağımlılık ağacına göre gerekli kütüphaneleri
kuruyor. Bu dosya sayesinde, "bundle install" komutu tekrar çalıştırıldığında,
bağımlılıkları yeniden analiz etmekle uğraşmıyor.

Gemfile.lock dosyasını ve kurulu paketleri güncellemek için ise
"bundle update" komutunu kullanıyoruz.

Artık "bundle exec" komutu ile sistemde yüklü diğer paketlerden yalıtılmış,
yalnızca Gemfile içerisinde belirttiğimiz paketlerin olduğu bir ortamda
çalışabiliriz.

{% highlight bash %}
$ bundle exec gem list

*** LOCAL GEMS ***

bundler (1.10.6)
json (1.8.3)
rack (1.6.4)
rack-protection (1.5.3)
sinatra (1.4.6)
tilt (2.0.1)
{% endhighlight %}

Uygulamamızı bu yalıtılmış ortamda çalıştırmak için de "bundle exec"
komutunu kullanıyoruz.

{% highlight bash %}
$ bundle exec ruby file_memory.rb
{% endhighlight %}

Bundler hakkında daha fazla bilgi almak için [bundler.io](http://bundler.io/)
adresindeki belgelerden faydalanabilirsiniz.

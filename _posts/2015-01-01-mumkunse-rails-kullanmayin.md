---
title: Mümkünse rails kullanmayın!
author: Andaç Karay Kudu
layout: post
language: Türkçe
categories: [turkce]

github_issue_id: 15
---

Geçtiğimiz ay, bir meslekdaşım [MRI][MRI]'ın üzerinde çalıştıkları iş
için yavaş kaldığından ve bir çözüm önerisi aradığını söyledi. Aynı
soru, şu yada bu biçimde, sıklıkla karşıma çıkıyor; "[Ruby][Ruby] çok
yavaş(mış), sen ne önerirsin?". Genellikle verdiğim cevaplarda vurguladığım
noktalar şunlar oluyor; "Ruby, sadece [Ruby on Rails][Rails] değildir"
ve "mümkünse rails kullanmayın!".

Tabi burada sadece rails'i de kastetmiyorum, diğer dillerdeki rails
benzeri [MVC][MVC] tabanlı [web geliştirme alt yapılarından][WAF] da uzak
durulması gerektiğini söylüyorum. Neden böyle düşündüğümü, bu
sistemlerin atası sayabileceğimiz rails üzerinden açıklamaya çalışayım.

MVC, sistemlere kullanıcı arabirimleri eklemek üzere, mikro ölçekte
kullanılmak üzere tasarlanmış bir tasarım desenidir. Rails, her
ne kadar MVC deseni ile tasarlanmış olsa da, hepimizin kabul edeceği
gibi, kesinlikle mikro değil. Ayrıca nesne odaklı programcılığın
temel prensiplerinin pek çoğuna aykırı bir tasarımı var. Bu gibi
nedenler, rails'in aşırı hantal ve oldukça tehlikeli bir araç haline
gelmesine neden oluyor. Gelin bu hantallığa ve tehlikelere kısaca bir
göz atalım.

Yüksek seviyeli programlama dillerindeki her komut işlemcinin anlayacağı
dile çevrildiğinde birden fazla komuta dönüşüyor. Ve hepimizin bildiği
gibi bu dillerde, bir satırda birden fazla komut kullanabiliyoruz.
Yani, kodumuzun uzunluğu işlemci üzerindeki yükün miktarıni belirliyor.

Peki, bunun rails ile bağlantısı ne? Eğer kafanızda böyle bir soru varsa,
size rails kodunun kaç satırdan oluştuğuna bir bakmanızı tavsiye ederim.
Sadece rails'in hafızaya yüklenmesi için gerekli işlem gücünü ve süreyi
bir göz önüne getirirsek bu yükün miktarını ve rails kullanmanın neden
iyi bir fikir olmadığını daha rahatlıkla anlayabiliriz.

Bunlara rağmen rails'i kullanmanın bazı avantajları olduğunu düşünen
pek çok rails kullanıcısı olduğu da bir gerçek. Bu kullanıcıların
sıklıkla vurguladıkları nokta da, rails ile gelen kod üreteçleri
(generators). Bu kod üreteçleri ile hızlı bir şekilde [prototip][Prototype]
oluşturulabiliyor ve bu protipleri müşteri yada kullanıcılar ile somut
bir uzlaşı zemini olarak kullanabiliyorsunuz. Kulağa güzel geliyor ama
bakalım işin aslı gerçekten de böyle mi?

Bildiğiniz gibi, prototipler [fikirlerin kanıtlanması][PoC]nda
kullanılırlar. Prototip oluşturmak, özellikle de somut nesneler
tasarlarken, oldukça önemli bir çalışma pratiği sağlar. Hazırlanan
prototipler birbirleri ile karşılaştırılır, öne çıkan özellikler
belirlenir ve bu özellikler yeni bir tasarımda birleştirilerek ürüne
doğru ilerleriz.

Yazılım üretiminde ise durum biraz farklı. Yazılım pek çoğumuzun
sandığının aksine bir mal değildir. Yazılım kullanıldığı ve
kullanılabilir olduğu sürece fayda üretir. Bu nedenle yazılımın
değişikliklere uyum sağlaması için sürekli olarak güncellenmesi,
değiştirilmesi ve genişletilmesi gereklidir. Yazılımın doğasındaki bu
süreklilik ve esneklik, birden çok prototip üretmek yerine [değişen ve
gelişen tek bir prototip][EvolutionaryPrototyping] ile çalışmamıza da
imkan sağlar. Bu prototip gelişir ve ürün haline gelir.

Bu tek prototipin ürün haline geleceği gerçeği ile baktığımızda,
rails'in kod üreteçleri ile türettiğimiz kodların sanıldığının aksine,
yarardan çok zarara yol açtığını kolayca görebiliriz. Kod üreteçleri ile
üretilen kod blokları, temel bir koddan farklı değişken isimlerine göre
türetilir ve aslında birbirinin aynı olan pek çok kod bloğuna sahip
olmamıza neden olurlar.

Sistematik üretim, yan etki olarak sistematik hata kavramını da
beraberinde getirir. Yani sadece aynı kodun birden fazla kopyasına
sahip olmakla kalmaz, hataların da birden çok kopyasına sahip
oluruz ve bu hataları gidermek için aynı düzeltmeyi tekrar tekrar
uygulamamız gerekir.

Daha da kötüsü, testlerimiz de bu üreteçler tarafından üretilir ve
[TDD][TDD] gibi çok önemli bir yöntemi uygulamamıza izin vermezler.
Bu testler, hataları yakalamak konusunda da pek başarılı değildirler ve
sahte bir güvenlik duygusu yaratırlar. Bu sahte güvenlik duygusu, ürün
üzerinde çalışan ekip üyelerinin içgüdüsel olarak ürettikleri ürüne
güvenmemelerine; daha çalışmalarını tamamlamadan yeni bir ürün
(sürüm/version) üzerinde düşünmeye başlamalarına ve üzerinde
çalıştıkları işe yabancılaşmalarına sebep olur.

Buraya küçük bir not düşmek istiyorum. Yazılımın sürekli doğası gereği,
yazılım üretiminde kullanılan sürümler yeni ürünleri nitelemezler. Tam
tersine, mevcut ürünün geldiği aşamayı ve bu aşamaya gelirken geçirdiği
değişiklik setlerini tanımlamakta kullanılırlar.

Yukarıda saydığım bu gibi nedenler bizi çok daha büyük bir tehlikenin
kucağına da atar. Bu tehlike, bir fikir olarak başlayan ve gelişip ürün
haline gelen prototipin kullanıcıya ulaşması ve ticari başarı
kazanmasıdır. Ticari başarı nasıl bir tehlike getirebilir irdeleyelim.

Bir ürünün ticari başarı kazanması, kullanıcı sayısının artması anlamına
gelir. Kullanıcı sayısının artması, performans problemleri olan bir
altyapı ile ürünün çalışmasında kesintilere neden olur. Acil olarak,
[kodun elden geçirilmesi][Code Refactoring], problemlerin ortadan
kaldırılması, test ve dokümantasyon gibi eksiklerin tamamlanması, kısaca
[teknik borcun][Technical Debt] kapatılması gereklidir. Eğer teknik
borç hızlıca kapatılmaz ise mevcut kullanıcılar rakip alternatiflere
yönelirler ve ticari başarımız ticari başarısızlığımıza dönüşür.

Üründen memnun olan kullanıcılar ise, üründen daha fazla faydalanabilmek
amacı ile ürün üzerinde bir takım değişiklikler ve genişletmeler talep
etmeye başlayacaklardır. Bu talepler, teknik borç üzerine odaklanmış
ekibin üzerindeki iş yükünün ve stresin daha da artmasına neden olur.
Stres ve ağır iş yükü, yeni hatalara zemin hazırlar ve ürünün kalitesini
hızla düşürür. Hatta çoğu zaman ekip üyelerinin bazılarının yada
tamamının ekipten ayrılmasına bile neden olabilir.

Ayrılan ekip üyelerinin yerlerini doldurmak ve ekibimizi büyütmeye
kalktığımızda ise başka bir problemle daha karşılaşırız. Ekibe yeni
katılan üyeler, doğal olarak, ürüne ve ürünün arkasındaki fikre
yabancıdırlar. Ayrıca, fikrin ürün aşamasına gelmesi sürecince bir
arada çalışan ekibin kültürünü ve dinamiklerini de anlamakta güçlük
çekerler. Ağır iş yükü altında çalışan ekibin eski üyeleri ise,
yeni üyeler ile birlikte çalışarak, onları ürüne, fikre, sahip oldukları
ekip kültürüne ve ekip içi dinamiklere alıştırmak için gerekli zamanı,
enerjiyi ve isteği kendilerinde bulamazlar.

Bunun sonucu olarak, eski ve yeni üyeler arasında uyum problemleri
ortaya çıkar. Yeni üyeler, mevcut kodlar üzerinde çalışmakta
zorlanırlarken, ürettikleri kodlar da ekibin üzerinde önceden uzlaşılmış
olan tasarım ve uygulama pratiklerine uymaz. Yeni hatalarla birlikte
mevcut iş yükü daha da artar. Ekip, kod ve ürün üzerindeki kontrolünü
kaybeder. Kırılması oldukça zor ve maliyetli olan bir kısır döngüye
girilir, mutlak bir sona doğru gittikçe hızlanan adımlarla ilerlemeye
başlarız...

Bütün bu sorunlara ve tehlikelere rağmen rails kullanımını önerdiğim
durumlar olmuyor da değil. Mesela, işiniz yazılım değil ise, hobi
projeleriniz üzerinde çalışırken keyifle rails kullanabilirsiniz. Yada
bir yazılımcı olarak henüz ruby ile tanışmadıysanız, rails kullanarak
ruby'nin [söz dizimine][syntax] bir göz atabilirsiniz. Benzer şekilde,
şirketinizin asıl işi yazılım değil ve yukarıdaki sorunlar ile
karşılaştığınızda kullanabileceğiniz yeterli kaynaklarınız varsa, asıl
işinizi desteklemek üzere rails ile kısa ömürlü ürünler üretebilirsiniz.

Şirketinizin işi yazılım ise ve nitelikli bir ekiple çalışma imkanına
sahip değilseniz, yeterli kaynağınız yok yada kaynaklarınızı ekibinize
aktarmak istemiyorsanız gene rails ile çalışabilirsiniz. Ama size daha
iyi bir tavsiyem var: Yazılım işinden uzak durun! Çünkü, yazılım işinde
en önemli nokta yazılımın kalitesidir. Nitelikli yazılım, ancak
nitelikli bir ekip tarafından üretilebilir. Böyle bir ekibiniz olmadan
girişeceğiniz her macera, uzun vadede size zaman, para ve prestij
kaybettirecek; sektöre ve müşterilerinize de zarar verecektir. Bunun
yanı sıra, tüm bu sorunlarla uğraşmak fiziksel ve ruhsal sağlığınız
üzerinde oldukça olumsuz etkiler bırakacaktır.

Kendi adıma, yukarıda sadece bir kısmından bahsettiğim tüm bu sorun ve
riskleri oldukça gereksiz buluyorum ve bunlardan kaçınmakiçin rails
kullanmıyorum. Size de tavsiyem bu yönde, **Mümkünse rails kullanmayın!**

[MRI]: https://en.wikipedia.org/wiki/Ruby_MRI
[Rails]: https://en.wikipedia.org/wiki/Ruby_on_Rails
[Ruby]: https://en.wikipedia.org/wiki/Ruby_%28programming_language%29
[MVC]: https://en.wikipedia.org/wiki/Model-View-Controller
[WAF]: https://en.wikipedia.org/wiki/Web_application_framework
[LOC]: https://en.wikipedia.org/wiki/Source_lines_of_code
[PoC]: https://en.wikipedia.org/wiki/Proof_of_concept
[TDD]: https://en.wikipedia.org/wiki/Test-driven_development
[Code Refactoring]: https://en.wikipedia.org/wiki/Code_refactoring
[Technical Debt]: https://en.wikipedia.org/wiki/Technical_debt
[Syntax]: https://en.wikipedia.org/wiki/Syntax
[Prototype]: https://en.wikipedia.org/wiki/Prototype
[EvolutionaryPrototyping]: https://en.wikipedia.org/wiki/Software_prototyping#Evolutionary_prototyping

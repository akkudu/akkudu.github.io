---
title: Raspberry Pi üzerine Arch Linux ARM Kurulumu
author: Andaç Karay Kudu
layout: post
language: Türkçe
categories: [turkce]
tags: raspberry-pi, arch linux
description: Raspberry Pi üzerine Arch Linux Arm Kurulumu

github_issue_id: 17
---

![Raspberry Pi Logo](/img/raspberry_pi-logo.jpg)
Geçtiğimiz son iki haftayı [Raspberry Pi](http://www.raspberrypi.org/)
ile oynayarak geçirdim. Amacım, raspberry pi üzerindeki IO portlarına
bağladığım bileşenlere ruby ile erişerek, tanımladığım olaylar
gerçekleştiğinde bana görsel ve işitsel uyarılar vermesini sağlamak.
Bunun için öncelikle favori linux dağıtımım olan
[Arch Linux](http://archlinux.org)'un
[ARM versiyonu](http://archlinuxarm.org/)nu kurdum.

Öncelikle çalışmalarımda kullandığım cihazları sağlayan
[İzmir Hacker Space](http://izmirhs.org/)'ten Berk'e ve
[Oğuz Yarımtepe](https://twitter.com/oguzy)'ye teşekkür ederim.

Şimdi adım adım raspberry pi'a nasıl arch linux kurduğumu anlatayım:

### Adım 1 : Kurulum imajının indirilmesi ve kontrolü

~~~
  $ wget http://archlinuxarm.org/os/ArchLinuxARM-rpi-latest.tar.gz
  $ wget http://archlinuxarm.org/os/ArchLinuxARM-rpi-latest.tar.gz.md5

  $ md5sum ArchLinuxARM-rpi-latest.tar.gz
  $ cat ArchLinuxARM-rpi-latest.tar.gz.md5
~~~

Hesaplanan md5 toplamının, md5 dosyasındaki toplamla aynı olup olmadığını
kontrol ederek dosyanın doğru geldiğinden emin oluyorum.

### Adım 2 : Hafıza Kartının Hazırlanması

Rasberry Pi, açılış yapabilmek için UEFI sistemlere benzer şekilde bir
FAT bölümüne ihtiyaç duyuyor. Bu nedenle açılış (/boot) ve kök (/) için
iki ayrı dosya sistemi oluşturacağım.

Bölümlendirme işlemi için fdisk programını kullanacağım ve root
yetkilerine ihtiyacım olacak. Sonrasında hafıza kartına
kopyalayacağım dizin ve dosyaların sahiplikleri ile ilgili problem
yaşamamak için diğer işlemleri de root olarak yapmaya devam edeceğim.
Root olarak çalışırken dikkatli olmakta fayda var.

~~~
  $ sudo su
  # fdisk /dev/mmcblk0
~~~

Fdisk programını kullanırken girdiğim komutlar ve açıklamaları şunlar:

~~~
  o       (Yeni bir bölümlendirme tablosu oluştur)
  n       (Yeni bir bölüm oluştur)
  p       (Yeni bölüm birincil/primary olsun)
  1       (Yeni bölümün bölüm numarası 1 olsun)
  <enter> (Yeni bölüm boş alanın başından başlasın)
  +32M    (Yeni bölümün büyüklüğü 32MB olsun)
  t       (Yeni Bölümün tipi değişsin)
  c       ("W95 FAT32 (LBA)" tipinde olsun)
  n       (İkinci bir yeni bölüm oluşsun)
  p       (Bu bölüm de birincil/primary olsun)
  2       (Bölüm numarası 2 olsun)
  <enter> (Bölüm boş alanın başından başlasın)
  <enter> (Bölüm boş alanın sonunda bitsin)
  w       (Değişiklikleri yaz ve çık)
~~~

Sırasıyla bölümlerin dosya sistemlerini oluşturuyorum.

Açılış (/boot) bölümü için vfat,

~~~
  # mkfs.vfat /dev/mmcblk0p1
~~~

Kök (/) bölümü için ise ext4 kullanacağım.

~~~
  # mkfs.ext4 /dev/mmcblk0p2
~~~

### Adım 3 : ArchLinuxARM'ın dosya sistemine aktarılması

Hafıza kartında oluşturduğum dosya sistemlerini bağlayabilmek için bir
dizine ihtiyacım var.

~~~
  # mkdir /tmp/io
~~~

Önce kök (/) dosya sistemini bağlıyorum.

~~~
  # mount /dev/mmcblk0p2 /tmp/io
~~~

Açılış (/boot) dosya sisteminin, kök (/) dosya sistemi üzerine bağlı
olması gerekiyor.

~~~
  # mkdir /tmp/io/boot
  # mount /dev/mmcblk0p1 /tmp/io/boot
~~~

Artık indirdiğim Arch Linux disk imajını hafıza kartına açabilirim:

~~~
  # bsdtar -xpf ArchLinuxARM-rpi-latest.tar.gz -C /tmp/io
  # sync
~~~

Her iki bölümü de üzerinde çalıştığım sistemden ayırıyorum.

~~~
  # umount /tmp/io/boot /tmp/io
~~~

Artık root yetkilerine ihtiyacım kalmadı, exit yazarak yada Ctrl+D
tuşlarına basarak çıkabilirim.

Hafıza kartını bilgisayarımdan çıkarıp, Raspberry Pi'a taktıktan sonra,
ethernet kablosunu bağlıyorum ve cihaza 5V güç veriyorum.

Ben, masamda fazladan kalabalık olmaması için cihaza ssh üzerinden
erişmeyi tercih ediyorum. Dilerseniz, klavye, fare ve monitör bağlayarak
da cihazı kullanabilirsiniz.

### Adım 4 : Yeni sistemin uzaktan yapılandırılması

Ssh ile raspberry pi'a bağlanabilmek için cihazın IP adresine ihtiyacım
var. Benim ağımdaki DHCP sunucusu (modemim) yeni cihaza 192.168.0.10
adresini verdi. Sizde ağınızdaki DHCP sunucusundan raspberry pi'a
atadığı ip adresini öğrenebilirsiniz.

~~~
  $ ssh root@192.168.0.10
~~~

Root kullanıcısının ön tanımlı şifresi root olarak belirlenmiş. Sisteme
giriş yapar yapmaz hemen bu şifreyi değiştiriyorum.

~~~
  # passwd
~~~

Makinanın adını da io olarak değiştiriyorum, siz istediğiniz ismi
seçebilirsiniz.

~~~
  # echo io > /etc/hostname
~~~

Makinanın zaman dilimi ayarını yapıyorum.

~~~
  # ln -f /usr/share/zoneinfo/Turkey /etc/localtime
~~~

Sistemi güncelliyorum.

~~~
  # pacman -Syu
~~~

Sistem güncellenirken, açılışta kullanılan yapılandırma dosyalarının
yeni versiyonlarının geldiğini, fakat mevcut ayarlarımın bozulmaması
için **.pacnew** uzantısı ile kayıt edildiğini söyleyen bir uyarıyla
karşılaştım. Sistemimde henüz böyle bir yapılandırma ayarı olmadığı için
gönül rahatlığıyla bu yeni yapılandırma dosyalarını kullanabilirim.

~~~
  # mv /boot/config.txt.pacnew /boot/config.txt
  # mv /boot/cmdline.txt.pacnew /boot/cmdline.txt
~~~

Raspberry Pi üzerinde bulunan ses çıkışını kullanmak için alsa
paketlerine ve bcm2835 modülüne ihtiyaç var.

~~~
  # pacman -S alsa-firmware alsa-utils alsa-lib alsa-plugins
  # sudo modprobe snd-bcm2835
  # echo snd-bcm2835 > /etc/modules-load.d/snd-bcm2835.conf
~~~

Sonucu kontrol etmek için alsamixer'i kullanabilirim.

~~~
  # sudo alsamixer
~~~

Arch Linux'un kullanıcı deposundaki ([AUR](http://aur.archlinux.org/))
paketlerden faydalanmak için packer'a ihtiyacım olacak.

~~~
  # pacman -S packer
~~~

AUR'da yer alan kanak kod paketlerini yada kendi yazdığım programları
derleyebilmek için ise temel geliştirme paket setini kuruyorum.

~~~
  # pacman -S base-devel
~~~

### Adım 5: Kullanıcı ayarları

Root kullanıcısı sistem üzerinde her türlü yetkiye sahiptir. Root olarak
yanlış girdiğim bir komut ile sisteme zarar verebilirim. Bu nedenle
kendime yeni bir kullanıcı açacağım. İhtiyaç duyduğumda root yetkilerine
erişebilmek için de sudo programını kullanacağım.

~~~
  # pacman -S sudo
~~~

Sudo programını her çalıştırdığımda şifremi yazmak istemiyorum.
Bu nedenle etc dizini içinde bulunan sudoers dosyasında wheel grubunun
sudo komutunu şifresiz olarak kullanabilmesi için gerekli ayarı
yapacağım ve kendi kullanıcımı da bu gruba dahil edeceğim.

~~~
  # nano /etc/sudoers
~~~

Dosya içerisinde şu satırı bulup, başındaki crunch (diyez) işaretini
siliyorum.

    # %wheel ALL=(ALL) NOPASSWD: ALL

Artık kendi kullanıcımı ve kullanıcıma ait ev dizinini oluşturabilirim.
Benim kullanıcı adım **airy**, siz kendi kullanıcı adını
kullanabilirsiniz. Bundan sonraki işlemlerde airy gördüğünüz yerleri
kendi kullanıcı adınızla değiştirmeyi unutmayın.

~~~
  # useradd -m -G users,wheel,audio -s /bin/bash airy
  # passwd airy
~~~

Artık root olarak gerçekleştirdiğim ssh bağlantımı sonlandırıp (exit
yada Ctrl+D), kendi kullanıcı adımla sisteme bağlanabilirim.

Her bağlantıda şifremi tekrar yazmak istemediğim için ssh anahtarımı
yeni sisteme kopyalıyorum.

~~~
  $ ssh-copy-id -i ~/.ssh/id_rsa.pub airy@192.168.0.10
~~~

Artık kendi kullanıcı adımla şifre girmeden raspberry pi'a
bağlanabilirim.

~~~
  $ ssh 192.168.0.10
~~~

Rahat bir kullanım için bir kaç rötüşa daha ihtiyacım var.

Önce renkli bir konsol ve ls komutu kısaltmaları için aşağıdaki
satırları da **~/.bashrc** dosyasına ekliyorum:

    PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
    alias ls='ls --color=auto'
    alias grep='grep --color=auto'
    alias ll='ls -lhF'
    alias la='ll -a'

Ek bir konsola ihtiyaç duyduğumda yeni bir ssh bağlantısı kurmak yerine
[tmux](http://tmux.sourceforge.net/) kullanmayı tercih ediyorum.

~~~
  $ packer -S tmux
~~~

İstediğim zaman hafıza kartınızı bilgisayarıma takarak yedeğini
almak için **dd** programını kullanabilirim.

~~~
  $ sudo dd bs=10M if=/dev/mmcblk0 of=io.img
~~~

Aynı şekilde, istediğim zaman yedeklediğim imajı hafıza kartına **dd**
programı ile yazdırabilirim.

~~~
  $ sudo dd bs=10M if=io.img of=/dev/mmcblk0
~~~

Artık üzerinde Arch Linux kurulu bir raspberry pi cihazım olduğuna göre
ruby ile program yazmaya başlayabilirim.

### Kaynaklar
* [Raspberry Pi](http://www.raspberrypi.org/)
* [Arch Linux Wiki](https://wiki.archlinux.org/index.php/Raspberry_Pi)
* [Arch Linux Arm](http://archlinuxarm.org/platforms/armv6/raspberry-pi)

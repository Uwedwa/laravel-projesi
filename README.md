# laravel-projesi
Yapmam gerekenler docker kurup servis olarak aktif etmek ve kullanıcıya vermek

[Dağıtımıma](https://docs.docker.com/engine/install/) uygun repo'yu seçiyorum 

Docker'de kurmam gerekenler
SQL servisi
Nginx
PHP 
Redis
Laravel

`sudo systemctl enable --now docker`

Her komutta sudo kullanmamak için docker grubunu kullanıcıma veriyorum

`sudo usermod -aG docker $USER`


Her şeyden önce .env dosyamı oluşturuyorum klasörümün içine

`touch .env`

Laravel kullanımını incelediğimde "composer" adlı bir yazılımla laravelin php mysql ve redis'i olan bir cihaza kurulabildiğini direkt olarak bir imaj olmadığını fark ettim.
Laravel'in sistem [gereksinimlerine](https://laravel.com/framework/docs/13.x/deployment#server-requirements) baktığımda

PHP 8.3 veya daha büyük bir versiyonunu istediğini fark ettim Dockerfile'ye yazılacak ve alpine tabanlı seçtim

Dockerfile [örneklerini](https://docs.docker.com/reference/dockerfile/) inceliyorum
Kısaca projenin çalışacağı konteynırın işletim sistemini, bağımlılığını, nereden çalışacağını ve projeyi çalıştırmak için tek bir komut gerekiyorsa o komutları içinde bulunduran konteynırlaştırmamıza yarayan dosya diyebiliriz
Bizim projemizde gereksinimleri seçmemiz lazım
Bu [Laravel gereksinimleri](https://laravel.com/framework/docs/deployment#server-requirements) bunu alpine'de aramam gerek Dockerfile tabanı olarak alpine kullanmak istiyorum
İçinde composer kullanabilmem için docker exec ile konteynere girip elle kuracağımı düşünüyordum böyle bir [repo](https://github.com/mlocati/docker-php-extension-installer) keşfettim ve Dockerfile içine koyacağım
Laravel için bağımlılıkları da Alpine reposunda taramam gerek onun içinde böyle bir [repo](https://pkgs.alpinelinux.org/packages) indexi keşfettim
Şimdi Dockerfile yapmaya başlayalım


`nano Dockerfile` ile dosyamı yazmaya başlıyorum 

From php:8.3 alpineden 8.3 phpyi çekiyor
M'locatiyi çekiyor
Run chmod+x ile başalyan terminalde o komutları çalıştırıyor 

Workdir konteynırın uygulamasının nerede çalışacağını belirliyor


Docker composeyi de oluşutrdum



  kısaca alpine tabanlı redis'i çekiyor mariadb çekiyor mysql yerine tercih ettim nginx'i çekip port 80de başlatıyor projem adlı uygulama da dockerfileden konteynırı oluştur docker compose up -d ile başlatıyorum


  sorunsuz başladı ama localhost:80'den gidemedim hata aldım nginx çalışıyor ama laravel yok

  Mlocati reposunu baştan incelemek istiyorum


  Laravel'i kurmak için tek başına mlocati yetmiyormul konteynera girip composerden laravel kurmam gerekiyormuş

  docker compose exec projem composer create-project --prefer-dist laravel/laravel .

Laravel kuruldu


 Kurulduktan sonra ilerleyemediğimi fark ettim Redis'e veritabanını tanıtmam gerekiyormuş

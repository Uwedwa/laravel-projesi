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



`nano Dockerfile`


SQL servisi olarak MariaDb seçiyorum daha az kaynak harcadığı için

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
`FROM php:8.3-fpm-alpine

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions pdo_mysql redis bcmath exif pcntl zip

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

From php:8.3 alpineden 8.3 phpyi çekiyor
M'locatiyi çekiyor
Run chmod+x ile başalyan terminalde o komutları çalıştırıyor 

Workdir konteynırın uygulamasının nerede çalışacağını belirliyor


Docker composeyi de oluşutrdum


services:
  projem:
    build:
      context: .
      dockerfile: Dockerfile
    restart: always
    volumes:
      - ./src:/var/www/html
      - ./etc/php/local.ini:/usr/local/etc/php/conf.d/local.ini
    networks:
      - laravel-network

  web:
    image: nginx:alpine
    restart: always
    ports:
      - "80:80"
    volumes:
      - ./src:/var/www/html
      - ./etc/nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - app
    networks:
      - laravel-network

  db:
    image: mariadb:10.11
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laravel_projem
      MYSQL_USER: enes
      MYSQL_PASSWORD: enes123
    volumes:
      - db-data:/var/lib/mysql
      - ./etc/mysql/my.cnf:/etc/mysql/my.cnf
    networks:
      - laravel-network

  redis:
    image: redis:alpine
    restart: always
    networks:
      - laravel-network

networks:
  laravel-network:
    driver: bridge

volumes:
  db-data:


  kısaca alpine tabanlı redis'i çekiyor mariadb çekiyor mysql yerine tercih ettim nginx'i çekip port 80de başlatıyor projem adlı uygulama da dockerfileden konteynırı oluştur docker compose up -d ile başlatıyorum


  sorunsuz başladı ama localhost:80'den gidemedim hata aldım nginx çalışıyor ama laravel yok

# 🚀 Laravel Docker Projesi


<div align="center">

![PHP](https://img.shields.io/badge/PHP-8.3-777BB4?style=for-the-badge&logo=php&logoColor=white)
![Laravel](https://img.shields.io/badge/Laravel-13.x-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-alpine-009639?style=for-the-badge&logo=nginx&logoColor=white)
![MariaDB](https://img.shields.io/badge/MariaDB-10.11-003545?style=for-the-badge&logo=mariadb&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-alpine-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)

PHP 8.3, Nginx, MariaDB ve Redis ile tam yapılandırılmış Laravel Docker ortamı.

</div>

---

## 📦 Servisler

| Konteyner | İmaj | Port | Açıklama |
|-----------|------|------|----------|
| `proje` | `php:8.3-fpm-alpine` | 9000 (dahili) | Laravel uygulaması — PHP-FPM |
| `web` | `nginx:alpine` | **80** | Web sunucusu |
| `db` | `mariadb:10.11` | 3306 (dahili) | Veritabanı |
| `redis` | `redis:alpine` | 6379 (dahili) | Cache · Session · Queue |

---

## 📁 Proje Yapısı

```
laravel-projesi/
├── 🐋 docker-compose.yml
├── 🐋 Dockerfile
├── 📄 .env.example
├── etc/
│   ├── nginx/
│   │   └── default.conf      # PHP-FPM fastcgi yönlendirme
│   ├── php/
│   │   └── local.ini         # Upload limiti, memory, timeout
│   └── mysql/
│       └── my.cnf            # utf8mb4, InnoDB ayarları
└── src/                      # ← Laravel buraya kurulur (git'e dahil değil)
```

---

## ⚡ Hızlı Başlangıç

### 1 — Docker'ı hazırla

```bash
# Dağıtımına uygun kurulum
# https://docs.docker.com/engine/install/

# Her seferinde sudo yazmamak için
sudo usermod -aG docker $USER
sudo systemctl enable --now docker
```

### 2 — Repoyu klonla

```bash
git clone https://github.com/Uwedwa/laravel-projesi.git
cd laravel-projesi
```

### 3 — Ortam dosyasını oluştur

```bash
cp .env.example src/.env
```

> [!IMPORTANT]
> `.env` içinde `DB_HOST=db` ve `REDIS_HOST=redis` olarak bırak.
> Docker'da konteynerler birbirini **IP adresiyle değil, servis adıyla** bulur.

### 4 — Konteynerleri başlat

```bash
docker compose up -d
```

### 5 — Laravel'i kur

```bash
docker compose exec proje composer create-project --prefer-dist laravel/laravel .
```

### 6 — Veritabanını oluştur

```bash
docker compose exec proje php artisan migrate
```

### ✅ Hazır!

Tarayıcında **[http://localhost](http://localhost)** adresini aç.

---

## 🔧 Dockerfile'ın İçinden

```dockerfile
FROM php:8.3-fpm-alpine
```

| Tercih | Neden |
|--------|-------|
| **Alpine tabanlı** imaj | Minimal boyut (~50 MB vs ~400 MB) |
| **[mlocati/docker-php-extension-installer](https://github.com/mlocati/docker-php-extension-installer)** | PHP uzantılarını tek satırda kurar (`pdo_mysql`, `redis`, `bcmath`, `exif`, `pcntl`, `zip`) |
| **Composer** resmi imajdan kopyalandı | Ayrıca yükleme gerekmez |
| **Laravel'i `docker exec` ile kurduk** | Hazır Laravel imajı yok; Composer üzerinden kurulum şart |

---

## 🐛 Sık Karşılaşılan Hatalar

<details>
<summary><b>SQLSTATE[HY000] [2002] Connection refused</b></summary>

Docker'da her konteyner izoledir. Veritabanına `127.0.0.1` ile bağlanamazsın.

```env
# ❌ Yanlış
DB_HOST=127.0.0.1

# ✅ Doğru — servis adını kullan
DB_HOST=db
```

</details>

<details>
<summary><b>502 Bad Gateway</b></summary>

`etc/nginx/default.conf` içindeki `fastcgi_pass` değeri, `docker-compose.yml`'deki PHP servisinin adıyla eşleşmeli:

```nginx
fastcgi_pass proje:9000;   # ← docker-compose'daki servis adı
```

</details>

<details>
<summary><b>localhost:80'e gidemiyorum, Laravel yok</b></summary>

Nginx çalışıyor olabilir ama Laravel henüz kurulmamış olabilir. 5. adımdaki `composer create-project` komutunu çalıştırdığından emin ol.

</details>

---

## 📚 Kaynaklar

- 📖 [Laravel Deployment Dokümantasyonu](https://laravel.com/docs/deployment)
- 🐋 [Dockerfile Referansı](https://docs.docker.com/reference/dockerfile/)
- 🔌 [mlocati PHP Extension Installer](https://github.com/mlocati/docker-php-extension-installer)
- 📦 [Alpine Linux Paket İndeksi](https://pkgs.alpinelinux.org/packages)

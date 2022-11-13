# Instalasi
### Requirements
Package | Version
--- | ---
[Laravel](https://laravel.com/docs/9.x/deployment#introduction) | V9.x
[Npm](https://nodejs.org/en/)  | V6.14.16+ 
[Composer](https://getcomposer.org/)  | V2.2.6+
[Php](https://www.php.net/)  | V8.0+
[Mysql](https://www.mysql.com/)  |V 8.0.27+
[Laravel WebSockets](https://beyondco.de/docs/laravel-websockets/getting-started/introduction) | 1.13+

### LAMP Server (PHP 8.1)
Install Dependencies & LAMP : 
```
sudo apt install lsb-release ca-certificates apt-transport-https software-properties-common npm -y
sudo add-apt-repository ppa:ondrej/php
sudo apt install apache2 mysql-server
sudo apt install php8.1 php8.1-common php8.1-mysql php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-intl composer -y
```

## Laravel WebApp Deployment
> **NOTE**: Pada kasus ini, root web folder terdapat pada ***/var/www/html/***
### GSM KEMPO
Ganti folder Owner & folder permission:
```
sudo chown user:www-data -R gsm-kempo
sudo chmod 775 -R gsm-kempo/storage
sudo chmod 775 -R gsm-kempo/bootstrap/cache
```

Inisialisasi .env : 
```
cd gsm-kempo/
cp .env.example .env
npm install && composer install
```

Mengatur konfigurasi *dotenv* :
```
APP_NAME=GSM-KEMPO
APP_ENV=production
APP_KEY=
APP_DEBUG=false
APP_URL=http://localhost
...
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=kempo
DB_USERNAME=root
DB_PASSWORD=passwd
```

Lakukan migrasi & generate APP_KEY : 
```
php artisan migrate
php artisan key:generate
```

Laravel Deployment : 
```
composer install --optimize-autoloader --no-dev
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

Import Data Porprov menggunakan *phpmyadmin* atau mysql-cli :
```
mysql -u username -p kempo < data-porprovbanten.sql
```

## Game Kempo
Ganti folder Owner & folder permission:
```
sudo chown user:www-data -R game-kempo
sudo chmod 775 -R game-kempo/storage
sudo chmod 775 -R game-kempo/bootstrap/cache
```

Inisialisasi .env : 
```
cd game-kempo/
cp .env.example .env
npm install && composer install
```

Mengatur konfigurasi *dotenv* :
```
APP_NAME=Game-Kempo
APP_ENV=production
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost:8080
#API FOR GET ATHLETE PHOTO
GSM_PHOTO_URL=http://localhost:9000/profile-picture/
...
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=kempo
DB_USERNAME=root
DB_PASSWORD=
```

Lakukan migrasi & generate APP_KEY : 
```
php artisan migrate
php artisan key:generate
```

Laravel Deployment : 
```
composer install --optimize-autoloader --no-dev
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

Menjalankan *Laravel Websockets* :
```
php artisan websockets:serve
```

## [Laravel-Websockets dengan supervisord](https://beyondco.de/docs/laravel-websockets/basic-usage/starting#keeping-the-socket-server-running-with-supervisord)
>**Note**: websockets:serve daemon harus selalu berjalan untuk menerima koneksi. Ini adalah kasus penggunaan utama untuk supervisor, agar laravel-websockets tetap berjalan.

Instalasi supervisor : 
```
sudo apt install supervisor
```
Setelah *supervisor* diinstall, maka buat file konfigurasi pada direktori */etc/supervisor/conf.d* :
```
[program:websockets]
command=/usr/bin/php /var/www/html/game-kempo/artisan websockets:serve
numprocs=1
autostart=true
autorestart=true
user=laravel-echo
```

Setelah membuat file konfigurasi maka *update* dan jalankan supervisor : 
```
supervisorctl update
supervisorctl start websockets
```

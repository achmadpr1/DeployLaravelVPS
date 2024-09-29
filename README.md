# DeployLaravelVPS
Beberapa hal yang kamu perlukan :

project Laravel
- VPS / Virtual Private Server
- Domain (jika punya)

# Install database sesuai dengan database Laravel kalian (disini saya menggunakan mysql)
```
sudo apt-get install mysql-server
```

# Install PHP-FPM dan beberapa ekstensi PHP
```
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ondrej/php
sudo apt update
```
```
sudo apt install php8.1-fpm php8.1-common php8.1-mysql php8.1-xml php8.1-ctype php8.1-curl php8.1-gd php8.1-dom php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-soap php8.1-zip php8.1-bcmath php8.1-fileinfo php8.1-pdo php8.1-tokenizer -y
```

# Install NGINX
```
sudo apt install -y nginx
```

# Install composer
```
sudo apt install -y composer
```

# Clone repository kalian kedalam folder /srv
```
git clone <URLrepo> /srv/app
```

# Buat Database

```
mysql -u root -p
```
```
create database billing;
```
```
flush privileges;
```
```
exit
```

# Masuk ke folder aplikasi kalian dan ubah file .env.example menjadi file .env
```
cd /srv/app
mv .env.example .env
```

# Atur ulang file .env
```
nano .env
```

# Menambahkan folder vendor, migrasi dan optimasi
```
composer update
```
```
composer install --optimize-autoloader --no-dev
```
```
php artisan migrate
```
```
php artisan key:generate
```
```
php artisan config:cache
```
```
php artisan route:cache
```
```
php artisan view:cache
```
```
sudo chmod -R 775 public && sudo chmod -R 777 storage
```

# Mengatur configurasi NGINX
```
nano /etc/nginx/sites-enabled/default
```

- Hapus semua isi file dan silahkan paste dengan configurasi dibawah ini :

```
server {
    listen 80;
    listen [::]:80;
    server_name domain.com;
    root /srv/app/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

# restart NGINX
```
nginx -s reload
```

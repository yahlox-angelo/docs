# Basic installation of working environment in WSL LEMP (Linux Nginx MySQL PHP) stack

### Update and install package list
``` bash
sudo apt update
sudo apt upgrade -y
sudo apt full-upgrade -y
```

### Remove old/unused packages
``` bash
sudo apt autoremove -y
sudo apt clean
```

## Install Tools

### Install Nginx
``` bash
sudo apt install nginx -y
sudo service nginx start
sudo service nginx status
```

### Install PHP
``` bash
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install -y php8.4 php8.4-fpm php8.4-cli php8.4-mysql php8.4-curl php8.4-xml php8.4-mbstring php8.4-zip php8.4-gd
sudo service php8.4-fpm start
```

### Install MySQL
``` bash
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

### Update nginx to use PHP (Latest)
``` bash
sudo nano /etc/nginx/sites-available/default
```

```
server {
    listen 80;
    server_name localhost;

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

### Test Nginx
``` bash
sudo nginx -t
sudo service nginx reload
```



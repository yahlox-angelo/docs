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

### /etc/nginx/sites-available/default
```
# Clean default server for WSL Ubuntu 24 + Nginx + PHP 8.4

server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name localhost _;

    # App root
    root /var/www/html;
    index index.php index.html;

    # --- Basic hardening & limits ---
    client_max_body_size 20m;
    sendfile on;

    # Security headers (tune for your app/CSP needs)
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # --- Static files ---
    location ~* \.(?:jpg|jpeg|png|gif|ico|svg|webp|css|js|map|woff2?)$ {
        access_log off;
        log_not_found off;
        expires 7d;
        add_header Cache-Control "public, max-age=604800";
        try_files $uri =404;
    }

    # Default handler
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # --- PHP via PHP-FPM 8.4 (recommended classic stack) ---
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        # Socket path for PHP 8.4 FPM
        fastcgi_pass unix:/run/php/php8.4-fpm.sock;

        # If you run multiple PHP versions, be explicit:
        # fastcgi_param PHP_VALUE "opcache.enable=1\nopcache.memory_consumption=128";
        fastcgi_read_timeout 60s;
    }

    # Deny access to hidden files
    location ~ /\. {
        deny all;
    }

    # Optional: health check
    location = /nginx-health {
        access_log off;
        return 200 "ok\n";
        add_header Content-Type text/plain;
    }
}

# -------------------------------
# QUICK SWITCHES (commented)
# -------------------------------

# 1) PHP via php-cgi (no FPM). Start with: php-cgi -b 127.0.0.1:9000
#
# location ~ \.php$ {
#     include fastcgi_params;
#     fastcgi_pass 127.0.0.1:9000;
#     fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
#     fastcgi_read_timeout 60s;
# }

# 2) Reverse proxy to an app server (e.g., RoadRunner, Swoole, Node, etc.) on :8080
#
# location / {
#     proxy_set_header Host $host;
#     proxy_set_header X-Real-IP $remote_addr;
#     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#     proxy_set_header X-Forwarded-Proto $scheme;
#     proxy_pass http://127.0.0.1:8080;
# }

```

### Test Nginx
``` bash
sudo nginx -t && sudo service nginx reload
```



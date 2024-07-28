# 🌐 Nginx Configuration for React, Express, and Laravel

This guide provides the Nginx configuration for setting up a **React** front-end with an **Express** back-end and a **Laravel** application.

## 📚 Table of Contents

- [🌐 Nginx Configuration for React, Express, and Laravel](#-nginx-configuration-for-react-express-and-laravel)
  - [📚 Table of Contents](#-table-of-contents)
  - [React and Express Configuration](#react-and-express-configuration)
    - [Nginx Configuration for React and Express](#nginx-configuration-for-react-and-express)
- [🚀 REACT EXPRESS NGINX CONFIG](#-react-express-nginx-config)
  - [Laravel 11 Configuration](#laravel-11-configuration)
    - [Nginx Configuration for Laravel](#nginx-configuration-for-laravel)
- [🛠️ LARAVEL NGINX CONFIG](#️-laravel-nginx-config)
  - [🔒 SSL Configuration](#-ssl-configuration)
  - [🏁 Conclusion](#-conclusion)

## React and Express Configuration

### Nginx Configuration for React and Express

Create a new Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-available/main-web
```

# 🚀 REACT EXPRESS NGINX CONFIG

Add the following configuration:

```nginx
server {
    server_name main-web react.tomdevstudio.org; # Root where the front-end is saved on the server
    root /var/www/main-web/frontend-csib6/dist;
    index index.html;

    location / {
        try_files $uri /index.html;
    }

    # API routing to the backend with PM2, ensure the port is unique
    location /api {
        proxy_pass http://localhost:5051;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    # SSL certificate generated automatically by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/react.tomdevstudio.org/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/react.tomdevstudio.org/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = react.tomdevstudio.org) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80;
    server_name main-web react.tomdevstudio.org;
    return 404; # managed by Certbot
}
```

Enable the configuration and restart Nginx:

```bash
sudo ln -s /etc/nginx/sites-available/main-web /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

## Laravel 11 Configuration

### Nginx Configuration for Laravel

Create a new Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-available/laravel.tomdevstudio.org
```

# 🛠️ LARAVEL NGINX CONFIG

```nginx
server {
    server_name laravel.tomdevstudio.org laravel.tomdevstudio.org; # Replace with your Laravel app domain

    # Root where the index.php Laravel entry point is stored
    root /var/www/tom-project/public;

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
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;  # Adjust the PHP version if necessary
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_hide_header X-Powered-By;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }

    # SSL certificate generated automatically by Certbot
    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/laravel.tomdevstudio.org/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/laravel.tomdevstudio.org/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = laravel.tomdevstudio.org) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80;
    listen [::]:80;
    server_name laravel.tomdevstudio.org laravel.tomdevstudio.org;
    return 404; # managed by Certbot
}
```

Enable the configuration and restart Nginx:

```bash
sudo ln -s /etc/nginx/sites-available/laravel.tomdevstudio.org /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

## 🔒 SSL Configuration

To secure your applications with SSL, you can use **Let's Encrypt**. Install Certbot:

```bash
sudo apt install certbot python3-certbot-nginx
```

Obtain an SSL certificate:

```bash
sudo certbot --nginx -d your_domain
```

Follow the prompts to complete the SSL setup. Certbot will automatically configure Nginx to use the SSL certificate.

## 🏁 Conclusion

With these configurations, your Nginx server should be able to serve your **React** front-end, **Express** back-end, and **Laravel** applications efficiently. If you encounter any issues, refer to the Nginx documentation or seek help from the community.

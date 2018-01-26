# Git Repository Viewer
* Configure a site on your localhost or server for the repository viewer
* Clone this repository to the document root of your new site.
* You can either use the included repos folder to store your repositories -OR- adjust the 3rd line of the config.ini file pointing to the path of your repositories (i.e. /Users/yourname/Sites) 
* Grant read and write access to the cache folder for your web server user

This repository viewer has been configured to work with Apache by default, notice the .htaccess file in the root. You can also run this on NGINX if you prefer. There is an example server block for setting this up on NGINX.

### NGINX Web Server Configuration

```
server {
    server_name repositoryviewer.com;
    access_log /var/log/nginx/repositoryviewer.access.log combined;
    error_log /var/log/nginx/repositoryviewer.error.log error;

    root /var/www/html/repositoryviewer.com;
    index index.php;

    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }

    location ~* ^/index.php.*$ {
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        # if you're using php5-fpm via tcp
        fastcgi_pass 127.0.0.1:9000;

        # if you're using php5-fpm via socket
        #fastcgi_pass unix:/var/run/php5-fpm.sock;

        include /etc/nginx/fastcgi_params;
    }

    location / {
        try_files $uri @gitlist;
    }

    location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
    add_header Vary "Accept-Encoding";
        expires max;
        try_files $uri @gitlist;
        tcp_nodelay off;
        tcp_nopush on;
    }

    location @gitlist {
        rewrite ^/.*$ /index.php;
    }
}
```
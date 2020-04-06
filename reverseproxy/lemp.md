# Proxy not speaking http
## uWSGI
## and
## fastcgi

# Fastcgi
binary protocol for exchanging data
key value pairs that get packaged up and sent across the wire so two fastcgi compatiple applications can speak to one another

No way for nginx to natively process php
so use php-fpm package
Centos release scl repository
LEMP Stack

# Code
#### Setting Up `php-fpm`
- `yum install -y centos-release-scl`
  - another repository to pull from
- `yum update`
- `yum install -y rh-php71-php rh-php71-php-fpm rh-php71-php-mysqlnd`
- `/etc/opt/rh/rh-php71/php-fpm.d/www.conf`  (Only Changed Lines)
        ; Set user and group
        user = nginx
        group = nginx
        ; Set to listen on a socket instaead of port.
        listen = /var/run/php-fpm.sock
        ; Set permissions on Unix socket.
        listen.owner = nginx
        listen.group = nginx
        listen.mode = 0660
- `systemctl start rh-php71-php-fpm`
- `systemctl enable rh-php71-php-fpm`
### Setting Up MariaDB

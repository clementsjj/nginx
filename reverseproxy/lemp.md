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

#############################  
Database Name: wordpress  
Database User: wpuser  
Database Password: p@ssw0rd  
#############################

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
- `/etc/yum.repos.d/mariadb.repo`
        
        [mariadb]
        name = mariaDB
        baseurl = http://yum.mariadb.org/10.2/centos7-amd64
        gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
        gpgcheck=1
- `yum update`
- `yum install -y MariaDB-server MariaDB-client MariaDB-devel MariaDB-shared`
- `systemctl start mariadb`
- `systemctl enable mariadb`
- `mysql_secure_installation`
- mysql setup
        > Answer yes to all
        > Password set same as server
- `mysql -u root -p`  

        > CREATE DATABASE wordpress;
        > GRANT ALL PRIVILEGES ON wordpress.* TO wpuser@localhost IDENTIFIED BY "p@ssw0rd";
        > FLUSH PRIVILEGES;
        > \q
- `mkdr /var/www/blog.example.com`
- `wget -q -O - http://wordpress.org/latest.tar.gz | tar -xzf - --strip 1 -C /var/www/blog.example.com`
- `cp /var/www/blog.example.com/wp-config-sample.php /var/www/blog.example.com/wp-config.php`
- `chown -R nginx:nginx /var/www/blog.example.com`
- `vim /var/www/blog.example.com/wp-config.php`
        
        define('DB_NAME', 'wordpress');
        define('DB_USER', 'wpuser');
        define('DB_PASSWORD', 'p@ssw0rd');
        define('DB_HOST', 'localhost');
        
- Scroll down and complete the salting of keys by generating unique phrase at the given url (`shift` + `insert`)


# FastCGI Proxy
for PHP/WordPress with fastcgi_pass
FastCGI is "binary protocol" for sharing informatin from a web server to external programs.
Common Gateway Interface.
In this case, allows us to pass information to and from PHP from NGINX using php-fpm.
NGINX provides the `http_fastcgi_module` for working with FastCGI.
Communicating over a socket.


- `vim /etc/nginx/conf.d/blog.example.com.conf`

        server {
          listen 80;
          server_name blog.example.com;

          root /var/www/blog.example.com;
          index index.php;

          location / {
              try_files $uri $uri/ /index.php?$args;
          }

        # where we actually set up the reverse proxy
          location ~ \.php$ {
              fastcgi_index index.php;
              fastcgi_pass unix:/var/run/php-fpm.sock;
              include fastcgi_params;
              fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
          }
        }

- `fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;`
- Add `blog.example.com` to `etc/hosts`
  - `<ipaddress> blog.example.com`

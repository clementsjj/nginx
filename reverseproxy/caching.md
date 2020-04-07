Utilize a reverse proxy for some caching. 
If we know the proper response, we can serve up content without bothering our application server with another request.

- `vim /etc/nginx/conf.d/blog.example.com.conf`
          
        fastcgi_cache_path /var/cache/nginx/blog levels=1:2
                   keys_zone=blog:10m size=1g inactive=60m;

        server {
            listen 80;
            server_name blog.example.com;

            root /var/www/blog.example.com;
            index index.php;

            fastcgi_cache_key $scheme$request_method$host$request_uri;

            location / {
                try_files $uri $uri/ /index.php?$args;
            }

            location ~ \.php$ {
                fastcgi_index index.php;
                fastcgi_pass unix:/var/run/php-fpm.sock;
                fastcgi_cache blog;
                fastcgi_cache_valid 60m;
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            }
        }
        
- 

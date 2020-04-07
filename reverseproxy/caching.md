Utilize a reverse proxy for some caching. 
If we know the proper response, we can serve up content without bothering our application server with another request.

##### Create Cache using fastcgi_cache_path  
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
        
##### Adding Additional Cache Header
- `vim /etc/nginx/conf.d/blog.example.com.conf`
  - View x-cache response header that shows if its a hit or miss for cached pages

            location ~ \.php$ {
                add_header X-Cache-Status $upstream_cache_status;
                ...
            }

##### Not Caching Content
- `vim /etc/nginx/conf.d/blog.example.com.conf`
    
        server {
          ...
          set $skip_cache 0;
          if ($request_uri ~* "/wp-admin") {
              set $skip_cache 1;
          }
          ...
          location ~ \.php$ {
            ...
            fastcgi_cache_bypass $skip_cache;
            fastcgi_no_cache $skip_cache;
            ...
          }
        }
        
        
        
## Microcaching
Short term caching to improve performance 

- `pip install boom`
- `boom http://localhost/ --header Host:notes.example.com -c 30 -n 3000`
  - 30 concurrent users making 3000 requests
- `vim /etc/nginx/conf.d/notes.example.com.conf/`
        
        uwsgi_cache_path /var/cache/nginx/micro levels=1:2
                         keys_zone=micro:10m max_size=1g;
        server {
            listen 80;
            server_name notes.example.com;

            uwsgi_cache_key $scheme$request_method$host$request_uri;

            location /static {
                root /var/www/notes.example.com;
            }

            location / {
                add_header X-Cache-Status $upstream_cache_status;
                include uwsgi_params;
                uwsgi_pass unix:/var/run/uwsgi/notes.sock;
                uwsgi_cache micro;
                uwsgi_cache_valid 10s;
            }
        }

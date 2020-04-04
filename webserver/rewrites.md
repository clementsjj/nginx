rewrite rules allow us to modify the url as its coming in
redirect or modify the url before passing it into our location blocks so it can be handles

remove html aspect of things in the url and have same content be rendered
also hides your infrastructure...

        server {
          ...
          rewrite ^(/.*)\.html(\?.*)?$ $1$2 redirect;
          rewrite ^/(.*)/$ /$1 redirect;

          location / {
            try_files $uri/index.html $uri.html $uri/ $uri =404;
          }

          location = /admin {
                auth_basic "Login Required";
                auth_basic_user_file /etc/nginx/.htpasswd;
                try_files $uri/index.html $uri.html $uri/ $uri =404;
          } 
        }


http_rewrite_module  
http_core_module  > try_files


---
## redirecting all traffic to https
        server {
            listen 80 default_server;
            server_name _;
            return 301 https://$host$request_uri;
        }




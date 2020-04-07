### Set up Web App

- `curl --silent --location https://rpm.nodesource.com/setup_12.x | sudo bash -`
- `yum install -y nodejs`
- `mkdir /srv/www`
- `cd /srv/www/`
- `git clone https://github.com/CloudAssessments/s3photoapp`
- `cd s3photoshop`
- `make install`
- `/etc/systemd/system/photo-filter.service

        [Unit]
        Description=photo-filter Node.js service
        After=network.target

        [Service]
        Restart=always
        User=nobody
        Group=nobody
        Environment=NODE_ENV=production
        ExecStart=/bin/node /srv/www/s3photoapp/apps/photo-filter/server.js

        [Install]
        WantedBy=multi-user.target
- `/etc/systemd/system/photo-storage.service`

        [Unit]
        Description=photo-storage Node.js service
        After=network.target

        [Service]
        Restart=always
        User=nobody
        Group=nobody
        Environment=NODE_ENV=production
        Environment=AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY_ID
        Environment=AWS_SECRET_ACCESS_KEY=YOUR_SECRET_ACCESS_KEY
        ExecStart=/bin/node /srv/www/s3photoapp/apps/photo-storage/server.js

        [Install]
        WantedBy=multi-user.target
- `/etc/systemd/system/web=client.service`  

        [Unit]
        Description=S3 Photo App Node.js service
        After=network.target photo-filter.target photo-storage.target

        [Service]
        Restart=always
        User=nobody
        Group=nobody
        Environment=NODE_ENV=production
        Environment=AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY_ID
        Environment=AWS_SECRET_ACCESS_KEY=YOUR_SECRET_ACCESS_KEY
        ExecStart=/srv/www/s3photoapp/apps/web-client/bin/www

        [Install]
        WantedBy=multi-user.target
-       systemctl start photo-storage
        systemctl enable photo-storage
        systemctl start photo-filter
        systemctl enable photo-filter
        systemctl start web-client
        systemctl enable web-client

### http_proxy_module
### With
### proxy_pass

- `/etc/nginx/conf.d/phones.example.com.conf`  

        server {     
          listen 80;     
          server_name photos.example.com;      
          location / {         
            proxy_pass http://127.0.0.1:3000; 
          # Could Stop Here with just the proxy_pass
          # version 1.0 by default..
            proxy_http_version 1.1;         
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;         
            proxy_set_header X-Real-IP  $remote_addr;         
            proxy_set_header Upgrade $http_upgrade;         
            proxy_set_header Connection "upgrade";     
            } 
          }
- `nginx -t`
- `systemctl reload nginx`
- `curl --header "Host: photos.example.com" localhost`
- less `/var/log/nginx/error.log`
- `setsebool -P httpd_can_network_connect 1`
- `curl --header "Host: photos.example.com" localhost`
- ` /etc/nginx/conf.d/photos.example.com`

        server {
        ...
        # ~ means regex, * means case insensitive
          location ~* \.(js|css|png|jpe?g|gif) {
            root /var/www/photos.example.com;
          }
        }
- `ln -s /srv/www/s3photoapp/apps/web-client/public/ /var/www/photos.example.com`
- `restorecon -R -v /var/www`
- `systemctl reload nginx`
  - get 403 for grabbing some files on browser
- `semanage fcontext -a -t httpd_sys_content_t '/srv/www/.*/public(/.*)?'`
- `restorecon -R -v /var/www`
- `/etc/nginx/conf.d/photos.example.com.conf` .
         server {
          ...
          client_max_body_size 5m;
         }
- `nginx -t`
- `systemctl reload nginx`

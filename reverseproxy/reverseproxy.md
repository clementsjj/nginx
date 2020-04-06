###Set up Web App

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

###http_proxy_module


WSGI = Web Server Gateway Interface
Alternative to FastCGI


############################  
Database Name: django_notes
Database User: notes
Database Password: p@ssw0rd  
############################  

- `yum install python36 python36-devel`
- `wget https://bootstrap.pypa.io/get-pip.py`
- `python3.6 get-pip.py`
- `pip3.6 install pipenv requests`
- `cd /srv/www`
- `git clone https://github.com/linuxacademy/content-nginx-uwsgi`
- `cd content-nginx-uwsgi`
- `mysql -u root -p`

        CREATE DATABASE django_notes;
        GRANT ALL PRIVILEGES ON django_notes.* TO notes@localhost IDENTIFIED BY "p@ssw0rd";
        FLUSH PRIVILEGES;
        \q
        
- `make install`
### ^ ERROR Here ^

 - `export NOTES_HOST=notes.example.com`
 - `export NOTES_DB=django_notes`
 - `export NOTES_DB_USER=notes`
- `export NOTES_DB_PASSWORD="p@ssw0rd"`
- `make migrate`
- `make static`
- `make service`
        > notes.example.com
        > django_notes
        > notes
        > p@ssw0rd
- `systemctl start notes.uwsgi`
- `systemctl enamble notes.uwsgi`

### Proxying to uSWSGI Python Web App with `uwsgi_pass`
- `vim /etc/nginx/conf.d/notes.example.com.conf`

        server {
            listen 80;
            server_name notes.example.com;

            location /static {
               root /var/www/notes.example.com;
            }

            location / {
                include uwsgi_params;
                uwsgi_pass unix:/var/run/uwsgi/notes.sock;
            }
        }
- `cd /etc/nginx`
- `semanage permissive -a httpd_t`
- `systemctl reload nginx`
- `curl --header "Host: notes.example.com" localhost`
- `grep nginx /var/log/audit/audit.log | audit2allow -m nginx`
- `semanage permissive -d httpd_t`
- `semodule -i nginx.pp`
- `semodule -e nginx`
- `restorecon -Rv /var/run/uwsgi/`


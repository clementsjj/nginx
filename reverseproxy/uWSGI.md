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
- 

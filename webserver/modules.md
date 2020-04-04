- `nginx -V 2>&1 | tr -- - '\n' | grep _module`
  - View built in modules
- load_module to load dynamic modules

mod security web application firewall

- `yum groupinstall 'Development tools'
-       yum install -y \
         geoip-devel \
         gperftools-devel \
         libcurl-devel \
         libxml2-devel \
         libxslt-devel \
         libgd-devel \
         lmdb-devel \
         openssl-devel \
         pcre-devel \
         perl-ExtUtils-Embed \
         yajl-devel \
         zlib-devel
- `cd /opt`
- `git clone --depth 1 -b v3/master https://github.com/SpiderLabs/ModSecurity.git`
- `cd ModSecurity`
- `git submodule init`
- `git submodule update`
- `./build.sh`
- `echo $?`
  - status of what we just did. 0 is good, despite fatal:no names found.
- `./configure`
- `make`
- `make install`
- `cd /opt`
- `git clone --depth 1 https://github.com/SpiderLabs/ModSecurity-nginx.git`
- `nginx -v`
  - `nginx version: nginx/1.17.9`
- `wget http://nginx.org/download/nginx-1.17.9.tar.gz`
- `tar zxvf nginx-1.17.9.tar.gz`
- `cd nginx-1.17.9`
- `./configure --with-compat --add-dynamic-module=../ModSecurity-nginx`
- `make modules`
- `cp objs/ngx_http_modsecurity_module.so /etc/nginx/modules/`


- `cd /etc/nginx`
- `vim nginx.conf`
-       ...
        load_module /etc/nginx/modules/ngx_http_modsecurity_module.so;
        ...


- `mkdir /etc/nginx/modsecurity`
- `cp /opt/ModSecurity/modsecurity.conf-recommended /etc/nginx/modsecurity/modsecurity.conf`
- `cp /opt/ModSecurity/unicode.mapping /etc/nginx/modsecurity/unicode.mapping`
- `vim /etc/nginx/modsecurity/modsecurity.conf`
- `SecAuditLog /var/log/nginx/modsec_audit.log`
  - adding nginx dir to path
- `vim /etc/nginx/conf.d/default.conf`
-       server {
          listen 443 ssl;
          ...
          modsecurity on;
          modsecurity_rules_file /etc/nginx/modsecurity/modsecurity.conf;
          ...
        }
- `nginx -t`
- `systemctl reload nginx`
-

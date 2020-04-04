## BLUF
### Simple Virtual HOst and Serving Static Content
- `rm conf.d/default.conf`
- `curl localhost`
  - returns the landing page
- `systemctl reload nginx`
- `systemctl status nginx`
- `curl localhost`
  - "Connection refused"
- `vim /etc/nginx/conf.d/default.conf`
-
        server {
          listen 80;
          root /usr/share/nginx/html;
        }
- nginx -t
  - Checks the validity of the .conf syntax
- `system reload nginx`
- curl localhost
  - Delivers content!
- `vim /etc/nginx/conf.d/example.com.conf`
-
        server {
          listen 80;
          server_name example.com www.example.com;
          root /var/www/example.com/html;
        }
- `mkdir -p /var/www.example.com/html`
- `echo "<h1>Hello, example.com</h1>" > /var/www/example.com/html/index.html`
- `nginx -t`
- `systemctl reload nginx`
- `systemctl status nginx`
- `curl localhost`
- `curl --header "Host: example.com"  localhost
  - Will get forbidden. This is a thing with Security-Enhanced Linux...
- `semanage fcontext -l | grep /usr/share/nginx/html`
  - copy `httpd_sys_content_t` from the type shown
  - httpd is the "rule" we'll be following
- `semanage fcontext -a -t httpd_sys_content_t '/var/www(/.*)?'`
  - "-a add it"; and the path we are including..
  - "libsemanage.dbase_llist_query: could not query record value (No such file or directory)."
- `semanage fcontext -l | grep /var/www`
  - Shows that the previous command was actually executed and not errored out
- `restorecon -R -v /var/www`
- `curl --header "Host: example.com" localhost`

### Error Pages


## Notes
- Virtual host = individual server configurations

ngx_http_core_module

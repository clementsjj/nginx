Generate Self Signed SSL Certificate
iot respond to https requests

- `mkdir /etc/nginx/ssl`
- 
         openssl req -x509 -nodes -days 365 \
         -newkey rsa:2048 \
         -keyout /etc/nginx/ssl/private.key \
         -out /etc/nginx/ssl/public.pem
- `vim conf.d/default.conf`
- Add to default.conf:
        server {
          ...
          listen 443 ssl;
          ssl_certificate /etc/nginx/ssl/public.pem;
          ssl_certificate_key /etc/nginx/ssl/private.key;
        }
- `nginx -t`
- `systemctl reload nginx`
- `curl https://localhost`
- In browser, will get a self signed cert warning




## Notes
- req - We’re making a certificate request to OpenSSL
- -x509 - Specifying the structure that our certificate should have. Conforms to the X.509 standard
- -nodes - Do not encrypt the output key
- -days 365 - Set the key to be valid for 365 days
- -newkey rsa:2048 - Generate an RSA key that is 2048 bits in size
- -keyout /etc/nginx/ssl/private.key - File to write the private key to
- -out /etc/nginx/ssl/public.pem - Output file for public portion of key

http_ssl_module

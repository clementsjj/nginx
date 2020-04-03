# Initial Setup for CentOS7

- `sudo vim /etc/yum.repos.d/nginx.repo`
-
        [nginx]
        name=nginx repo
        baseurl=https://nginx.org/packages/mainline/centos/7/$basearch/
        gpgcheck=0
        enabled=1
- `sudo yum update`
- `sudo yum install nginx`
- `sudo systemctl start nginx`
- `sudo systemctl enable nginx`

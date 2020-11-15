## Ansible Role: Nginx
Role for installing and configuring Nginx
## Requirements
Ansible version: 2.9
Centos 7/ Debian
## Dependencies
ansible-role-php
## Example Playbook
```
- hosts: "{{ var }}"
  roles:
    - { role: ansible-role-nginx }
  vars:
    nginx_vhosts:
      - listen: "80"
        server_name: "site.com"
        return: "301 https://site.com$request_uri"
        filename: "site.com.80.conf"
      - listen: "443 ssl http2"
        server_name: "site.com"
        root: "/var/www/site.com/current/public"
        index: "index.php index.html"
        filename: "site.com.conf"
        access_log: "/var/log/nginx/site.access.log"
        error_log: "/var/log/nginx/site.error.log"
        extra_parameters: |
           # Remove index.php
           #if ($request_uri ~* "^(.*/)index\.php/*(.*)") {
           #   return 301 $1$2;
           #}
           location / {
              try_files $uri /index.php$is_args$args;
           }
           location ~ [^/]\.php(/|$) {
              fastcgi_index index.php;
              fastcgi_pass 127.0.0.1:9000;
              fastcgi_split_path_info ^(.+\.php)(/.*)$;
              #fastcgi_param HTTPS 'on';
              fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
              fastcgi_param DOCUMENT_ROOT $realpath_root;
              include fastcgi_params;
           }
           #set_real_ip_from 10.10.10.5;
           #real_ip_header X-Real-IP;
           ssl_certificate     /etc/nginx/ssl/site.crt;
           ssl_certificate_key /etc/nginx/ssl/site.key;
           ssl_protocols       TLSv1.1 TLSv1.2;
           ssl_ciphers         HIGH:!aNULL:!MD5;
           autoindex off;
           client_max_body_size 50m;
           include ./include/opcache.conf;
           include ./include/well-known.conf;
           include ./include/php-fpm_status.conf;
           include ./include/status.conf;
           include ./include/static-files.conf;
           include ./include/deny.conf;
           location ~ \.php$ {
           return 404;
           }
```

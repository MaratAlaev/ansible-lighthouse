# Ansible Role: lighthouse

Устанавливает lighthous

## Role Variables

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| nginx_config | | конфиг nginx для lighthouse |
| nginx_host |  | конфид для хоста |


## Example

### playbook

```yaml
- name: Install Lighthouse
  hosts: lighthouse
  vars:
    nginx_config: |
      user www-data;
      worker_processes auto;
      pid /run/nginx.pid;
      include /etc/nginx/modules-enabled/*.conf;

      events {
        worker_connections 768;
      }

      http {

        sendfile on;
        tcp_nopush on;
        types_hash_max_size 2048;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;


        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        log_format track '$remote_addr - $time_iso8601 "$request_uri" '
                 '$status $body_bytes_sent "$http_user_agent"';

        access_log /var/log/track.log track;    
        gzip on;

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
      }

nginx_host: |
    server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /var/www/lighthouse;

        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                try_files $uri $uri/ =404;
        }
      }
  roles:
    - {role: lighthouse-role}
```
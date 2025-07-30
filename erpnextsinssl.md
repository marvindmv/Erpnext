# Restaurar Supervisor y desactivar SSL en ERPNext

Este procedimiento permite revertir la configuración SSL en ERPNext y restaurar el servicio Supervisor para entornos sin HTTPS.

Eliminar lineas en circulo 
<img width="919" height="679" alt="image" src="https://github.com/user-attachments/assets/b9a20707-c53b-46ab-8d59-e13fa038265a" />

# sustituir en archivo : nginx.conf

    upstream frappe-bench-frappe {
        server 127.0.0.1:8000 fail_timeout=0;
    }
    
    upstream frappe-bench-socketio-server {
        server 127.0.0.1:9000 fail_timeout=0;
    }
    
    server {
        listen 80;
        server_name site1.local;

        root /home/frappe/frappe-bench/sites;
    
        add_header X-Frame-Options "SAMEORIGIN";
    
        location /assets {
            try_files $uri =404;
        }
    
        location ~ ^/protected/(.*) {
            internal;
            try_files /site1.local/$1 =404;
        }
    
        location /socket.io {
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header X-Frappe-Site-Name site1.local;
            proxy_set_header Origin $scheme://$http_host;
            proxy_set_header Host $host;
            proxy_pass http://frappe-bench-socketio-server;
        }
    
        location / {
            try_files /site1.local/public/$uri @webserver;
        }
    
        location @webserver {
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Frappe-Site-Name site1.local;
            proxy_set_header Host $host;
            proxy_set_header X-Use-X-Accel-Redirect True;
            proxy_read_timeout 120;
            proxy_redirect off;
            proxy_pass http://frappe-bench-frappe;
        }
    
        error_page 502 /502.html;
        location /502.html {
            root /home/frappe/.bench/bench/config/templates;
            internal;
        }
    
        sendfile on;
        keepalive_timeout 15;
        client_max_body_size 50m;
        client_body_buffer_size 16K;
        client_header_buffer_size 1k;
    
        gzip on;
        gzip_http_version 1.1;
        gzip_comp_level 5;
        gzip_min_length 256;
        gzip_proxied any;
        gzip_vary on;
        gzip_types
            application/atom+xml
            application/javascript
            application/json
            application/rss+xml
            application/vnd.ms-fontobject
            application/x-font-ttf
            application/font-woff
            application/x-web-app-manifest+json
            application/xhtml+xml
            application/xml
            font/opentype
            image/svg+xml
            image/x-icon
            text/css
            text/plain
            text/x-component;
    }



## 🔁 Restaurar Supervisor

```bash

# Configurar Supervisor para ERPNext
bench setup supervisor

# Recargar y actualizar configuración
sudo supervisorctl reread
sudo supervisorctl update

# Reiniciar servicios ERPNext
bench restart

# Reiniciar servidor
sudo reboot

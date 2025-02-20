¡Claro! Aquí tienes un archivo README de GitHub basado en la guía para instalar ERPNext versión 15 en Ubuntu 22.04:


# Guía de Instalación de ERPNext Versión 15 en Ubuntu 22.04

**Escrito por Karani, 31 de diciembre de 2023**

En este artículo, te guiaré paso a paso para configurar nuestro recién instalado sistema operativo Ubuntu 22.04 y preparar el entorno para instalar ERPNext versión 15. ¡Manos a la obra!

## Prerrequisitos

### Requisitos de Software
- Ubuntu 22.04 actualizado
- Un usuario con privilegios sudo
- Python 3.10+
- Node.js 18

### Requisitos de Hardware
- 4GB de RAM
- Disco duro de 40GB

## Configuración del Servidor

### Actualizar y Mejorar Paquetes
```sh
sudo apt-get update -y
sudo apt-get upgrade -y
```

### Crear un Nuevo Usuario (usuario de bench)
```sh
sudo adduser [frappe-user]
usermod -aG sudo [frappe-user]
su [frappe-user]
cd /home/[frappe-user]
```
Asegúrate de reemplazar `[frappe-user]` con tu nombre de usuario. Ejemplo: `sudo adduser frappe`.

### Instalar Paquetes Necesarios

#### Instalar GIT
```sh
sudo apt-get install git
```

#### Instalar Python
ERPNext versión 15 requiere Python versión 3.10+. Instalaremos lo siguiente:
```sh
sudo apt-get install python3-dev python3.10-dev python3-setuptools python3-pip python3-distutils
```

#### Instalar Entorno Virtual de Python
```sh
sudo apt-get install python3.10-venv
```

#### Instalar Software Properties Common
```sh
sudo apt-get install software-properties-common
```

#### Instalar MariaDB
```sh
sudo apt install mariadb-server mariadb-client
```

#### Instalar Servidor Redis
```sh
sudo apt-get install redis-server
```

#### Instalar Otros Paquetes
```sh
sudo apt-get install xvfb libfontconfig wkhtmltopdf
sudo apt-get install libmysqlclient-dev
```

### Configurar el Servidor MYSQL

#### Configurar el Servidor
```sh
sudo mysql_secure_installation
```
Sigue las indicaciones para completar la configuración correctamente.
- Enter current password for root: (Enter your SSH root user password)
- Switch to unix_socket authentication [Y/n]: Y
- Change the root password? [Y/n]: Y
- Remove anonymous users? [Y/n] Y
- Disallow root login remotely? [Y/n]: N
- Remove test database and access to it? [Y/n]: Y
- Reload privilege tables now? [Y/n]: Y

#### Editar el Archivo de Configuración Predeterminado de MYSQL
```sh
sudo nano /etc/mysql/my.cnf
```
Añade el siguiente bloque de código:
```ini
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```

#### Reiniciar el Servidor MYSQL
```sh
sudo service mysql restart
```

### Instalar CURL, Node, NPM y Yarn

#### Instalar CURL
```sh
sudo apt install curl
```

#### Instalar Node
```sh
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
source ~/.profile
nvm install 18
```

#### Instalar NPM
```sh
sudo apt-get install npm
```

#### Instalar Yarn
```sh
sudo npm install -g yarn
```

### Instalar Frappe Bench
```sh
sudo pip3 install frappe-bench
```

### Inicializar Frappe Bench
```sh
bench init --frappe-branch version-15 frappe-bench
cd frappe-bench
```

### Cambiar Permisos del Directorio del Usuario
```sh
chmod -R o+rx /home/[frappe-user]
```

### Crear un Nuevo Sitio
```sh
bench new-site [site-name]
```

### Instalar ERPNext y Otras Aplicaciones

#### Descargar Aplicaciones
```sh
bench get-app payments
bench get-app --branch version-15 erpnext
bench get-app hrms
```

#### Instalar Aplicaciones en Nuestro Sitio
```sh
bench --site [site-name] install-app erpnext
bench --site [site-name] install-app hrms
```

### Iniciar el Servidor
```sh
bench start
```
Visita `[TU IP DEL SERVIDOR:8000]` para ver ERPNext en funcionamiento.

### Configuración de ERPNext para Producción

#### Habilitar el Programador
```sh
bench --site [site-name] enable-scheduler
```

#### Desactivar el Modo de Mantenimiento
```sh
bench --site [site-name] set-maintenance-mode off
```

#### Configurar Producción
```sh
sudo bench setup production [frappe-user]
bench setup nginx
sudo supervisorctl restart all
sudo bench setup production [frappe-user]
```
Para más detalles, consulta la [guía completa](https://codewithkarani.com/2023/12/31/how-to-install-erpnext-version-15-in-ubuntu-a-step-by-step-guide/).

 
# Configuración de la Tenencia Múltiple en ERPNext
**Escrito por Karani, 24 de agosto de 2021**
En software, la tenencia múltiple es una arquitectura en la que una sola instancia de una aplicación de software atiende a varios clientes, que se denominan inquilinos. La aplicación puede ofrecerse como un servicio o como una plataforma.

   
## Pasos necesarios
### 1. Configurar DNS para tenencia múltiple:
```sh
bench config dns_multitenant on
```

### 2. Crear un nuevo sitio:
```sh
bench new-site first.site
```
### 3. Configurar NGINX:
```sh
sudo service nginx reload
```
### 4. Instalar ERPNext en el nuevo sitio:
```sh
bench --site first.site install-app erpnext
```
#### 5. Editar el archivo `/etc/hosts` y añadir las rutas para cada sitio:
```plaintext
127.0.0.1 first.site
127.0.0.1 second.site
```
#### Por favor, tenga en cuenta el nombre de sus sitios. Deberá agregar una ruta para cada sitio que agregue.
#### Para más detalles, puedes visitar el artículo original en [Code con Karani](https://codewithkarani.com/2021/08/24/setting-up-multi-tenancy-in-erpnext/).
---
 # **Instale y configure Certbot para SSL:**
```sh
sudo apt-get remove certbot
```
```sh
sudo apt install snapd
```
```sh
sudo snap install core
```
```sh
sudo snap refresh core
```
```sh
sudo snap install --classic certbot
```
```sh
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
```sh
sudo certbot --nginx
```
###Elimine los sitios nginx estándar. Para evitar conflictos y evitar vunlerabidades de seguridad  
```sh
sudo rm /etc/nginx/sites-available/default
sudo rm /etc/nginx/sites-enabled/default

```

####**Una vez hecho esto, puede cerrar su cliente SSH, abrir un navegador y escribir la URL de su servidor en la barra de direcciones para acceder a la versión segura de su servidor ERPNext.**

####**Nota final:**
####    Este método específicamente NO cambia el nombre de banco o los directorios del sitio para brindar flexibilidad más adelante en caso de que necesite tener varias URL que apunten a la misma instancia de ERPNext o si necesita cambiar su URL por algún motivo en el futuro. Usar el `bench setup add-domain` comando hace que esto sea mucho más simple. Las instrucciones del certificado SSL proporcionadas están diseñadas para admitir esta configuración.

https://copilot.microsoft.com/shares/BHV2XHXvYjJB5SX1tiCph

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

¡Todo lo mejor! 🙂

---

Para más detalles, consulta la [guía completa](https://codewithkarani.com/2023/12/31/how-to-install-erpnext-version-15-in-ubuntu-a-step-by-step-guide/).
```

Espero que esto te sea útil. Si necesitas más ayuda, aquí estoy. 🚀
```

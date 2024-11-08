
# Guía de Instalación de ERPNext

Lea cada línea cuidadosamente. ¡No te saltes ningún paso! En cualquier lugar donde vea `[USUARIO]`, debe insertar el nombre de usuario de su servidor.

## Pasos de Instalación

1. **Elija cualquier nube en la que confíe.**
2. **Inicie sesión en su sistema a través de SSH con el usuario root** (utilizo PuTTY Client para esto):
   ```bash
   ssh root@[IP_DEL_SERVIDOR]
   ```
3. **Actualice y reinicie el sistema:**
   ```bash
   apt update && apt upgrade -y && shutdown -r now
   ```
4. **Vuelva a iniciar sesión como usuario root y luego escriba los siguientes comandos:**
   ```bash
   apt-get install curl git
   apt-get install sudo
   curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
   ```
5. **Instale todos los requisitos previos para un servidor en funcionamiento:**
   ```bash
   apt install -y nodejs mariadb-server redis-server python3-pip nginx python3-testresources python3-distutils python3-setuptools libssl-dev wkhtmltopdf
   ```
6. **Agregue el usuario para que sirva como directorio de inicio para la instalación de ERPNext:**
   ```bash
   adduser [USUARIO]
   usermod -aG sudo [USUARIO]
   ```
7. **Edite el archivo de configuración de MySQL:**
   ```bash
   nano /etc/mysql/my.cnf
   ```
   Agregue las siguientes líneas al final del archivo:
   ```
   [mysqld]
   character-set-client-handshake = FALSE
   character-set-server = utf8mb4
   collation-server = utf8mb4_unicode_ci

   [mysql]
   default-character-set = utf8mb4
   ```
8. **Reinicie MySQL:**
   ```bash
   service mysql restart
   ```
9. **Configure MySQL (MariaDB) para que funcione correctamente:**
   ```bash
   mysql_secure_installation
   ```
   Responda las preguntas de la siguiente manera:
   - ¿Establecer contraseña de root? [S/n]: **S**
   - ¿Eliminar usuarios anónimos? [S/n]: **S**
   - ¿Deshabilitar el inicio de sesión raíz de forma remota? [S/n]: **n**
   - ¿Eliminar la base de datos de prueba y acceder a ella? [S/n]: **S**
   - ¿Recargar tablas de privilegios ahora? [S/n]: **S**

10. **Complete la configuración de MySQL:**
    ```bash
    mysql -u root -p[your-password-here]
    ```
    Luego, en el indicador de la base de datos, escriba los siguientes comandos:
    ```sql
    USE mysql;
    UPDATE user SET plugin=' ' WHERE user = 'root';
    FLUSH PRIVILEGES;
    exit
    ```

11. **Cierre la ventana de la terminal (cierre de sesión de root) y abra una nueva con SSH [USUARIO] y ejecute los siguientes comandos:**
    ```bash
    sudo npm install -g yarn
    pip3 install frappe-bench
    export PATH=$PATH:/home/[USUARIO]/.local/bin
    sudo pip3 install frappe-bench
    sudo apt install python3.8-venv
    bench init --frappe-branch version-13 frappe-bench
    cd frappe-bench
    bench new-site site1.local
    bench get-app --branch version-13 erpnext
    bench --site site1.local install-app erpnext
    sudo /usr/bin/python3 -m pip install ansible  
    sudo bench setup production [USUARIO]
    ```

12. **Verifique la existencia de un archivo de configuración importante:**
    ```bash
    sudo ls /etc/supervisor/conf.d
    ```
    Si `supervisor.conf` aparece como un archivo aquí, puede saltar al paso 'habilitar el programador' a continuación. Sin embargo, si no hay archivos en la lista, ejecute el siguiente comando:
    ```bash
    sudo cp ~/frappe-bench/config/supervisor.conf /etc/supervisor/conf.d/supervisor.conf
    ```
    Edite el archivo para que se vea así:
    ```
    [unix_http_server]
    file =/var/tmp/supervisord.sock
    chmod=0700
    chown=frappe:frappe
    ```

    Luego, reinicie el supervisor:
    ```bash
    sudo -A systemctl restart supervisor
    ```

13. **Solucione el problema de los DocType:**
    ```bash
    bench --site xxxxl restore visual.sql.gz
    bench update --patch
    bench update --reset --requirements
    bench --site xxxx migrate
    bench update --patch
    bench restart
    ```

14. **Habilite el programador:**
    ```bash
    bench --site site1.local enable-scheduler
    ```

15. **Reinicie el servidor. Una vez reiniciado, abra un navegador web y escriba la dirección IP de su servidor para obtener la pantalla de inicio de sesión de ERPNext. Inicie sesión como administrador y siga el asistente de configuración antes de intentar realizar cualquier otro trabajo en el servidor.**

16. **Configure su URL y su certificado SSL:**
    ```bash
    export LC_ALL=C.UTF-8
    cd frappe-bench
    bench config dns_multitenant on
    bench setup add-domain --site site1.local [your.new.URL]
    bench setup nginx
    sudo service nginx restart
    cd ~
    ```

17. **Instale y configure Certbot para SSL:**
    ```bash
    sudo apt-get remove certbot
    sudo apt install snapd
    sudo snap install core
    sudo snap refresh core
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    sudo certbot --nginx
    ```

18. **Una vez hecho esto, puede cerrar su cliente SSH, abrir un navegador y escribir la URL de su servidor en la barra de direcciones para acceder a la versión segura de su servidor ERPNext.**

19. **Nota final:**
    Este método específicamente NO cambia el nombre de banco o los directorios del sitio para brindar flexibilidad más adelante en caso de que necesite tener varias URL que apunten a la misma instancia de ERPNext o si necesita cambiar su URL por algún motivo en el futuro. Usar el `bench setup add-domain` comando hace que esto sea mucho más simple. Las instrucciones del certificado SSL proporcionadas están diseñadas para admitir esta configuración.

Espero que esto ayude, pero como siempre, ¡asegúrate de seguir cada paso cuidadosamente!
```



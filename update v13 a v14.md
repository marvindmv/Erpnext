

# Actualización de ERPNext de la versión 13 a la versión 14

La versión en video de este documento está disponible en [YouTube](https://youtu.be/9S3APcmAql8), donde explicamos el concepto y los procedimientos en detalle.

**Nota:** NO necesita actualizar Ubuntu de 20+ a 22+ para actualizar ERPNext a la versión 14. ERPNext y Frappe 14 funcionan tanto en Ubuntu 20+ como en 22+.

## Pasos para la actualización

1. **Tomar copia de seguridad.** (¿Debería explicar eso?)

2. **Asegúrate de no tener ninguna personalización que no esté comprometida.**
    ```bash
    cd /opt/bench/frappe-bench/apps/erpnext
    git status
    ```
    ```bash
    cd /opt/bench/frappe-bench/apps/frappe
    git status
    ```

3. **Comprueba la versión de Python**
    ```bash
    python3 -V
    ```

4. **Comprueba la versión del nodo.**
    ```bash
    node --version
    ```

5. **Comprueba la versión de pip o pip3**
    ```bash
    pip3 --version
    ```

6. **Actualizar Python**
    ```bash
    sudo apt install software-properties-common -y
    sudo add-apt-repository ppa:deadsnakes/ppa
    sudo apt install python3.10 python3.10-dev python3.10-distutils
    python3.10 --version
    sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1
    sudo apt install python-is-python3
    python -V
    python3 -V
    ```

7. **Actualizar PIP**
    ```bash
    curl -sS https://bootstrap.pypa.io/get-pip.py | python3.10
    pip3 install --upgrade pip
    sudo apt-get remove python3-apt -y
    sudo apt-get install python3-apt -y
    pip --version
    pip3 --version
    ```

8. **Actualizar Nodo**
    ```bash
    curl -sL https://deb.nodesource.com/setup_16.x | bash -
    sudo apt-get install -y nodejs redis-server
    node --version
    ```

9. **Actualizar NPM**
    ```bash
    npm install -g npm@8.19.1
    npm --version
    ```

10. **Mueve tu antigua carpeta python env a env-archive**
    ```bash
    cd /opt/bench/frappe-bench/
    mv env env-archive
    ```

11. **Crea un nuevo entorno virtual para python-3.10**
    ```bash
    pip install virtualenv
    virtualenv --python python3.10 env
    env/bin/pip install -U pip
    ```

12. **Cambiar git upstream de V13 a V14**
    ```bash
    env/bin/pip install -e apps/frappe -e apps/erpnext
    pip3 install frappe-bench
    bench switch-to-branch version-14 frappe erpnext --upgrade
    cd /opt/bench/frappe-bench/apps/erpnext
    git status
    cd /opt/bench/frappe-bench/apps/frappe
    git status
    ```

13. **Instalar y actualizar V14**
    ```bash
    bench get-app payments
    bench get-app hrms
    bench update --reset
    bench --site *nombre del sitio* install-app hrms
    bench --site *nombre del sitio* install-app payments
    bench --site v13.erpgulf.com migrate
    sudo service supervisor restart
    sudo service nginx restart
    ```

## Notas

1. **Problemas con nginx debido a un error de tipo de registro**
    ```bash
    vim /etc/nginx/nginx.conf
    ```
    Agrega lo siguiente en la sección http:
    ```nginx
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    ```

2. **Nota de (https://discuss.erpnext.com/u/flexy2ky)**
    Modifique este comando en su documento:
    ```bash
    bench switch-to-branch version-14
    ```
    Como este comando fallará si el sitio tiene aplicaciones personalizadas instaladas, use:
    ```bash
    bench switch-to-branch version-14 frappe erpnext --upgrade
    ```

¡Eso es todo! Comprueba si ERPNext y Frappe se actualizan a V14 correctamente.
```

Espero que esto te sea útil. ¿Hay algo más en lo que pueda ayudarte?

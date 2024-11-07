¡Claro! Aquí tienes una versión de la información convertida para un archivo README de GitHub:

```markdown
# Guía de Instalación de Docker y ERPNext

Esta guía te ayudará a instalar Docker y ERPNext en tu sistema Ubuntu 22.04. Sigue los pasos a continuación para configurar todo correctamente.

## Instalación de Docker

1. **Actualiza los paquetes del sistema**:
   ```sh
   sudo apt update
   sudo apt upgrade -y
   ```

2. **Instala las dependencias necesarias**:
   ```sh
   sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
   ```

3. **Añade la clave GPG de Docker**:
   ```sh
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. **Añade el repositorio de Docker**:
   ```sh
   echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. **Instala Docker**:
   ```sh
   sudo apt update
   sudo apt install docker-ce docker-ce-cli containerd.io -y
   ```

6. **Verifica la instalación de Docker**:
   ```sh
   sudo systemctl status docker
   ```

7. **Añade tu usuario al grupo de Docker**:
   ```sh
   sudo usermod -aG docker ${USER}
   newgrp docker
   ```

8. **Prueba Docker**:
   ```sh
   docker run hello-world
   ```

## Instalación de Docker Compose

1. **Descarga Docker Compose**:
   ```sh
   sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

2. **Aplica permisos ejecutables al binario**:
   ```sh
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. **Verifica la instalación de Docker Compose**:
   ```sh
   docker-compose --version
   ```

## Instalación de ERPNext

1. **Clona el repositorio de Frappe Docker**:
   ```sh
   git clone https://github.com/frappe/frappe_docker
   cd frappe_docker
   ```

2. **Levanta los contenedores de ERPNext**:
   ```sh
   docker compose -f pwd.yml up -d
   ```

3. **Accede a ERPNext**:
   - URL: `http://localhost:8080`
   - Usuario: `Administrator`
   - Contraseña: `admin`

4. **Consideraciones**:
   - Al iniciar ERPNext, es recomendable dejarlo sin datos de ejemplo para evitar problemas al instalar nuevas aplicaciones.

## Comandos Útiles

- **Detener todos los contenedores**:
  ```sh
  sudo docker stop $(sudo docker ps -q)
  ```

- **Iniciar todos los contenedores**:
  ```sh
  docker start $(docker ps -a -q)
  ```

- **Eliminar todas las imágenes de Docker**:
  ```sh
  sudo docker system prune --all --force --volumes
  ```

- **Ejecutar un shell interactivo dentro de un contenedor Docker**:
  ```sh
  sudo docker exec -it <NAME> /bin/bash
  ```
  Reemplaza `<NAME>` por el ID o el nombre del contenedor de Docker.

## Instalación de la Aplicación HRMS en ERPNext

1. **Accede al contenedor frontend**:
   ```sh
   sudo docker exec -it b9ef7a659f3e /bin/bash
   cd apps/
   bench get-app hrms
   cd frappe-bench
   bench --site frontend install-app hrms
   bench version
   bench app-list
   bench list-apps
   bench migrate --skip-failing
   exit
   ```

2. **Accede al contenedor backend**:
   ```sh
   sudo docker exec -it 883f932dcf79 /bin/bash
   cd apps/
   bench get-app hrms
   cd frappe-bench
   bench --site frontend install-app hrms
   bench --site frontend list-apps
   bench migrate --skip-failing
   exit
   ```

3. **Reinicia los contenedores**:
   ```sh
   sudo docker stop $(sudo docker ps -q)
   docker start $(docker ps -a -q)
   ```

## Configuración de Firewall en Google Cloud

- Configura el firewall para permitir el puerto 8080:
  - TCP: Agregar a 60000 para que acepte el puerto 8080.
  - Entrada: `http-server`
  - Intervalos de IP: `0.0.0.0/0`
  - TCP: `80-60000`
  - Permitir: `1000`
  - Default: `Desactivado`

Para más detalles, consulta la [guía completa](https://codewithkarani.com/2024/04/06/beginners-guide-to-installing-docker-on-ubuntu-22-04-a-step-by-step-tutorial/) y el [video tutorial](https://www.youtube.com/watch?v=qz5LjuLkr8Q&ab_channel=RuruSabado).

---

Espero que esto te sea útil. Si necesitas más ayuda, aquí estoy. 🚀
```

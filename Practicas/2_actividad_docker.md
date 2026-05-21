# Actividad práctica: Instalación y uso de Docker en Ubuntu Server

---

## 1. Objetivos de aprendizaje

Al finalizar la actividad, podrás:

1. Instalar Docker Engine en Ubuntu Server.
2. Verificar el estado del servicio Docker.
3. Ejecutar contenedores usando `docker run`.
4. Publicar puertos entre el servidor y los contenedores.
5. Usar volúmenes para persistir datos.
6. Inspeccionar logs, procesos, imágenes y contenedores.
7. Crear servicios usando Docker Compose.
8. Levantar y detener proyectos definidos en archivos YAML.
9. Comprender la diferencia entre usar `docker run` y `docker compose`.

---

## 2. Requisitos previos

Antes de comenzar, debes contar con:

- Ubuntu Server instalado y con conexión a Internet.
- Usuario con permisos `sudo`.
- Acceso a terminal.
- Red configurada correctamente.
- Conocimientos básicos de comandos Linux.

Comprobar conectividad:

```bash
ping -c 4 ubuntu.com
```

Actualizar el sistema:

```bash
sudo apt update
sudo apt upgrade -y
```

---

## 3. Parte 1: Instalar Docker

### Paso 1: eliminar paquetes conflictivos

Ejecutar:

```bash
sudo apt remove docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc
```

Si algunos paquetes no están instalados, continuar normalmente.

### Paso 2: instalar dependencias

```bash
sudo apt install -y ca-certificates curl
```

### Paso 3: crear directorio para claves

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

### Paso 4: descargar clave oficial de Docker

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```

### Paso 5: asignar permisos a la clave

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### Paso 6: agregar el repositorio oficial

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

### Paso 7: actualizar repositorios

```bash
sudo apt update
```

### Paso 8: instalar Docker y Compose

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Paso 9: verificar el servicio

```bash
sudo systemctl status docker
```

Si Docker no está activo:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

---

## 4. Parte 2: Probar Docker

Ejecutar el contenedor de prueba:

```bash
sudo docker run hello-world
```

Ver contenedores:

```bash
sudo docker ps -a
```

Ver imágenes descargadas:

```bash
sudo docker images
```

Docker habrá descargado una imagen llamada `hello-world` y creado un contenedor que finaliza luego de mostrar un mensaje.

---

## 5. Parte 3: Usar Docker sin sudo

Agregar el usuario actual al grupo `docker`:

```bash
sudo usermod -aG docker $USER
```

Reiniciar la VM:

```bash
sudo reboot
```

Luego de volver a iniciar sesión, probar:

```bash
docker ps
```

Si el comando funciona sin `sudo`, la configuración quedó aplicada.

> Importante: el grupo `docker` otorga permisos elevados sobre el sistema. Usarlo solo con usuarios de confianza.

---

## 6. Parte 4: Ejecutar contenedores con docker run

### Caso 1: contenedor interactivo de Ubuntu

Ejecutar:

```bash
docker run -it ubuntu bash
```

Dentro del contenedor, probar:

```bash
cat /etc/os-release
pwd
ls
exit
```

Verificar que el contenedor quedó detenido:

```bash
docker ps -a
```

### Caso 2: contenedor temporal

Ejecutar:

```bash
docker run --rm ubuntu echo "Hola desde un contenedor"
```

La opción `--rm` elimina el contenedor automáticamente al finalizar.

### Caso 3: servidor web Nginx

Ejecutar:

```bash
docker run -d --name web-nginx -p 8080:80 nginx
```

Verificar:

```bash
docker ps
```

Probar desde el servidor:

```bash
curl http://localhost:8080
```

Probar desde otra computadora de la red:

```text
http://IP_DEL_SERVIDOR:8080
```

Ver logs:

```bash
docker logs web-nginx
```

Detener:

```bash
docker stop web-nginx
```

Eliminar:

```bash
docker rm web-nginx
```

### Caso 4: servidor web con contenido propio

Crear carpeta:

```bash
mkdir -p ~/docker-practica/html
cd ~/docker-practica
```

Crear archivo HTML:

```bash
nano html/index.html
```

Contenido:

```html
<h1>Servidor Nginx ejecutado con Docker</h1>
<p>Contenido servido desde una carpeta del sistema anfitrion.</p>
```

Ejecutar contenedor:

```bash
docker run -d --name web-html -p 8080:80 -v "$PWD/html:/usr/share/nginx/html:ro" nginx
```

Probar:

```bash
curl http://localhost:8080
```

Detener y eliminar:

```bash
docker stop web-html
docker rm web-html
```

### Caso 5: base de datos MariaDB con volumen

Crear volumen:

```bash
docker volume create mariadb_data
```

Ejecutar MariaDB:

```bash
docker run -d \
  --name mariadb-lab \
  -e MARIADB_ROOT_PASSWORD=clave_root \
  -e MARIADB_DATABASE=practica \
  -e MARIADB_USER=usuario \
  -e MARIADB_PASSWORD=clave_usuario \
  -v mariadb_data:/var/lib/mysql \
  mariadb:latest
```

Ver logs iniciales:

```bash
docker logs mariadb-lab
```

Entrar al cliente de MariaDB dentro del contenedor:

```bash
docker exec -it mariadb-lab mariadb -u usuario -p practica
```

Ingresar la contraseña:

```text
clave_usuario
```

Dentro de MariaDB:

```sql
CREATE TABLE prueba (id INT, nombre VARCHAR(50));
INSERT INTO prueba VALUES (1, 'docker');
SELECT * FROM prueba;
exit
```

Detener y eliminar el contenedor:

```bash
docker stop mariadb-lab
docker rm mariadb-lab
```

El volumen queda guardado:

```bash
docker volume ls
```

Eliminar el volumen al terminar:

```bash
docker volume rm mariadb_data
```

---

## 7. Parte 5: Administración básica de Docker

Listar contenedores activos:

```bash
docker ps
```

Listar todos los contenedores:

```bash
docker ps -a
```

Listar imágenes:

```bash
docker images
```

Listar volúmenes:

```bash
docker volume ls
```

Listar redes:

```bash
docker network ls
```

Ver consumo de espacio:

```bash
docker system df
```

Limpiar recursos sin uso:

```bash
docker system prune
```

---

## 8. Parte 6: Crear un proyecto con Docker Compose

Docker Compose permite definir servicios en un archivo YAML.

Crear carpeta:

```bash
mkdir -p ~/compose-nginx/html
cd ~/compose-nginx
```

Crear página:

```bash
nano html/index.html
```

Contenido:

```html
<h1>Nginx con Docker Compose</h1>
<p>Este servicio fue levantado desde compose.yml.</p>
```

Crear archivo:

```bash
nano compose.yml
```

Contenido:

```yaml
services:
  web:
    image: nginx
    container_name: compose-nginx
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    restart: unless-stopped
```

Levantar el servicio:

```bash
docker compose up -d
```

Ver estado:

```bash
docker compose ps
```

Ver logs:

```bash
docker compose logs
```

Probar:

```bash
curl http://localhost:8080
```

Detener y eliminar el proyecto:

```bash
docker compose down
```

---

## 9. Parte 7: Compose con Apache, PHP, MySQL y phpMyAdmin

Usar el archivo existente:

```bash
cd /Users/sysadmin/Desktop/UTU/2026/3--Adm-SSOO/docker
```

En la VM, si esa ruta no existe, crear una carpeta de trabajo y copiar el archivo `lampp-docker-compose.yml`.

Crear la carpeta para el sitio:

```bash
mkdir -p www
```

Crear archivo PHP:

```bash
nano www/index.php
```

Contenido:

```php
<?php
phpinfo();
?>
```

Levantar el proyecto:

```bash
docker compose -f lampp-docker-compose.yml up -d
```

Ver contenedores:

```bash
docker compose -f lampp-docker-compose.yml ps
```

Probar Apache/PHP:

```text
http://IP_DEL_SERVIDOR:8080
```

Probar phpMyAdmin:

```text
http://IP_DEL_SERVIDOR:8081
```

Datos de ejemplo definidos en el archivo:

| Servicio | Valor |
|---|---|
| MySQL root password | `root_password` |
| Base de datos | `mydatabase` |
| Usuario | `user` |
| Contraseña | `user_password` |

Ver logs:

```bash
docker compose -f lampp-docker-compose.yml logs
```

Detener:

```bash
docker compose -f lampp-docker-compose.yml down
```

Para eliminar también el volumen de base de datos:

```bash
docker compose -f lampp-docker-compose.yml down -v
```

---

## 10. Parte 8: Compose con Portainer

Portainer es una interfaz web para administrar Docker.

Entrar a la carpeta:

```bash
cd /Users/sysadmin/Desktop/UTU/2026/3--Adm-SSOO/docker
```

Levantar Portainer:

```bash
docker compose -f portainer-docker-compose.yaml up -d
```

Probar en navegador:

```text
http://IP_DEL_SERVIDOR:9000
```

Ver estado:

```bash
docker compose -f portainer-docker-compose.yaml ps
```

Detener:

```bash
docker compose -f portainer-docker-compose.yaml down
```

---

## 11. Parte 9: Comparar docker run y Docker Compose

Completar la comparación durante la práctica:

| Tarea | docker run | Docker Compose |
|---|---|---|
| Ejecutar un contenedor simple | Directo y rápido | Requiere archivo YAML |
| Repetir la misma configuración | Hay que recordar el comando | Queda guardada en el archivo |
| Varios contenedores relacionados | Más incómodo | Más claro y ordenado |
| Puertos y volúmenes | Se escriben como opciones | Se declaran en YAML |
| Uso recomendado | Pruebas rápidas | Servicios y proyectos |

---

## 12. Resolución de problemas frecuentes

### Docker no inicia

Verificar:

```bash
sudo systemctl status docker
sudo journalctl -u docker --no-pager
```

### El puerto ya está ocupado

Ver qué proceso usa un puerto:

```bash
sudo ss -tulnp
```

Cambiar el puerto publicado. Por ejemplo, usar `8082:80` en lugar de `8080:80`.

### No se puede acceder desde otra computadora

Verificar:

```bash
ip addr
docker ps
sudo ufw status
```

Si el firewall está activo, permitir el puerto:

```bash
sudo ufw allow 8080/tcp
```

### Compose muestra error de YAML

Revisar:

- Indentación con espacios.
- No usar tabuladores.
- Comillas en puertos como `"8080:80"`.
- Nombre correcto del archivo.

---

## 13. Comandos principales

| Comando | Función |
|---|---|
| `docker run imagen` | Ejecuta un contenedor desde una imagen. |
| `docker ps` | Lista contenedores activos. |
| `docker ps -a` | Lista todos los contenedores. |
| `docker images` | Lista imágenes descargadas. |
| `docker logs nombre` | Muestra logs de un contenedor. |
| `docker exec -it nombre bash` | Abre terminal dentro de un contenedor. |
| `docker stop nombre` | Detiene un contenedor. |
| `docker rm nombre` | Elimina un contenedor. |
| `docker volume ls` | Lista volúmenes. |
| `docker compose up -d` | Levanta servicios definidos en Compose. |
| `docker compose ps` | Muestra servicios del proyecto. |
| `docker compose logs` | Muestra logs del proyecto. |
| `docker compose down` | Detiene y elimina servicios del proyecto. |

---

## 14. Cierre de la actividad

Docker permite ejecutar servicios de forma aislada y repetible. Con `docker run` se pueden hacer pruebas rápidas y levantar contenedores individuales. Con Docker Compose se puede definir una aplicación completa en un archivo YAML, lo que facilita repetir la configuración, compartirla y mantenerla ordenada.

Al finalizar, tendrás una base práctica para ejecutar servicios web, bases de datos y aplicaciones usando contenedores en Ubuntu Server.

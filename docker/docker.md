---
layout: default
title: "Recetario Docker en Ubuntu Server"
---

[← Volver al portal](../)

# Docker en Ubuntu Server

---

## 1. ¿Qué es Docker?

**Docker** es una plataforma que permite ejecutar aplicaciones dentro de **contenedores**.

Un contenedor es un entorno aislado que incluye todo lo necesario para ejecutar una aplicación: archivos, dependencias, bibliotecas, configuración y procesos. A diferencia de una máquina virtual, un contenedor no necesita instalar un sistema operativo completo por separado, porque comparte el kernel del sistema anfitrión.

Docker se usa mucho en servidores, desarrollo web, laboratorios, pruebas, despliegue de aplicaciones y administración de servicios.

Ejemplos de servicios que pueden ejecutarse con Docker:

- Servidores web como Nginx o Apache.
- Bases de datos como MariaDB, MySQL o PostgreSQL.
- Aplicaciones como WordPress, Nextcloud o Portainer.
- Herramientas de monitoreo.
- Entornos de prueba para desarrollo.

---

## 2. ¿Para qué sirve Docker?

Docker permite ejecutar aplicaciones de forma rápida, repetible y aislada.

Sirve para:

- Instalar servicios sin modificar demasiado el sistema principal.
- Probar aplicaciones sin ensuciar el servidor.
- Ejecutar varias versiones de un mismo servicio.
- Mover una aplicación entre equipos con menos problemas.
- Automatizar despliegues.
- Crear laboratorios de red, servicios y servidores.
- Separar aplicaciones en contenedores independientes.

Ejemplo:

En vez de instalar manualmente Apache, PHP y MariaDB en el sistema, se pueden ejecutar esos servicios en contenedores. Si algo falla, se puede borrar el contenedor y volver a crearlo.

---

## 3. Conceptos básicos

### Imagen

Una **imagen** es una plantilla lista para crear contenedores.

Ejemplos:

- `ubuntu`
- `nginx`
- `mariadb`
- `wordpress`
- `nextcloud`

Una imagen puede descargarse desde un registro como Docker Hub.

### Contenedor

Un **contenedor** es una instancia en ejecución creada a partir de una imagen.

Ejemplo:

```bash
docker run nginx
```

Ese comando crea y ejecuta un contenedor usando la imagen `nginx`.

### Volumen

Un **volumen** permite guardar datos fuera del contenedor.

Esto es importante porque los contenedores pueden eliminarse y recrearse. Si los datos importantes están en un volumen, no se pierden al borrar el contenedor.

Ejemplos de datos que conviene guardar en volúmenes:

- Bases de datos.
- Archivos subidos por usuarios.
- Configuraciones persistentes.

### Puerto

Un contenedor puede exponer servicios mediante puertos.

Ejemplo:

```bash
docker run -p 8080:80 nginx
```

Esto conecta el puerto `8080` del servidor con el puerto `80` del contenedor.

Luego se puede abrir en el navegador:

```text
http://IP_DEL_SERVIDOR:8080
```

### Red

Docker puede crear redes internas para que los contenedores se comuniquen entre sí.

Por ejemplo, un contenedor con WordPress puede comunicarse con otro contenedor que ejecuta MariaDB usando una red Docker.

---

## 4. Docker y máquinas virtuales

Docker no reemplaza siempre a las máquinas virtuales. Son tecnologías diferentes.

| Característica | Máquina virtual | Contenedor Docker |
|---|---|---|
| Sistema operativo | Incluye un sistema completo | Comparte el kernel del anfitrión |
| Consumo de recursos | Mayor | Menor |
| Inicio | Más lento | Muy rápido |
| Aislamiento | Muy fuerte | Fuerte, pero más liviano |
| Uso típico | Sistemas completos | Aplicaciones y servicios |

En clase, se puede usar Ubuntu Server dentro de VirtualBox y luego instalar Docker dentro de esa VM. Así se obtiene un entorno seguro para practicar.

---

## 5. Instalación de Docker en Ubuntu Server

Las siguientes instrucciones usan el repositorio oficial de Docker para Ubuntu.

### Paso 1: actualizar el sistema

```bash
sudo apt update
sudo apt upgrade -y
```

### Paso 2: eliminar paquetes conflictivos

Si existen paquetes viejos o no oficiales, eliminarlos:

```bash
sudo apt remove docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc
```

Si `apt` indica que alguno no está instalado, no hay problema.

### Paso 3: instalar dependencias

```bash
sudo apt install -y ca-certificates curl
```

### Paso 4: crear el directorio para claves

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

### Paso 5: descargar la clave oficial de Docker

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```

### Paso 6: dar permisos de lectura a la clave

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### Paso 7: agregar el repositorio oficial

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

### Paso 8: actualizar la lista de paquetes

```bash
sudo apt update
```

### Paso 9: instalar Docker Engine

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Este comando instala:

- `docker-ce`: motor principal de Docker.
- `docker-ce-cli`: cliente de línea de comandos.
- `containerd.io`: runtime de contenedores.
- `docker-buildx-plugin`: herramienta moderna para construir imágenes.
- `docker-compose-plugin`: plugin de Docker Compose.

### Paso 10: verificar el servicio

```bash
sudo systemctl status docker
```

Si Docker no está iniciado:

```bash
sudo systemctl start docker
```

Activar Docker al inicio del sistema:

```bash
sudo systemctl enable docker
```

### Paso 11: probar Docker

```bash
sudo docker run hello-world
```

Si la instalación funciona, Docker descargará una imagen de prueba y ejecutará un contenedor que muestra un mensaje de confirmación.

---

## 6. Usar Docker sin sudo

Por defecto, muchos sistemas requieren `sudo` para ejecutar Docker:

```bash
sudo docker ps
```

Para permitir que el usuario actual use Docker sin escribir `sudo`, agregarlo al grupo `docker`:

```bash
sudo usermod -aG docker $USER
```

Luego cerrar sesión y volver a entrar, o reiniciar:

```bash
sudo reboot
```

Verificar:

```bash
docker ps
```

> Importante: pertenecer al grupo `docker` otorga permisos administrativos sobre Docker. En servidores reales debe usarse con cuidado.

---

## 7. Comandos básicos de Docker

Ver versión:

```bash
docker --version
```

Ver información general:

```bash
docker info
```

Listar contenedores en ejecución:

```bash
docker ps
```

Listar todos los contenedores:

```bash
docker ps -a
```

Listar imágenes descargadas:

```bash
docker images
```

Descargar una imagen:

```bash
docker pull nginx
```

Ejecutar un contenedor:

```bash
docker run nginx
```

Ejecutar un contenedor en segundo plano:

```bash
docker run -d nginx
```

Ejecutar Nginx publicando un puerto:

```bash
docker run -d --name web -p 8080:80 nginx
```

Ver logs de un contenedor:

```bash
docker logs web
```

Entrar a una terminal dentro de un contenedor:

```bash
docker exec -it web bash
```

Detener un contenedor:

```bash
docker stop web
```

Iniciar un contenedor detenido:

```bash
docker start web
```

Eliminar un contenedor:

```bash
docker rm web
```

Eliminar una imagen:

```bash
docker rmi nginx
```

---

## 8. Ejemplo práctico: ejecutar un servidor web

Crear un contenedor con Nginx:

```bash
docker run -d --name servidor-web -p 8080:80 nginx
```

Verificar que esté corriendo:

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

Detener el contenedor:

```bash
docker stop servidor-web
```

Eliminar el contenedor:

```bash
docker rm servidor-web
```

---

## 9. ¿Qué es Docker Compose?

**Docker Compose** es una herramienta para definir y ejecutar aplicaciones formadas por uno o varios contenedores.

En lugar de escribir comandos largos con `docker run`, se crea un archivo YAML llamado normalmente:

```text
docker-compose.yml
```

o:

```text
compose.yml
```

En ese archivo se declaran:

- Servicios.
- Imágenes.
- Puertos.
- Volúmenes.
- Variables de entorno.
- Redes.
- Dependencias entre contenedores.

Docker Compose es muy útil cuando una aplicación necesita varios servicios.

Ejemplos:

- WordPress + MariaDB.
- Nextcloud + base de datos.
- Apache + PHP + MariaDB + phpMyAdmin.
- Aplicación web + API + base de datos.

---

## 10. Instalar Docker Compose

En Ubuntu Server, Docker Compose se instala como plugin del comando `docker`.

Si se siguió la instalación anterior, Compose ya quedó instalado con este paquete:

```bash
docker-compose-plugin
```

Para instalarlo o actualizarlo manualmente:

```bash
sudo apt update
sudo apt install -y docker-compose-plugin
```

Verificar versión:

```bash
docker compose version
```

> Nota: el comando moderno es `docker compose` con espacio. El comando antiguo `docker-compose` con guion pertenece a versiones anteriores.

---

## 11. Ejemplo básico de Docker Compose

Crear una carpeta de trabajo:

```bash
mkdir nginx-compose
cd nginx-compose
```

Crear el archivo:

```bash
nano compose.yml
```

Contenido:

```yaml
services:
  web:
    image: nginx
    container_name: nginx-compose
    ports:
      - "8080:80"
    restart: unless-stopped
```

Levantar el servicio:

```bash
docker compose up -d
```

Ver contenedores del proyecto:

```bash
docker compose ps
```

Ver logs:

```bash
docker compose logs
```

Detener el servicio:

```bash
docker compose down
```

---

## 12. Ejemplo con volumen

Crear carpeta:

```bash
mkdir sitio-web
cd sitio-web
mkdir html
```

Crear una página:

```bash
nano html/index.html
```

Contenido de ejemplo:

```html
<h1>Sitio servido con Docker Compose</h1>
```

Crear `compose.yml`:

```yaml
services:
  web:
    image: nginx
    container_name: sitio-web
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    restart: unless-stopped
```

Iniciar:

```bash
docker compose up -d
```

Probar:

```text
http://IP_DEL_SERVIDOR:8080
```

Detener y borrar el contenedor:

```bash
docker compose down
```

Los archivos dentro de `html/` quedan guardados en el sistema anfitrión.

---

## 13. Comandos básicos de Docker Compose

Levantar servicios:

```bash
docker compose up -d
```

Detener y eliminar contenedores del proyecto:

```bash
docker compose down
```

Ver estado:

```bash
docker compose ps
```

Ver logs:

```bash
docker compose logs
```

Ver logs en tiempo real:

```bash
docker compose logs -f
```

Reiniciar servicios:

```bash
docker compose restart
```

Descargar imágenes definidas en el archivo:

```bash
docker compose pull
```

Reconstruir y levantar:

```bash
docker compose up -d --build
```

---

## 14. Buenas prácticas iniciales

- Usar nombres claros para contenedores y carpetas.
- Guardar los archivos `compose.yml` en carpetas separadas por proyecto.
- Usar volúmenes para datos importantes.
- No guardar contraseñas reales en archivos compartidos públicamente.
- Revisar logs cuando un contenedor falla.
- Mantener imágenes actualizadas.
- Detener y eliminar contenedores que ya no se usan.
- No ejecutar contenedores desconocidos sin revisar su origen.

---

## 15. Limpieza y mantenimiento

Ver uso de espacio:

```bash
docker system df
```

Eliminar contenedores detenidos, redes sin uso e imágenes no utilizadas:

```bash
docker system prune
```

Eliminar también volúmenes sin uso:

```bash
docker system prune --volumes
```

> Precaución: antes de eliminar volúmenes, verificar que no contengan datos importantes.

Actualizar imágenes de un proyecto Compose:

```bash
docker compose pull
docker compose up -d
```

---

## 16. Desinstalar Docker

Eliminar paquetes principales:

```bash
sudo apt purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
```

Eliminar datos de Docker:

```bash
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

Eliminar repositorio y clave:

```bash
sudo rm -f /etc/apt/sources.list.d/docker.sources
sudo rm -f /etc/apt/keyrings/docker.asc
sudo apt update
```

> Precaución: eliminar `/var/lib/docker` borra imágenes, contenedores, redes y volúmenes locales.

---

## 17. Resumen

Docker permite ejecutar aplicaciones en contenedores livianos y aislados.

Docker Compose permite definir varios contenedores en un archivo YAML y administrarlos como un solo proyecto.

Comandos principales:

| Comando | Función |
|---|---|
| `docker ps` | Lista contenedores en ejecución. |
| `docker ps -a` | Lista todos los contenedores. |
| `docker images` | Lista imágenes descargadas. |
| `docker run imagen` | Ejecuta un contenedor. |
| `docker stop nombre` | Detiene un contenedor. |
| `docker rm nombre` | Elimina un contenedor. |
| `docker logs nombre` | Muestra logs. |
| `docker compose up -d` | Levanta servicios en segundo plano. |
| `docker compose down` | Detiene y elimina servicios del proyecto. |
| `docker compose logs -f` | Muestra logs en tiempo real. |

---

## 18. Fuentes

- Documentación oficial de Docker: instalación de Docker Engine en Ubuntu.  
  https://docs.docker.com/engine/install/ubuntu/
- Documentación oficial de Docker: instalación del plugin Docker Compose.  
  https://docs.docker.com/compose/install/linux/

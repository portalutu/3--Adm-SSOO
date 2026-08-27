---
layout: default
title: "Práctica 4 — Ubuntu Server minimalista y despliegue con Docker"
---

[← Volver al portal](../)

# Actividad práctica: Ubuntu Server minimalista, administración inicial y despliegue de servicios con Docker

**Curso:** 3.o EMT - Administración de Sistemas Operativos  
**Orientación:** Tecnologías de la Información  
**Modalidad:** Práctica guiada en laboratorio o entorno de virtualización  
**Duración sugerida:** 3 a 5 clases de 3 horas  
**Herramientas principales:** VirtualBox, Ubuntu Server, terminal Bash, Netplan, Docker, Docker Compose, Webmin y Portainer

## 1. Fundamentación

En esta actividad instalarás y configurarás un servidor Ubuntu Server en VirtualBox utilizando una instalación minimalista. Trabajarás sobre un escenario cercano a la administración real de servidores: creación de una máquina virtual, configuración de red, mantenimiento inicial del sistema operativo, revisión de archivos de configuración, control de servicios y despliegue de aplicaciones mediante contenedores.

La práctica se vincula directamente con la administración de sistemas operativos desde consola y con el uso de máquinas virtuales y contenedores para resolver necesidades de infraestructura. Durante el trabajo no solo ejecutarás comandos: también deberás comprender qué modifica cada paso, cómo verificar el resultado y qué evidencias puedes conservar para justificar técnicamente tus decisiones.

## 2. Competencias abordadas

Esta actividad contribuye especialmente al desarrollo de las siguientes competencias específicas tecnológicas de la unidad curricular:

- **CET1:** Implementa la administración de servicios del sistema operativo mediante terminal Bash y herramientas de scripting, automatizando o ejecutando tareas rutinarias del sistema desde el lado del servidor.
- **CET2:** Analiza y evalúa la gestión de máquinas virtuales y contenedores para brindar soluciones eficientes y realizar el despliegue de aplicaciones.

También se promueven competencias generales del MCN vinculadas al pensamiento crítico, pensamiento computacional, pensamiento científico, resolución de problemas, comunicación técnica y trabajo autónomo.

## 3. Objetivos de aprendizaje

Al finalizar la actividad, podrás:

- Crear una máquina virtual en VirtualBox para ejecutar Ubuntu Server.
- Instalar Ubuntu Server en modalidad minimalista.
- Verificar la configuración de red recibida por DHCP antes de modificarla.
- Configurar nombre de host e IP fija utilizando Netplan.
- Actualizar el sistema operativo y realizar limpieza de paquetes y archivos obsoletos.
- Identificar archivos de configuración relevantes del sistema operativo.
- Verificar servicios activos y su estado mediante `systemctl`.
- Instalar y utilizar herramientas de consola para administración, diagnóstico y monitoreo.
- Explicar qué son Docker y Docker Compose, y reconocer casos de uso.
- Instalar Docker, Docker Compose, Webmin y Portainer.
- Desplegar servicios con `docker compose`: Apache con PHP8, Nginx, MariaDB, phpMyAdmin e IT-Tools.
- Registrar evidencias técnicas del proceso realizado.

## 4. Requisitos previos

Antes de comenzar, deberás contar con:

- VirtualBox instalado.
- Imagen ISO de Ubuntu Server 24.04 LTS o versión LTS reciente.
- Conexión a Internet.
- Conocimientos básicos de terminal Linux.
- Conocimientos básicos de direccionamiento IP, puerta de enlace, DNS y máscara de red.

## 5. Escenario de trabajo

Instalarás un servidor Ubuntu Server en una máquina virtual. El servidor tendrá una IP fija dentro de la red local o de la red NAT configurada en VirtualBox. Sobre ese servidor instalarás herramientas de administración y luego desplegarás varios servicios usando contenedores.

Servicios finales esperados:

| Servicio | Tipo de instalación | Puerto sugerido |
| --- | --- | --- |
| Webmin | Instalado en el sistema operativo | `10000` |
| Portainer | Contenedor Docker | `9000` y `9443` |
| Apache con PHP8 | Contenedor Docker Compose | `8080` |
| Nginx | Contenedor Docker Compose | `8081` |
| MariaDB | Contenedor Docker Compose | `3306` |
| phpMyAdmin | Contenedor Docker Compose | `8082` |
| IT-Tools | Contenedor Docker Compose | `8083` |

> Nota: se utilizan puertos diferentes para evitar conflictos entre Apache, Nginx, phpMyAdmin, IT-Tools, Webmin y Portainer.

## 6. Parte 1 - Creación de la máquina virtual en VirtualBox

### Paso 1: Crear la máquina virtual

1. Abrir VirtualBox.
2. Seleccionar **Nueva**.
3. Asignar un nombre descriptivo, por ejemplo:

```text
ubuntu-server-docker
```

4. Seleccionar tipo **Linux** y versión **Ubuntu (64-bit)**.
5. Asignar memoria RAM:

```text
Mínimo: 2048 MB
Recomendado: 4096 MB
```

6. Asignar procesadores:

```text
Mínimo: 2 CPU
Recomendado: 2 o 4 CPU, según el equipo disponible
```

7. Crear un disco virtual:

```text
Tipo: VDI
Tamaño: 25 GB como mínimo
Asignación: dinámica
```

### Paso 2: Configurar la red de la máquina virtual

Para esta práctica se puede usar una de las siguientes opciones:

| Modo de red | Uso recomendado |
| --- | --- |
| Adaptador puente | Permite que la VM aparezca como otro equipo dentro de la red física. Recomendado si se desea acceder desde otros equipos del aula. |
| NAT | Permite salida a Internet, pero dificulta el acceso desde otros equipos. Útil para prácticas individuales. |
| Red NAT | Permite que varias VMs se comuniquen entre sí dentro de una red virtual. Útil para laboratorio. |

Recomendación para esta actividad:

```text
Adaptador puente, si la red del aula lo permite.
Red NAT, si se desea un entorno controlado.
```

### Paso 3: Montar la ISO de Ubuntu Server

1. Ir a **Configuración > Almacenamiento**.
2. Seleccionar la unidad óptica.
3. Cargar la ISO de Ubuntu Server.
4. Iniciar la máquina virtual.

## 7. Parte 2 - Instalación minimalista de Ubuntu Server

Durante el instalador:

1. Seleccionar idioma.
2. Seleccionar distribución de teclado.
3. En el tipo de instalación, elegir una instalación mínima o evitar paquetes adicionales innecesarios.
4. Configurar red inicialmente por DHCP.
5. Configurar usuario administrador.
6. Asignar un nombre temporal al servidor, por ejemplo:

```text
server-temporal
```

7. Activar instalación de OpenSSH Server si el instalador lo ofrece.
8. No instalar paquetes adicionales del listado de snaps del instalador.
9. Finalizar la instalación.
10. Reiniciar la máquina virtual y retirar la ISO si VirtualBox no lo hace automáticamente.

## 8. Parte 3 - Primer inicio de sesión y verificación inicial

Iniciar sesión con el usuario creado durante la instalación.

Verificar la versión instalada:

```bash
lsb_release -a
```

Verificar el kernel:

```bash
uname -a
```

Verificar el usuario actual:

```bash
whoami
```

Verificar privilegios administrativos:

```bash
sudo -v
```

## 9. Parte 4 - Verificación de red recibida por DHCP

Antes de configurar una IP fija, se debe observar qué valores entregó DHCP. Esto permite tomar decisiones correctas y evitar conflictos de red.

Ver interfaces disponibles:

```bash
ip link
```

Ver direcciones IP asignadas:

```bash
ip addr
```

Ver ruta por defecto:

```bash
ip route
```

Ver servidores DNS utilizados por el sistema:

```bash
resolvectl status
```

Probar conectividad hacia la puerta de enlace:

```bash
ping -c 4 IP_DE_LA_PUERTA_DE_ENLACE
```

Probar conectividad hacia Internet:

```bash
ping -c 4 8.8.8.8
```

Probar resolución DNS:

```bash
ping -c 4 ubuntu.com
```

Registrar los siguientes datos:

| Dato | Valor observado |
| --- | --- |
| Nombre de la interfaz |  |
| IP recibida por DHCP |  |
| Máscara o prefijo CIDR |  |
| Puerta de enlace |  |
| DNS |  |

Ejemplo de interfaz común en VirtualBox:

```text
enp0s3
```

## 10. Parte 5 - Configuración del nombre del host

Ver el nombre actual:

```bash
hostnamectl
```

Cambiar el nombre del host:

```bash
sudo hostnamectl set-hostname srv-docker-01
```

Verificar el cambio:

```bash
hostnamectl
```

Editar el archivo `/etc/hosts`:

```bash
sudo nano /etc/hosts
```

Agregar o ajustar una línea similar:

```text
127.0.1.1 srv-docker-01
```

Verificar resolución local del nombre:

```bash
ping -c 4 srv-docker-01
```

## 11. Parte 6 - Configuración de IP fija con Netplan

### Paso 1: Identificar archivo de configuración de Netplan

Listar archivos:

```bash
ls -l /etc/netplan/
```

Ver contenido actual:

```bash
sudo cat /etc/netplan/*.yaml
```

Crear una copia de seguridad:

```bash
sudo cp /etc/netplan/*.yaml /etc/netplan/backup-netplan.yaml
```

### Paso 2: Editar la configuración

Abrir el archivo de Netplan:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

> El nombre del archivo puede variar. Si el archivo tiene otro nombre, editar el archivo real que exista dentro de `/etc/netplan/`.

Ejemplo de configuración con IP fija:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 192.168.1.50/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

Deberás adaptar:

- `enp0s3`: nombre real de su interfaz.
- `192.168.1.50/24`: IP fija elegida.
- `192.168.1.1`: puerta de enlace real.
- DNS: servidores DNS que se utilizarán.

### Paso 3: Probar y aplicar Netplan

Probar la configuración:

```bash
sudo netplan try
```

Si la conexión funciona, aceptar los cambios.

Aplicar la configuración:

```bash
sudo netplan apply
```

Verificar:

```bash
ip addr
ip route
resolvectl status
ping -c 4 8.8.8.8
ping -c 4 ubuntu.com
```

## 12. Parte 7 - Actualización del sistema y limpieza inicial

Actualizar el índice de paquetes:

```bash
sudo apt update
```

Actualizar paquetes instalados:

```bash
sudo apt upgrade -y
```

Actualizar dependencias de la distribución si corresponde:

```bash
sudo apt full-upgrade -y
```

Eliminar paquetes que ya no son necesarios:

```bash
sudo apt autoremove -y
```

Limpiar paquetes descargados:

```bash
sudo apt autoclean
sudo apt clean
```

Verificar espacio disponible:

```bash
df -h
```

Verificar uso de memoria:

```bash
free -h
```

Reiniciar si hubo actualización de kernel o servicios importantes:

```bash
sudo reboot
```

## 13. Parte 8 - Verificación de archivos de configuración del sistema

En esta sección se revisan archivos importantes del sistema operativo. No todos deben modificarse; el objetivo es reconocer su función.

Ver usuarios del sistema:

```bash
less /etc/passwd
```

Ver grupos:

```bash
less /etc/group
```

Ver configuración de resolución DNS local:

```bash
resolvectl status
```

Ver configuración de hosts locales:

```bash
cat /etc/hosts
```

Ver configuración de repositorios APT:

```bash
ls -l /etc/apt/
ls -l /etc/apt/sources.list.d/
```

Ver logs del sistema:

```bash
journalctl -p warning -n 50
```

Ver mensajes del arranque actual:

```bash
journalctl -b -n 100
```

Ver configuración de SSH:

```bash
sudo cat /etc/ssh/sshd_config
```

Ver configuración de Netplan:

```bash
sudo cat /etc/netplan/*.yaml
```

## 14. Parte 9 - Verificación de servicios en ejecución

Listar servicios activos:

```bash
systemctl list-units --type=service --state=running
```

Ver servicios habilitados al inicio:

```bash
systemctl list-unit-files --type=service --state=enabled
```

Ver estado del servicio SSH:

```bash
systemctl status ssh
```

Ver estado de red:

```bash
systemctl status systemd-networkd
systemctl status systemd-resolved
```

Ver últimos eventos del sistema:

```bash
journalctl -xe
```

Comandos importantes para administrar servicios:

```bash
sudo systemctl start nombre-servicio
sudo systemctl stop nombre-servicio
sudo systemctl restart nombre-servicio
sudo systemctl enable nombre-servicio
sudo systemctl disable nombre-servicio
sudo systemctl status nombre-servicio
```

## 15. Parte 10 - Instalación de utilidades de consola

Las siguientes herramientas ayudan a administrar, diagnosticar y explorar el servidor desde terminal.

Antes de instalar:

```bash
sudo apt update
```

### 15.1. tldr

`tldr` muestra ejemplos simples de uso de comandos. Es útil cuando se necesita recordar rápidamente cómo usar una herramienta sin leer páginas extensas de manual.

Instalación:

```bash
sudo apt install -y tldr
```

Actualizar la base de ejemplos:

```bash
tldr --update
```

Ejemplo:

```bash
tldr tar
```

### 15.2. bat

`bat` es una alternativa mejorada a `cat`, con resaltado de sintaxis y paginación. En Ubuntu, el comando suele instalarse como `batcat`.

Instalación:

```bash
sudo apt install -y bat
```

Ejemplo:

```bash
batcat /etc/hosts
```

Opcionalmente, crear un alias:

```bash
echo "alias bat='batcat'" >> ~/.bashrc
source ~/.bashrc
```

### 15.3. btop

`btop` permite monitorear CPU, memoria, disco, red y procesos con una interfaz interactiva en terminal.

Instalación:

```bash
sudo apt install -y btop
```

Ejecución:

```bash
btop
```

### 15.4. htop

`htop` permite ver procesos, consumo de CPU, memoria y carga del sistema de forma interactiva. Es una alternativa más cómoda que `top`.

Instalación:

```bash
sudo apt install -y htop
```

Ejecución:

```bash
htop
```

### 15.5. ztop

`ztop` es una herramienta de monitoreo en terminal que permite observar recursos del sistema. Según la versión de Ubuntu y los repositorios disponibles, puede instalarse por Snap.

Verificar si Snap está instalado:

```bash
snap version
```

Si Snap no está instalado:

```bash
sudo apt install -y snapd
```

Instalar `ztop`:

```bash
sudo snap install ztop
```

Ejecución:

```bash
ztop
```

> Si el paquete no está disponible en el entorno utilizado, registrar el error y continuar con `htop` y `btop`.

### 15.6. fastfetch

`fastfetch` muestra información resumida del sistema: distribución, kernel, CPU, memoria, disco y otros datos útiles para documentación rápida.

Instalación:

```bash
sudo apt install -y fastfetch
```

Ejecución:

```bash
fastfetch
```

### 15.7. ncdu

`ncdu` permite analizar el uso del disco por carpetas desde terminal. Es útil para encontrar directorios que consumen demasiado espacio.

Instalación:

```bash
sudo apt install -y ncdu
```

Ejemplo:

```bash
sudo ncdu /
```

### 15.8. mc

`mc`, Midnight Commander, es un administrador de archivos para terminal. Permite navegar carpetas, copiar, mover, borrar y editar archivos desde una interfaz de paneles.

Instalación:

```bash
sudo apt install -y mc
```

Ejecución:

```bash
mc
```

## 16. Parte 11 - Introducción a Docker y Docker Compose

### ¿Qué es Docker?

Docker es una plataforma que permite ejecutar aplicaciones dentro de contenedores. Un contenedor incluye la aplicación y las dependencias necesarias para ejecutarla, pero comparte el kernel del sistema operativo anfitrión. Esto permite desplegar servicios de forma más liviana y reproducible que usando una máquina virtual completa por cada aplicación.

Ejemplo: en lugar de instalar manualmente Apache, PHP, MariaDB y phpMyAdmin sobre el sistema operativo principal, se puede ejecutar cada servicio en un contenedor separado.

### ¿Qué es Docker Compose?

Docker Compose permite definir varios contenedores en un archivo YAML llamado `compose.yml` o `docker-compose.yml`. En ese archivo se declaran servicios, imágenes, puertos, volúmenes, redes y variables de entorno.

Con Docker Compose se puede levantar un entorno completo con un solo comando:

```bash
docker compose up -d
```

Y detenerlo con:

```bash
docker compose down
```

### Casos de uso

Docker y Docker Compose se utilizan para:

- Crear entornos de desarrollo reproducibles.
- Desplegar aplicaciones web.
- Separar servicios en contenedores independientes.
- Probar diferentes versiones de software sin modificar el sistema principal.
- Ejecutar bases de datos, servidores web, herramientas administrativas y aplicaciones internas.
- Facilitar migraciones entre servidores.
- Documentar infraestructura como código mediante archivos YAML.

### Conceptos básicos

| Concepto | Explicación |
| --- | --- |
| Imagen | Plantilla desde la cual se crea un contenedor. |
| Contenedor | Instancia en ejecución de una imagen. |
| Volumen | Espacio persistente para conservar datos aunque se elimine el contenedor. |
| Red Docker | Red virtual que permite comunicación entre contenedores. |
| Puerto publicado | Puerto del servidor que se redirige hacia un puerto interno del contenedor. |
| Variable de entorno | Valor de configuración que se entrega al contenedor al iniciarlo. |

## 17. Parte 12 - Instalación de Docker y Docker Compose

Eliminar versiones antiguas si existieran:

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
  sudo apt remove -y $pkg
done
```

Instalar dependencias:

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
```

Crear carpeta para claves:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Descargar clave oficial de Docker:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Ajustar permisos:

```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

Agregar repositorio oficial:

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Actualizar repositorios:

```bash
sudo apt update
```

Instalar Docker Engine y Docker Compose Plugin:

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verificar Docker:

```bash
sudo docker version
sudo docker run hello-world
```

Verificar Docker Compose:

```bash
docker compose version
```

Agregar el usuario actual al grupo `docker`:

```bash
sudo usermod -aG docker $USER
```

Cerrar sesión y volver a ingresar, o reiniciar:

```bash
sudo reboot
```

Después del reinicio, verificar sin `sudo`:

```bash
docker ps
```

## 18. Parte 13 - Instalación de Webmin

Webmin es una herramienta web para administrar servidores Linux desde el navegador. Permite revisar servicios, usuarios, paquetes, logs, red y otros elementos del sistema. No reemplaza el aprendizaje de consola, pero ayuda a visualizar la administración del servidor.

Instalar dependencias:

```bash
sudo apt update
sudo apt install -y curl software-properties-common apt-transport-https
```

Descargar y ejecutar el script oficial de repositorios de Webmin:

```bash
curl -o setup-repos.sh https://raw.githubusercontent.com/webmin/webmin/master/setup-repos.sh
sudo sh setup-repos.sh
```

Instalar Webmin:

```bash
sudo apt install -y webmin --install-recommends
```

Verificar servicio:

```bash
sudo systemctl status webmin
```

Acceder desde navegador:

```text
https://IP_DEL_SERVIDOR:10000
```

Ejemplo:

```text
https://192.168.1.50:10000
```

> El navegador puede mostrar una advertencia de certificado. En un entorno de práctica local esto es esperable, porque Webmin utiliza HTTPS con certificado autofirmado.

## 19. Parte 14 - Despliegue de servicios con Docker Compose

Crear carpeta principal para los contenedores:

```bash
mkdir -p ~/docker-lab
cd ~/docker-lab
```

Crear subcarpetas:

```bash
mkdir -p apache-html nginx-html mariadb-data portainer-data
```

Crear una página de prueba para Apache:

```bash
nano apache-html/index.php
```

Contenido:

```php
<?php
phpinfo();
```

Crear una página de prueba para Nginx:

```bash
nano nginx-html/index.html
```

Contenido:

```html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <title>Nginx en Docker</title>
</head>
<body>
  <h1>Nginx funcionando en Docker</h1>
  <p>Servidor desplegado en la actividad de 3.o EMT.</p>
</body>
</html>
```

Crear archivo `.env`:

```bash
nano .env
```

Contenido sugerido:

```env
MARIADB_ROOT_PASSWORD=CambiarEstaClaveRoot
MARIADB_DATABASE=appdb
MARIADB_USER=appuser
MARIADB_PASSWORD=CambiarEstaClaveUsuario
```

> En un entorno real no se deben usar claves simples ni compartir el archivo `.env`. En esta práctica se utiliza para comprender variables de entorno.

Crear el archivo `compose.yml`:

```bash
nano compose.yml
```

Contenido:

```yaml
services:
  apache:
    image: php:8.2-apache
    container_name: lab_apache_php8
    restart: unless-stopped
    ports:
      - "8080:80"
    volumes:
      - ./apache-html:/var/www/html
    networks:
      - labnet

  nginx:
    image: nginx:stable
    container_name: lab_nginx
    restart: unless-stopped
    ports:
      - "8081:80"
    volumes:
      - ./nginx-html:/usr/share/nginx/html:ro
    networks:
      - labnet

  mariadb:
    image: mariadb:11
    container_name: lab_mariadb
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD: ${MARIADB_ROOT_PASSWORD}
      MARIADB_DATABASE: ${MARIADB_DATABASE}
      MARIADB_USER: ${MARIADB_USER}
      MARIADB_PASSWORD: ${MARIADB_PASSWORD}
    ports:
      - "3306:3306"
    volumes:
      - ./mariadb-data:/var/lib/mysql
    networks:
      - labnet

  phpmyadmin:
    image: phpmyadmin:latest
    container_name: lab_phpmyadmin
    restart: unless-stopped
    environment:
      PMA_HOST: mariadb
      PMA_PORT: 3306
    ports:
      - "8082:80"
    depends_on:
      - mariadb
    networks:
      - labnet

  it-tools:
    image: corentinth/it-tools:latest
    container_name: lab_it_tools
    restart: unless-stopped
    ports:
      - "8083:80"
    networks:
      - labnet

  portainer:
    image: portainer/portainer-ce:latest
    container_name: lab_portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
      - "9443:9443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./portainer-data:/data
    networks:
      - labnet

networks:
  labnet:
    driver: bridge
```

Levantar los servicios:

```bash
docker compose up -d
```

Ver contenedores en ejecución:

```bash
docker compose ps
docker ps
```

Ver logs:

```bash
docker compose logs
```

Ver logs de un servicio específico:

```bash
docker compose logs apache
docker compose logs mariadb
```

## 20. Parte 15 - Verificación de servicios desplegados

Desde el navegador del equipo anfitrión o desde otro equipo de la red, acceder a:

| Servicio | URL |
| --- | --- |
| Apache con PHP8 | `http://IP_DEL_SERVIDOR:8080` |
| Nginx | `http://IP_DEL_SERVIDOR:8081` |
| phpMyAdmin | `http://IP_DEL_SERVIDOR:8082` |
| IT-Tools | `http://IP_DEL_SERVIDOR:8083` |
| Portainer HTTP | `http://IP_DEL_SERVIDOR:9000` |
| Portainer HTTPS | `https://IP_DEL_SERVIDOR:9443` |
| Webmin | `https://IP_DEL_SERVIDOR:10000` |

Verificar Apache desde consola:

```bash
curl http://localhost:8080
```

Verificar Nginx:

```bash
curl http://localhost:8081
```

Verificar IT-Tools:

```bash
curl -I http://localhost:8083
```

Verificar puertos abiertos:

```bash
ss -tulpn
```

Verificar uso de recursos:

```bash
btop
```

## 21. Parte 16 - Pruebas con MariaDB y phpMyAdmin

Ingresar a phpMyAdmin:

```text
http://IP_DEL_SERVIDOR:8082
```

Datos de acceso:

```text
Servidor: mariadb
Usuario: appuser
Clave: CambiarEstaClaveUsuario
Base de datos: appdb
```

También se puede probar desde consola:

```bash
docker exec -it lab_mariadb mariadb -u appuser -p appdb
```

Dentro de MariaDB:

```sql
SHOW DATABASES;
CREATE TABLE prueba (
  id INT AUTO_INCREMENT PRIMARY KEY,
  mensaje VARCHAR(100)
);
INSERT INTO prueba (mensaje) VALUES ('Servidor funcionando correctamente');
SELECT * FROM prueba;
EXIT;
```

## 22. Parte 17 - Comandos básicos de administración Docker

Listar contenedores:

```bash
docker ps
```

Listar todos los contenedores, incluso detenidos:

```bash
docker ps -a
```

Listar imágenes:

```bash
docker images
```

Detener servicios del laboratorio:

```bash
cd ~/docker-lab
docker compose stop
```

Iniciar servicios:

```bash
docker compose start
```

Recrear servicios:

```bash
docker compose up -d
```

Detener y eliminar contenedores de la práctica, conservando datos en carpetas locales:

```bash
docker compose down
```

Eliminar contenedores, redes y volúmenes declarados como volúmenes Docker:

```bash
docker compose down -v
```

> Atención: `docker compose down -v` puede eliminar datos persistentes si se usan volúmenes Docker. En esta práctica MariaDB usa una carpeta local `./mariadb-data`.

## 23. Parte 18 - Limpieza y mantenimiento posterior

Ver espacio usado por Docker:

```bash
docker system df
```

Eliminar imágenes, redes y cachés no utilizados:

```bash
docker system prune
```

Eliminar paquetes innecesarios del sistema:

```bash
sudo apt autoremove -y
sudo apt autoclean
```

Analizar uso de disco:

```bash
sudo ncdu /
```

Ver estado general:

```bash
fastfetch
df -h
free -h
systemctl --failed
```

## 24. Evidencias de verificación

Durante la actividad, deberás verificar y conservar evidencia de los siguientes puntos:

1. Máquina virtual creada en VirtualBox.
2. Ubuntu Server instalado correctamente.
3. Resultado de `lsb_release -a`.
4. Valores de red recibidos por DHCP antes de configurar IP fija.
5. Archivo Netplan configurado con IP fija.
6. Resultado de `ip addr`, `ip route` y prueba de `ping`.
7. Nombre del host configurado correctamente.
8. Sistema actualizado y limpieza ejecutada.
9. Servicios activos verificados con `systemctl`.
10. Instalación y ejecución de al menos cinco herramientas de consola.
11. Docker instalado y funcionando con `docker run hello-world`.
12. Resultado de `docker compose version`.
13. Webmin accesible desde navegador.
14. Portainer accesible desde navegador.
15. Apache con PHP8 mostrando `phpinfo()`.
16. Nginx mostrando la página HTML creada.
17. phpMyAdmin conectado a MariaDB.
18. IT-Tools accesible desde navegador.
19. Resultado de `docker compose ps`.
20. Reflexión técnica breve: qué ventaja aporta Docker Compose frente a instalar todos los servicios directamente en el sistema operativo.

## 25. Preguntas de reflexión técnica

Responde estas preguntas para revisar tu comprensión técnica:

1. ¿Por qué es importante verificar los datos recibidos por DHCP antes de configurar una IP fija?
2. ¿Qué problema puede ocurrir si dos equipos usan la misma IP en la red?
3. ¿Qué función cumple Netplan en Ubuntu Server?
4. ¿Qué diferencia hay entre una máquina virtual y un contenedor?
5. ¿Por qué MariaDB necesita persistencia de datos?
6. ¿Qué ventaja tiene separar Apache, Nginx, MariaDB y phpMyAdmin en contenedores distintos?
7. ¿Qué riesgos pueden existir al exponer Webmin, Portainer o phpMyAdmin sin medidas de seguridad?
8. ¿Qué comandos usarías para diagnosticar un contenedor que no inicia?

## 26. Criterios de evaluación

| Criterio | Nivel esperado |
| --- | --- |
| Instalación del servidor | Ubuntu Server instalado correctamente en VirtualBox con configuración mínima. |
| Configuración de red | Verifica DHCP, configura IP fija con Netplan y demuestra conectividad. |
| Administración del sistema | Actualiza, limpia, revisa archivos de configuración y controla servicios. |
| Uso de terminal | Utiliza comandos con criterio técnico y registra evidencias. |
| Herramientas de consola | Instala y prueba utilidades de monitoreo, navegación y diagnóstico. |
| Docker y Compose | Instala Docker, comprende conceptos básicos y despliega servicios con Compose. |
| Servicios desplegados | Apache/PHP, Nginx, MariaDB, phpMyAdmin, IT-Tools y Portainer funcionan correctamente. |
| Documentación | Presenta evidencias claras, ordenadas y con breve explicación técnica. |
| Reflexión | Justifica decisiones y reconoce ventajas, riesgos y casos de uso. |

## 27. Desafío opcional

Si finalizas antes, puedes realizar uno o más de los siguientes desafíos:

- Crear una segunda base de datos en MariaDB desde phpMyAdmin.
- Crear una página PHP que se conecte a MariaDB.
- Cambiar el contenido de la página de Nginx.
- Crear un segundo archivo Compose con otros servicios.
- Investigar cómo hacer backup de la carpeta `mariadb-data`.
- Activar firewall con `ufw` permitiendo solamente los puertos necesarios.

Ejemplo inicial de firewall:

```bash
sudo ufw allow OpenSSH
sudo ufw allow 8080/tcp
sudo ufw allow 8081/tcp
sudo ufw allow 8082/tcp
sudo ufw allow 8083/tcp
sudo ufw allow 9000/tcp
sudo ufw allow 9443/tcp
sudo ufw allow 10000/tcp
sudo ufw enable
sudo ufw status verbose
```

## 28. Cierre

Esta práctica integra administración de sistemas operativos, redes, virtualización y contenedores. El resultado final es un servidor funcional con servicios desplegados de forma ordenada, verificable y documentada. Más allá de ejecutar comandos, el objetivo principal es comprender cómo se prepara un servidor, cómo se controla su estado y cómo se despliegan aplicaciones modernas utilizando contenedores.

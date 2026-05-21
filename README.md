# Administración de Sistemas Operativos - 3º BT

Materiales para el curso de **Administración de Sistemas Operativos**.

El repositorio reúne apuntes teóricos, guías rápidas, actividades prácticas y archivos de ejemplo para trabajar con Ubuntu Server, Linux, mantenimiento de servidores y contenedores Docker.

---

## Estructura del repositorio

```text
3--Adm-SSOO/
├── Practicas/
│   ├── 1_actividad_instalacion_ubuntu_server_virtualbox.md
│   └── 2_actividad_docker.md
├── Teoricos/
│   ├── Linux.md
│   ├── cloud_init.md
│   ├── comandos_linux.md
│   ├── linux_cheatsheet.md
│   └── mantenimiento_server.md
├── docker/
│   ├── docker.md
│   ├── httpd_mariadb_phpmyadmin-docker-compose.yaml
│   ├── lampp-docker-compose.yml
│   ├── nextcloud_docker-compose.yml
│   ├── portainer-docker-compose.yaml
│   └── wordpress_docker-compose.yml
└── README.md
```

---

## Prácticas

### `Practicas/1_actividad_instalacion_ubuntu_server_virtualbox.md`

Actividad práctica para instalar y configurar **Ubuntu Server en VirtualBox**.

Incluye:

- Descarga de Ubuntu Server.
- Creación de máquina virtual.
- Configuración de red en modo bridged.
- Instalación manual de Ubuntu Server.
- Primer inicio y actualización del sistema.
- Instalación de Guest Additions.
- Configuración de IP fija con Netplan.
- Verificación de conectividad.
- Comparación conceptual con instalación desatendida.
- Resolución de problemas frecuentes.

### `Practicas/2_actividad_docker.md`

Actividad práctica para instalar y usar **Docker en Ubuntu Server**.

Incluye:

- Instalación de Docker Engine y Docker Compose.
- Prueba inicial con `hello-world`.
- Uso de Docker sin `sudo`.
- Ejecución de contenedores con `docker run`.
- Casos prácticos con Ubuntu, Nginx y MariaDB.
- Uso de puertos, volúmenes, logs e inspección de contenedores.
- Creación de servicios con Docker Compose.
- Uso de ejemplos existentes de Apache/PHP/MySQL/phpMyAdmin y Portainer.
- Resolución de problemas frecuentes.

---

## Teóricos

### `Teoricos/Linux.md`

Introducción general a Linux desde la perspectiva de administración de sistemas.

Incluye arquitectura del sistema, kernel, shell, usuarios, permisos, procesos, sistema de archivos y conceptos base para trabajar en servidores GNU/Linux.

### `Teoricos/comandos_linux.md`

Guía de comandos principales de Linux para práctica en terminal.

Incluye comandos de navegación, archivos, búsqueda, permisos, procesos, red, paquetes, compresión y administración básica.

### `Teoricos/linux_cheatsheet.md`

Guía rápida de comandos Linux en formato resumido e imprimible.

Está organizada por categorías:

- Comandos de usuario.
- Navegación y archivos.
- Permisos.
- Procesos.
- Red.
- Compresión.
- Administración por distribución.
- Herramientas universales de administración.

### `Teoricos/cloud_init.md`

Material teórico sobre **cloud-init en Ubuntu Server**.

Incluye:

- Qué es cloud-init.
- Para qué sirve.
- Etapas de ejecución.
- Servicios y archivos relacionados.
- Ejemplos de configuración YAML.
- Configuración de usuarios, SSH, paquetes y red.
- Comandos útiles.
- Problemas frecuentes.
- Cómo deshabilitarlo para liberar recursos.

### `Teoricos/mantenimiento_server.md`

Guía de mejores prácticas para el mantenimiento de **Ubuntu Server**.

Incluye:

- Actualizaciones del sistema.
- Actualizaciones automáticas de seguridad.
- Reinicios necesarios.
- Limpieza de paquetes.
- Monitoreo de disco, logs, servicios y recursos.
- Seguridad SSH.
- Firewall con UFW.
- Usuarios y permisos.
- Respaldos.
- Control de cambios en `/etc`.
- Red, cron y mantenimiento de Docker.
- Checklist diario, semanal y mensual.

---

## Docker

### `docker/docker.md`

Material teórico-práctico sobre Docker.

Incluye:

- Qué es Docker.
- Para qué sirve.
- Diferencia entre contenedores y máquinas virtuales.
- Instalación en Ubuntu Server.
- Comandos básicos.
- Ejemplos con Nginx.
- Qué es Docker Compose.
- Instalación de Docker Compose.
- Ejemplos de `compose.yml`.
- Mantenimiento y limpieza.

### Archivos Docker Compose incluidos

La carpeta `docker/` también contiene archivos YAML listos para analizar, adaptar o ejecutar en prácticas:

| Archivo | Descripción |
|---|---|
| `lampp-docker-compose.yml` | Entorno con Apache/PHP, MySQL y phpMyAdmin. |
| `wordpress_docker-compose.yml` | Ejemplo de WordPress con base de datos MySQL. |
| `nextcloud_docker-compose.yml` | Ejemplo de Nextcloud con MariaDB, Redis, phpMyAdmin y Collabora. |
| `portainer-docker-compose.yaml` | Portainer CE para administrar Docker desde una interfaz web. |
| `httpd_mariadb_phpmyadmin-docker-compose.yaml` | Entorno con Apache HTTPD, MariaDB y phpMyAdmin. |

---

## Orden sugerido de trabajo

1. Leer `Teoricos/Linux.md`.
2. Practicar comandos con `Teoricos/comandos_linux.md`.
3. Usar `Teoricos/linux_cheatsheet.md` como referencia rápida.
4. Realizar `Practicas/1_actividad_instalacion_ubuntu_server_virtualbox.md`.
5. Leer `Teoricos/cloud_init.md`.
6. Leer `Teoricos/mantenimiento_server.md`.
7. Leer `docker/docker.md`.
8. Realizar `Practicas/2_actividad_docker.md`.
9. Probar y modificar los archivos Compose de la carpeta `docker/`.

---

## Requisitos generales

Para las prácticas se recomienda contar con:

- Una computadora con VirtualBox.
- Ubuntu Server instalado en una máquina virtual.
- Conexión a Internet.
- Usuario con permisos `sudo`.
- Conocimientos básicos de terminal.
- Al menos 4 GB de RAM disponible para las prácticas iniciales.
- Al menos 25 GB de espacio libre en disco.

---

## Notas de uso

- Los comandos están pensados principalmente para **Ubuntu Server**.
- Las rutas, IPs, usuarios y contraseñas de ejemplo deben adaptarse al entorno real de laboratorio.
- Antes de ejecutar archivos Docker Compose, revisar puertos, volúmenes y contraseñas definidas.
- Los archivos YAML dependen de la indentación: usar espacios y no tabuladores.
- En servidores reales, usar contraseñas seguras, claves SSH y respaldos periódicos.

# Administración de Sistemas Operativos - 3º BT

Materiales para el curso de **Administración de Sistemas Operativos**.

El repositorio reúne apuntes teóricos, guías rápidas, actividades prácticas, casos de evaluación y archivos de ejemplo para trabajar con Ubuntu Server, Linux, mantenimiento de servidores y contenedores Docker.

🌐 **Sitio web del curso (GitHub Pages):** <https://portalutu.github.io/3--Adm-SSOO/>

Todo el contenido de este repositorio también está disponible navegable desde ese portal, con un diseño pensado para facilitar el acceso a los estudiantes.

---

## Estructura del repositorio

```text
3--Adm-SSOO/
├── index.html                     # Portal del sitio (GitHub Pages)
├── _config.yml                    # Configuración de Jekyll (tema del sitio)
├── Teoricos/
│   ├── Linux.md
│   ├── cloud_init.md
│   ├── linux_cheatsheet.md
│   ├── mantenimiento_server.md
│   ├── plan_servidor_linux_docker.md
│   ├── calculadora_requerimientos_servidor.html
│   ├── Linux_Cheatsheet1.png / Linux_Cheatsheet2.png
│   └── old/                       # Versiones anteriores, fuera del portal
├── Practicas/
│   ├── 1_actividad_instalacion_ubuntu_server_virtualbox.md
│   ├── 2_actividad_docker.md
│   ├── 3_practica_guiada_infraestructura_linux_virtualizada.md
│   ├── 4_ubuntu_server_completo.md
│   └── 5_calculo_requerimientos_vps_tambotrace.md
├── Eval/
│   ├── 3_caso_evaluacion_infraestructura_errores.html
│   └── 3_caso_evaluacion_infraestructura.md
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

## Teóricos

### `Teoricos/Linux.md`

Introducción general a Linux desde la perspectiva de administración de sistemas.

Incluye arquitectura del sistema, kernel, shell, usuarios, permisos, procesos, sistema de archivos y conceptos base para trabajar en servidores GNU/Linux.

### `Teoricos/linux_cheatsheet.md`

Guía rápida de comandos Linux en formato resumido e imprimible, organizada por categorías: usuario, navegación y archivos, permisos, procesos, red, compresión, administración por distribución y herramientas universales.

### `Teoricos/cloud_init.md`

Material teórico sobre **cloud-init en Ubuntu Server**: qué es, para qué sirve, etapas de ejecución, servicios y archivos relacionados, ejemplos de configuración YAML, comandos útiles y problemas frecuentes.

### `Teoricos/mantenimiento_server.md`

Guía de mejores prácticas para el mantenimiento de **Ubuntu Server**: actualizaciones, reinicios, limpieza de paquetes, monitoreo, seguridad SSH, firewall con UFW, respaldos y checklist diario/semanal/mensual.

### `Teoricos/plan_servidor_linux_docker.md`

Laboratorio integral que recorre el ciclo completo de un servidor: instalación de Ubuntu Server, IP estática con Netplan, UFW, Docker Engine y Docker Compose, varios servicios de ejemplo, Cockpit, y una estrategia completa de **backups 3-2-1** con scripts listos para usar.

### `Teoricos/calculadora_requerimientos_servidor.html`

Herramienta interactiva (abrir directamente en el navegador) para estimar CPU, RAM y disco de un servidor Ubuntu Server + Docker a partir de la carga de trabajo esperada.

---

## Prácticas

### `Practicas/1_actividad_instalacion_ubuntu_server_virtualbox.md`

Instalación y configuración de **Ubuntu Server en VirtualBox**: creación de la VM, red en modo bridged, instalación manual, Guest Additions, IP fija con Netplan y resolución de problemas frecuentes.

### `Practicas/2_actividad_docker.md`

Instalación y uso de **Docker en Ubuntu Server**: Docker Engine, Docker Compose, `docker run`, puertos, volúmenes, logs, y ejemplos con Apache/PHP/MySQL/phpMyAdmin y Portainer.

### `Practicas/3_practica_guiada_infraestructura_linux_virtualizada.md`

Despliegue guiado de una pequeña infraestructura virtualizada con tres servidores especializados: SRV-BD, SRV-WEB y SRV-SMB.

### `Practicas/4_ubuntu_server_completo.md`

Instalación de un Ubuntu Server minimalista, administración inicial del sistema y despliegue de servicios con Docker de punta a punta.

### `Practicas/5_calculo_requerimientos_vps_tambotrace.md`

Toma el proyecto Scrum de trazabilidad lechera de la materia Ingeniería de Software y calcula, paso a paso, el dimensionamiento de un VPS (Ubuntu Server + Docker + Docker Compose + UFW + backups 3-2-1) a partir de sus requerimientos funcionales y no funcionales.

---

## Evaluaciones

### `Eval/3_caso_evaluacion_infraestructura_errores.html`

Caso de evaluación interactivo: un diseño de infraestructura Linux virtualizada con 19 errores de planificación e implementación para que el estudiante detecte y corrija.

### `Eval/3_caso_evaluacion_infraestructura.md`

Clave de respuestas del caso anterior, con la explicación de cada error y su corrección.

---

## Docker

### `docker/docker.md`

Material teórico-práctico sobre Docker: qué es, diferencia con máquinas virtuales, instalación en Ubuntu Server, comandos básicos, Docker Compose y mantenimiento/limpieza.

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
2. Usar `Teoricos/linux_cheatsheet.md` como referencia rápida durante la práctica en terminal.
3. Realizar `Practicas/1_actividad_instalacion_ubuntu_server_virtualbox.md`.
4. Leer `Teoricos/cloud_init.md` y `Teoricos/mantenimiento_server.md`.
5. Leer `docker/docker.md` y realizar `Practicas/2_actividad_docker.md`.
6. Probar y modificar los archivos Compose de la carpeta `docker/`.
7. Realizar `Practicas/3_practica_guiada_infraestructura_linux_virtualizada.md`.
8. Resolver `Eval/3_caso_evaluacion_infraestructura_errores.html` y verificar con la clave de respuestas.
9. Leer `Teoricos/plan_servidor_linux_docker.md` y realizar `Practicas/4_ubuntu_server_completo.md`.
10. Cerrar con `Practicas/5_calculo_requerimientos_vps_tambotrace.md`, aplicando todo lo anterior a un caso de dimensionamiento real.

---

## Requisitos generales

Para las prácticas se recomienda contar con:

- Una computadora con VirtualBox.
- Ubuntu Server instalado en una máquina virtual (o un VPS para la práctica 5).
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

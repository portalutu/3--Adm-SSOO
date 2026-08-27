# Linux Cheatsheet Unificado para Estudiantes

> Guía rápida de referencia en terminal Linux, unificando los comandos contenidos en `comandos_linux.md` y `linux_cheatsheet.md`.  
> Incluye un índice con enlaces internos para facilitar la navegación.

---

## Índice

- [Navegación y ubicación](#navegación-y-ubicación)
- [Archivos y directorios](#archivos-y-directorios)
- [Visualización y edición de archivos](#visualización-y-edición-de-archivos)
- [Búsqueda de archivos e información](#búsqueda-de-archivos-e-información)
- [Permisos y propiedad](#permisos-y-propiedad)
- [Usuarios y sesión](#usuarios-y-sesión)
- [Procesos y monitoreo del sistema](#procesos-y-monitoreo-del-sistema)
- [Red y conectividad](#red-y-conectividad)
- [Paquetes y software](#paquetes-y-software)
- [Compresión y archivado](#compresión-y-archivado)
- [Disco, particiones y almacenamiento](#disco-particiones-y-almacenamiento)
- [Redirecciones, tuberías y salida](#redirecciones-tuberías-y-salida)
- [Administración del sistema y servicios](#administración-del-sistema-y-servicios)
- [Seguridad y firewall](#seguridad-y-firewall)
- [Tareas programadas, sincronización y backups](#tareas-programadas-sincronización-y-backups)
- [Ayuda y documentación](#ayuda-y-documentación)
- [Comandos para memorizar primero](#comandos-para-memorizar-primero)

---

## Navegación y ubicación

| Comando | Explicación | Ejemplo |
|---|---|---|
| `pwd` | Muestra la ruta actual. | `pwd` |
| `ls` | Lista archivos y carpetas. | `ls` |
| `ls -l` | Lista detallada. | `ls -l` |
| `ls -a` | Incluye archivos ocultos. | `ls -a` |
| `cd carpeta` | Entra en una carpeta. | `cd Documentos` |
| `cd /ruta` | Cambia a una ruta específica. | `cd /etc/nginx` |
| `cd ..` | Sube un nivel. | `cd ..` |
| `cd ~` | Vuelve al directorio personal. | `cd ~` |
| `tree` | Muestra estructura en árbol. | `tree` |

---

## Archivos y directorios

| Comando | Explicación | Ejemplo |
|---|---|---|
| `mkdir nombre` | Crea una carpeta. | `mkdir practicas` |
| `mkdir -p ruta` | Crea carpetas intermedias si faltan. | `mkdir -p curso/linux/comandos` |
| `touch archivo` | Crea un archivo vacío o actualiza fecha. | `touch notas.txt` |
| `cp origen destino` | Copia un archivo. | `cp notas.txt respaldo.txt` |
| `cp -r origen destino` | Copia una carpeta completa. | `cp -r practicas practicas_backup` |
| `mv origen destino` | Mueve o renombra. | `mv notas.txt apuntes.txt` |
| `rm archivo` | Elimina un archivo. | `rm apuntes.txt` |
| `rm -r carpeta` | Elimina una carpeta recursivamente. | `rm -r practicas_backup` |
| `rmdir carpeta` | Elimina una carpeta vacía. | `rmdir carpeta_vacia` |
| `ln -s destino nombre_enlace` | Crea un enlace simbólico. | `ln -s /var/www/html web` |

---

## Visualización y edición de archivos

| Comando | Explicación | Ejemplo |
|---|---|---|
| `cat archivo` | Muestra el contenido completo. | `cat notas.txt` |
| `tac archivo` | Muestra el contenido en orden inverso. | `tac notas.txt` |
| `less archivo` | Permite leer por páginas. | `less /etc/services` |
| `head archivo` | Muestra primeras 10 líneas. | `head notas.txt` |
| `head -n 5 archivo` | Muestra una cantidad específica de líneas iniciales. | `head -n 5 notas.txt` |
| `head -n 10 archivo` | Variante frecuente para 10 líneas. | `head -n 10 archivo` |
| `tail archivo` | Muestra últimas 10 líneas. | `tail notas.txt` |
| `tail -n 20 archivo` | Muestra cantidad específica de líneas finales. | `tail -n 20 notas.txt` |
| `tail -f log.txt` | Sigue un log en tiempo real. | `tail -f /var/log/syslog` |
| `nl archivo` | Muestra el archivo numerando líneas. | `nl notas.txt` |
| `nano archivo` | Edita con Nano. | `nano notas.txt` |
| `vim archivo` | Edita con Vim. | `vim notas.txt` |

---

## Búsqueda de archivos e información

| Comando | Explicación | Ejemplo |
|---|---|---|
| `find ruta -name "nombre"` | Busca archivos o carpetas por nombre. | `find . -name "notas.txt"` |
| `find /ruta -name archivo.txt` | Variante equivalente. | `find /home -name archivo.txt` |
| `grep texto archivo` | Busca texto dentro de un archivo. | `grep Linux notas.txt` |
| `grep -i texto archivo` | Busca ignorando mayúsculas/minúsculas. | `grep -i linux notas.txt` |
| `grep -r texto carpeta` | Busca texto en varios archivos. | `grep -r contraseña Documentos` |
| `grep "patron" archivo` | Busca un patrón textual. | `grep "error" app.log` |
| `which comando` | Indica dónde está instalado un comando. | `which bash` |
| `history` | Muestra historial de comandos. | `history` |

---

## Permisos y propiedad

| Comando | Explicación | Ejemplo |
|---|---|---|
| `chmod permisos archivo` | Cambia permisos. | `chmod 644 notas.txt` |
| `chmod 755 archivo` | Asigna permisos `rwxr-xr-x`. | `chmod 755 script.sh` |
| `chmod +x archivo` | Agrega permiso de ejecución. | `chmod +x script.sh` |
| `chmod u+x script.sh` | Agrega permiso de ejecución al usuario dueño. | `chmod u+x script.sh` |
| `chmod -R 755 /dir` | Cambia permisos recursivamente. | `chmod -R 755 /srv/app` |
| `chown usuario archivo` | Cambia el propietario. | `sudo chown alumno notas.txt` |
| `chown usuario:grupo archivo` | Cambia propietario y grupo. | `sudo chown admin:devs app.conf` |
| `chown -R usuario:grupo /dir` | Cambia propietario de forma recursiva. | `sudo chown -R www-data:www-data /var/www/html` |
| `chgrp grupo archivo` | Cambia el grupo. | `sudo chgrp estudiantes notas.txt` |
| `ls -l` | Permite visualizar permisos, dueño y grupo. | `ls -l` |

---

## Usuarios y sesión

| Comando | Explicación | Ejemplo |
|---|---|---|
| `whoami` | Muestra el usuario actual. | `whoami` |
| `id` | Muestra UID, GID y grupos. | `id` |
| `who` | Muestra usuarios conectados. | `who` |
| `passwd` | Cambia la contraseña del usuario actual. | `passwd` |
| `passwd nuevousuario` | Define la contraseña de otro usuario. | `sudo passwd alumno` |
| `sudo comando` | Ejecuta como administrador. | `sudo apt update` |
| `su - usuario` | Cambia a otro usuario cargando su entorno. | `su - root` |
| `useradd nuevousuario` | Crea un usuario nuevo. | `sudo useradd alumno1` |
| `groupadd devs` | Crea un grupo. | `sudo groupadd devs` |
| `usermod -aG grupo usuario` | Agrega un usuario a un grupo. | `sudo usermod -aG docker alumno1` |
| `deluser nombreusuario` | Elimina usuario en Debian/Ubuntu. | `sudo deluser alumno1` |
| `userdel nombreusuario` | Elimina usuario genérico. | `sudo userdel alumno1` |

---

## Procesos y monitoreo del sistema

| Comando | Explicación | Ejemplo |
|---|---|---|
| `ps` | Muestra procesos de la sesión actual. | `ps` |
| `ps aux` | Muestra todos los procesos. | `ps aux` |
| `top` | Muestra procesos en tiempo real. | `top` |
| `htop` | Monitor interactivo más amigable. | `htop` |
| `kill PID` | Finaliza un proceso. | `kill 2451` |
| `kill -9 PID` | Fuerza el cierre de un proceso. | `kill -9 2451` |
| `uname -a` | Muestra información del sistema y kernel. | `uname -a` |
| `date` | Muestra fecha y hora. | `date` |
| `uptime` | Muestra tiempo encendido y carga. | `uptime` |
| `df -h` | Muestra uso de disco. | `df -h` |
| `du -sh *` | Muestra tamaños de archivos/directorios. | `du -sh *` |
| `free -h` | Muestra uso de memoria. | `free -h` |
| `iotop` | Monitorea entrada/salida de disco. | `sudo iotop` |
| `dmesg \| tail` | Muestra los mensajes recientes del kernel. | `dmesg | tail` |

---

## Red y conectividad

| Comando | Explicación | Ejemplo |
|---|---|---|
| `ip a` | Muestra interfaces y direcciones IP. | `ip a` |
| `ping host` | Prueba conectividad. | `ping google.com` |
| `ssh usuario@equipo` | Se conecta remotamente. | `ssh alumno@192.168.1.20` |
| `scp origen destino` | Copia archivos por SSH. | `scp notas.txt alumno@192.168.1.20:/home/alumno/` |
| `curl url` | Consulta o descarga contenido desde una URL. | `curl https://example.com` |
| `wget url` | Descarga un archivo desde internet. | `wget https://example.com/archivo.zip` |
| `ifconfig` | Muestra configuración IP. **Obsoleto**. | `ifconfig` |
| `netstat -tuln` | Muestra puertos abiertos. **Obsoleto**. | `netstat -tuln` |
| `ss -tuln` | Muestra puertos abiertos. Alternativa moderna. | `ss -tuln` |

---

## Paquetes y software

### Debian / Ubuntu / Linux Mint

| Comando | Explicación | Ejemplo |
|---|---|---|
| `apt update` | Actualiza la lista de paquetes. | `sudo apt update` |
| `apt upgrade` | Instala actualizaciones disponibles. | `sudo apt upgrade` |
| `apt install paquete` | Instala un paquete. | `sudo apt install htop` |
| `apt remove paquete` | Desinstala un paquete. | `sudo apt remove htop` |
| `apt search palabra` | Busca paquetes. | `apt search editor` |
| `dpkg -l` | Lista paquetes instalados. | `dpkg -l` |
| `dpkg -i paquete.deb` | Instala un paquete `.deb` local. | `sudo dpkg -i app.deb` |

### RedHat / CentOS / Fedora / Rocky

| Comando | Explicación | Ejemplo |
|---|---|---|
| `dnf update` | Actualiza paquetes. | `sudo dnf update` |
| `yum update` | Actualiza paquetes en CentOS antiguos. | `sudo yum update` |
| `dnf install paquete` | Instala un paquete. | `sudo dnf install vim` |
| `dnf remove paquete` | Elimina un paquete. | `sudo dnf remove vim` |
| `rpm -qa` | Lista paquetes instalados. | `rpm -qa` |

### openSUSE / SUSE Linux Enterprise

| Comando | Explicación | Ejemplo |
|---|---|---|
| `zypper refresh` | Actualiza repositorios. | `sudo zypper refresh` |
| `zypper update` | Actualiza paquetes. | `sudo zypper update` |
| `zypper install paquete` | Instala un paquete. | `sudo zypper install htop` |
| `zypper remove paquete` | Elimina un paquete. | `sudo zypper remove htop` |
| `yast2` | Abre la herramienta de configuración. | `sudo yast2` |

### Arch / Manjaro

| Comando | Explicación | Ejemplo |
|---|---|---|
| `pacman -Syu` | Actualiza el sistema. | `sudo pacman -Syu` |
| `pacman -S paquete` | Instala un paquete. | `sudo pacman -S htop` |
| `pacman -R paquete` | Elimina un paquete. | `sudo pacman -R htop` |
| `pacman -Q` | Lista paquetes instalados. | `pacman -Q` |
| `yay -S paquete` | Instala paquetes desde AUR. | `yay -S visual-studio-code-bin` |

---

## Compresión y archivado

| Comando | Explicación | Ejemplo |
|---|---|---|
| `tar -cvf archivo.tar carpeta` | Empaqueta en `.tar`. | `tar -cvf practicas.tar practicas` |
| `tar -xvf archivo.tar` | Extrae `.tar`. | `tar -xvf practicas.tar` |
| `tar -czvf archivo.tar.gz carpeta` | Empaqueta y comprime en `.tar.gz`. | `tar -czvf practicas.tar.gz practicas` |
| `tar -xzvf archivo.tar.gz` | Extrae `.tar.gz`. | `tar -xzvf practicas.tar.gz` |
| `tar -czf archivo.tar.gz carpeta/` | Variante compacta para comprimir. | `tar -czf backup.tar.gz /home/alumno` |
| `tar -xzf archivo.tar.gz` | Variante compacta para extraer. | `tar -xzf backup.tar.gz` |
| `zip archivo.zip archivo` | Comprime en `.zip`. | `zip documentos.zip notas.txt` |
| `zip -r archivo.zip carpeta/` | Comprime una carpeta en `.zip`. | `zip -r sitio.zip sitio/` |
| `unzip archivo.zip` | Extrae `.zip`. | `unzip documentos.zip` |
| `gzip archivo` | Comprime un archivo. | `gzip app.log` |
| `gunzip archivo.gz` | Descomprime un archivo `.gz`. | `gunzip app.log.gz` |

---

## Disco, particiones y almacenamiento

| Comando | Explicación | Ejemplo |
|---|---|---|
| `lsblk` | Lista discos y particiones. | `lsblk` |
| `fdisk -l` | Muestra tabla de particiones. | `sudo fdisk -l` |
| `mount /dev/sda1 /mnt` | Monta un sistema de archivos. | `sudo mount /dev/sdb1 /mnt` |
| `umount /mnt` | Desmonta un sistema de archivos. | `sudo umount /mnt` |
| `df -h` | Muestra uso de disco. | `df -h` |
| `du -sh *` | Muestra el tamaño de carpetas/archivos. | `du -sh *` |

---

## Redirecciones, tuberías y salida

| Comando | Explicación | Ejemplo |
|---|---|---|
| `>` | Redirige salida y reemplaza el archivo. | `ls > lista.txt` |
| `>>` | Redirige salida agregando al final. | `date >> registro.txt` |
| `\|` | Envía la salida de un comando a otro. | `ls -l | less` |
| `echo texto` | Muestra un texto en pantalla. | `echo Hola` |
| `echo texto > archivo` | Escribe texto en un archivo. | `echo "Práctica 1" > notas.txt` |

---

## Administración del sistema y servicios

| Comando | Explicación | Ejemplo |
|---|---|---|
| `systemctl start servicio` | Inicia un servicio. | `sudo systemctl start nginx` |
| `systemctl enable servicio` | Habilita un servicio al arranque. | `sudo systemctl enable nginx` |
| `systemctl restart servicio` | Reinicia un servicio. | `sudo systemctl restart ssh` |
| `systemctl status servicio` | Muestra el estado del servicio. | `systemctl status docker` |
| `journalctl -xe` | Muestra logs recientes del sistema. | `journalctl -xe` |

---

## Seguridad y firewall

| Comando | Explicación | Ejemplo |
|---|---|---|
| `ufw enable` | Activa UFW. | `sudo ufw enable` |
| `ufw status` | Verifica el estado de UFW. | `sudo ufw status` |
| `ufw allow 22/tcp` | Permite SSH. | `sudo ufw allow 22/tcp` |
| `iptables -L` | Lista reglas de firewall de bajo nivel. | `sudo iptables -L` |
| `firewall-cmd --state` | Verifica estado del firewall en sistemas RedHat. | `sudo firewall-cmd --state` |
| `firewall-cmd --add-service=ssh --permanent` | Permite SSH de forma permanente. | `sudo firewall-cmd --add-service=ssh --permanent` |

---

## Tareas programadas, sincronización y backups

| Comando | Explicación | Ejemplo |
|---|---|---|
| `crontab -e` | Edita tareas programadas del usuario. | `crontab -e` |
| `rsync -av origen/ destino/` | Sincroniza archivos y carpetas. | `rsync -av /home/alumno/ /backup/alumno/` |
| `tar -czf backup.tar.gz /home/usuario` | Crea una copia comprimida. | `tar -czf backup.tar.gz /home/admin` |

---

## Ayuda y documentación

| Comando | Explicación | Ejemplo |
|---|---|---|
| `man comando` | Abre el manual del comando. | `man ls` |
| `comando --help` | Muestra ayuda rápida. | `ls --help` |
| `info comando` | Muestra documentación adicional. | `info grep` |

---

## Comandos para memorizar primero

Si recién empezás, estos son los más importantes para recordar primero:

`pwd`, `ls`, `cd`, `mkdir`, `touch`, `cp`, `mv`, `rm`, `cat`, `grep`, `chmod`, `ps`, `top`, `ping`, `ssh`, `man`

---

## Notas útiles

- Linux distingue mayúsculas de minúsculas.
- Leé siempre los mensajes de error: suelen indicar la causa del problema.
- Tené cuidado con `rm` y especialmente con `rm -r`.
- Siempre confirmá tu ubicación con `pwd` y `ls`.
- Preferí `ss` en lugar de `netstat`, y `ip` en lugar de `ifconfig`, salvo cuando necesites compatibilidad con materiales antiguos.

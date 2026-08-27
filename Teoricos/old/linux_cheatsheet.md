**Guía rápida de comandos Linux - Edición multidistribución (A4 imprimible)**

---

## 👤 COMANDOS DE USUARIO

### 📁 Navegación y sistema de archivos
- `pwd` — Muestra el directorio de trabajo actual
- `ls -l` — Lista archivos en formato largo
- `ls -a` — Muestra archivos ocultos
- `cd /ruta` — Cambia de directorio
- `cd ..` — Sube un directorio
- `tree` — Muestra la estructura de directorios
- `find /ruta -name archivo.txt` — Busca archivos

### 📄 Operaciones con archivos
- `cp archivo1 archivo2` — Copia un archivo
- `mv archivo1 /dir/` — Mueve o renombra un archivo
- `rm archivo.txt` — Elimina un archivo
- `rm -r carpeta/` — Elimina una carpeta recursivamente
- `touch nuevoarchivo.txt` — Crea un archivo vacío
- `mkdir carpeta` — Crea un directorio
- `rmdir carpeta` — Elimina un directorio vacío
- `ln -s destino nombre_enlace` — Crea un enlace simbólico

### 🔍 Visualización y edición de archivos
- `cat archivo` — Muestra el contenido de un archivo
- `tac archivo` — Muestra el archivo en orden inverso
- `less archivo` — Permite ver un archivo página por página
- `head -n 10 archivo` — Muestra las primeras 10 líneas
- `tail -f log.txt` — Muestra actualizaciones de un log en tiempo real
- `nano archivo` — Edita un archivo con Nano
- `vim archivo` — Edita un archivo con Vim
- `grep "patron" archivo` — Busca texto dentro de un archivo

### 🔐 Permisos y propiedad
- `chmod 755 archivo` — Cambia permisos (rwxr-xr-x)
- `chmod u+x script.sh` — Agrega permiso de ejecución al usuario
- `chown usuario:grupo archivo` — Cambia el propietario de un archivo
- `sudo comando` — Ejecuta un comando como superusuario

### ⚙️ Información del sistema y procesos
- `uname -a` — Muestra información del kernel y del sistema
- `top` — Muestra procesos del sistema en tiempo real
- `htop` — Monitor interactivo de procesos
- `ps aux` — Muestra todos los procesos
- `kill PID` — Termina un proceso
- `df -h` — Muestra uso de disco por partición
- `du -sh *` — Muestra el tamaño de carpetas/archivos
- `free -h` — Muestra el uso de memoria
- `uptime` — Muestra tiempo encendido y carga del sistema
- `whoami` — Muestra el usuario actual

### 🌐 Utilidades de red
- `ip a` — Muestra interfaces de red
- `ping host` — Prueba conectividad
- `curl http://example.com` — Obtiene datos desde la web
- `wget http://example.com/file` — Descarga un archivo
- `ssh usuario@host` — Conecta a un sistema remoto
- `scp archivo usuario@host:/ruta` — Copia un archivo mediante SSH
- `ifconfig` — Muestra configuración IP (obsoleto)
- `netstat -tuln` — Muestra puertos abiertos (obsoleto)
- `ss -tuln` — Muestra puertos abiertos (moderno)

### 🗜️ Compresión y archivado
- `tar -czf archivo.tar.gz carpeta/` — Crea un archivo tar comprimido
- `tar -xzf archivo.tar.gz` — Extrae un archivo tar
- `zip -r archivo.zip carpeta/` — Crea un archivo zip
- `unzip archivo.zip` — Extrae un archivo zip
- `gzip archivo` — Comprime un archivo
- `gunzip archivo.gz` — Descomprime un archivo

### 🧮 Disco y monitoreo del sistema
- `lsblk` — Lista dispositivos de bloque
- `fdisk -l` — Muestra particiones de disco
- `mount /dev/sda1 /mnt` — Monta un disco
- `umount /mnt` — Desmonta un disco
- `iotop` — Monitorea entrada/salida de disco
- `dmesg | tail` — Muestra mensajes del kernel

---

## 🛠️ COMANDOS DE ADMINISTRADOR

### 🧱 Debian / Ubuntu / Linux Mint
- `apt update` — Actualiza la lista de paquetes
- `apt upgrade` — Actualiza paquetes instalados
- `apt install paquete` — Instala un paquete
- `apt remove paquete` — Elimina un paquete
- `dpkg -l` — Lista paquetes instalados
- `dpkg -i paquete.deb` — Instala un paquete .deb local
- `ufw enable` — Activa el firewall
- `ufw status` — Verifica el estado del firewall
- `systemctl start servicio` — Inicia un servicio
- `systemctl enable servicio` — Activa un servicio al inicio

### 🦋 RedHat / CentOS / Fedora / Rocky
- `dnf update` — Actualiza todos los paquetes (Fedora/Rocky)
- `yum update` — Actualiza todos los paquetes (CentOS)
- `dnf install paquete` — Instala un paquete
- `dnf remove paquete` — Elimina un paquete
- `rpm -qa` — Lista paquetes instalados
- `firewall-cmd --state` — Verifica el estado del firewall
- `firewall-cmd --add-service=ssh --permanent` — Permite SSH
- `systemctl restart servicio` — Reinicia un servicio

### 🧊 openSUSE / SUSE Linux Enterprise
- `zypper refresh` — Actualiza los repositorios
- `zypper update` — Actualiza paquetes
- `zypper install paquete` — Instala un paquete
- `zypper remove paquete` — Elimina un paquete
- `yast2` — Abre la herramienta de configuración de SUSE

### 🌀 Arch / Manjaro
- `pacman -Syu` — Actualiza el sistema
- `pacman -S paquete` — Instala un paquete
- `pacman -R paquete` — Elimina un paquete
- `pacman -Q` — Lista paquetes instalados
- `yay -S paquete` — Instala un paquete de AUR

### 🧰 Herramientas universales de administración
- `useradd nuevousuario` — Agrega un usuario nuevo
- `passwd nuevousuario` — Define la contraseña de un usuario
- `groupadd devs` — Crea un grupo nuevo
- `usermod -aG grupo usuario` — Agrega un usuario a un grupo
- `deluser nombreusuario` — Elimina un usuario (Debian)
- `userdel nombreusuario` — Elimina un usuario (genérico)
- `journalctl -xe` — Muestra logs del sistema
- `systemctl status servicio` — Muestra el estado de un servicio
- `crontab -e` — Edita tareas cron del usuario
- `rsync -av origen/ destino/` — Sincroniza archivos/directorios
- `tar -czf backup.tar.gz /home/usuario` — Crea una copia de seguridad
- `iptables -L` — Lista reglas del firewall
- `ufw allow 22/tcp` — Abre el puerto SSH
- `chmod -R 755 /dir` — Define permisos de directorio
- `chown -R usuario:grupo /dir` — Define propietario y grupo

---

🖨️ **Instrucciones de impresión:**
- Formato: A4 vertical, fondo blanco
- Fuente: sans-serif (Roboto/Inter)
- Secciones separadas por divisores claros
- Comandos en cajas grises monoespaciadas
- Diseño en dos columnas para facilitar la lectura

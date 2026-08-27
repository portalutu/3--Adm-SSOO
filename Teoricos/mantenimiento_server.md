---
layout: default
title: "Mantenimiento de Ubuntu Server"
---

[← Volver al portal](../)

# Mantenimiento de Ubuntu Server

---

## 1. ¿Qué significa mantener un servidor?

Mantener un servidor Ubuntu significa realizar tareas periódicas para que el sistema siga siendo seguro, estable, actualizado y recuperable.

Un servidor no se administra solo después de instalarlo. Con el tiempo aparecen actualizaciones, logs, cambios de configuración, paquetes sin uso, intentos de acceso, consumo de disco, servicios detenidos o necesidades de respaldo.

El mantenimiento busca:

- Reducir riesgos de seguridad.
- Evitar interrupciones del servicio.
- Detectar problemas antes de que sean graves.
- Mantener el sistema actualizado.
- Conservar respaldos recuperables.
- Documentar cambios importantes.
- Usar recursos de forma eficiente.

---

## 2. Actualización del sistema

Ubuntu usa APT para instalar, actualizar y eliminar paquetes.

Actualizar la lista de paquetes:

```bash
sudo apt update
```

Instalar actualizaciones disponibles:

```bash
sudo apt upgrade
```

Actualizar aceptando automáticamente:

```bash
sudo apt upgrade -y
```

Ver paquetes que se pueden actualizar:

```bash
apt list --upgradable
```

Ver historial de cambios de paquetes:

```bash
less /var/log/apt/history.log
```

Ver detalles de instalaciones y eliminaciones:

```bash
less /var/log/dpkg.log
```

Buena práctica:

- Ejecutar actualizaciones regularmente.
- Leer los cambios importantes antes de actualizar servicios críticos.
- Reiniciar cuando el sistema lo requiera.
- Evitar mezclar repositorios de distintas versiones de Ubuntu.

---

## 3. Actualizaciones automáticas de seguridad

Ubuntu Server puede aplicar actualizaciones de seguridad automáticamente mediante `unattended-upgrades`.

Instalar:

```bash
sudo apt install -y unattended-upgrades
```

Ver archivo principal de configuración:

```bash
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Ver frecuencia de ejecución:

```bash
cat /etc/apt/apt.conf.d/20auto-upgrades
```

Configuración típica:

```text
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

Esto indica:

- Actualizar lista de paquetes diariamente.
- Ejecutar actualizaciones automáticas diariamente.

Ver logs:

```bash
sudo ls /var/log/unattended-upgrades/
sudo less /var/log/unattended-upgrades/unattended-upgrades.log
```

Ver timers relacionados:

```bash
systemctl list-timers | grep apt
```

Buena práctica:

- Mantener activas las actualizaciones automáticas de seguridad en servidores simples.
- Revisar logs periódicamente.
- Definir una política clara para reinicios.
- En servidores críticos, probar actualizaciones en un entorno de prueba antes de producción.

---

## 4. Reinicios necesarios

Algunas actualizaciones requieren reiniciar el sistema, especialmente actualizaciones de kernel o bibliotecas críticas.

Verificar si se requiere reinicio:

```bash
test -f /var/run/reboot-required && cat /var/run/reboot-required
```

Ver paquetes que solicitan reinicio:

```bash
cat /var/run/reboot-required.pkgs
```

Reiniciar:

```bash
sudo reboot
```

Buena práctica:

- Programar reinicios fuera del horario de uso.
- Avisar antes si el servidor presta servicios a otras personas.
- Verificar que los servicios vuelvan a iniciar correctamente.

Después de reiniciar:

```bash
systemctl --failed
systemctl status ssh
```

---

## 5. Limpieza de paquetes

Eliminar paquetes que ya no son necesarios:

```bash
sudo apt autoremove
```

Limpiar caché local de paquetes:

```bash
sudo apt autoclean
```

Limpiar toda la caché de paquetes descargados:

```bash
sudo apt clean
```

Ver espacio usado por la caché:

```bash
du -sh /var/cache/apt
```

Buena práctica:

- Ejecutar `autoremove` después de actualizaciones grandes.
- Revisar antes de aceptar eliminaciones en servidores importantes.
- No borrar archivos manualmente en directorios del sistema sin entender su función.

---

## 6. Monitoreo de espacio en disco

Ver uso de disco por partición:

```bash
df -h
```

Ver dispositivos y puntos de montaje:

```bash
lsblk
```

Ver uso de una carpeta:

```bash
du -sh /var/log
```

Buscar carpetas grandes en `/var`:

```bash
sudo du -h --max-depth=1 /var | sort -h
```

Buscar archivos grandes:

```bash
sudo find /var -type f -size +500M -exec ls -lh {} \;
```

Buena práctica:

- Vigilar especialmente `/var`, `/home`, `/srv` y directorios de bases de datos.
- No dejar que el disco llegue al 100%.
- Investigar por qué crece un directorio antes de borrar archivos.

---

## 7. Logs del sistema

Ubuntu usa `systemd-journald` y archivos tradicionales en `/var/log`.

Ver logs recientes del sistema:

```bash
journalctl -xe
```

Ver logs del arranque actual:

```bash
journalctl -b
```

Ver logs de un servicio:

```bash
sudo journalctl -u ssh
```

Ver logs en tiempo real:

```bash
sudo journalctl -f
```

Ver tamaño del journal:

```bash
journalctl --disk-usage
```

Reducir logs del journal dejando solo los últimos 7 días:

```bash
sudo journalctl --vacuum-time=7d
```

Reducir logs dejando un tamaño máximo:

```bash
sudo journalctl --vacuum-size=500M
```

Archivos útiles:

| Archivo | Uso |
|---|---|
| `/var/log/syslog` | Mensajes generales del sistema. |
| `/var/log/auth.log` | Autenticación, sudo y SSH. |
| `/var/log/kern.log` | Mensajes del kernel. |
| `/var/log/apt/history.log` | Historial de APT. |
| `/var/log/dpkg.log` | Cambios de paquetes. |

Buena práctica:

- Revisar logs cuando un servicio falla.
- No borrar logs activos sin necesidad.
- Usar rotación de logs en servicios que generan mucho contenido.

---

## 8. Estado de servicios

Listar servicios fallidos:

```bash
systemctl --failed
```

Ver estado de un servicio:

```bash
systemctl status nombre_servicio
```

Ejemplo:

```bash
systemctl status ssh
```

Reiniciar un servicio:

```bash
sudo systemctl restart nombre_servicio
```

Activar un servicio al arranque:

```bash
sudo systemctl enable nombre_servicio
```

Desactivar un servicio al arranque:

```bash
sudo systemctl disable nombre_servicio
```

Ver servicios activos:

```bash
systemctl list-units --type=service --state=running
```

Buena práctica:

- Revisar `systemctl --failed` después de reinicios.
- Mantener activos solo los servicios necesarios.
- Consultar logs antes de reiniciar repetidamente un servicio que falla.

---

## 9. Seguridad SSH

OpenSSH permite administrar el servidor de forma remota.

Instalar servidor SSH:

```bash
sudo apt install -y openssh-server
```

Ver estado:

```bash
systemctl status ssh
```

Ver logs en tiempo real:

```bash
sudo journalctl -fu ssh.service
```

La configuración principal está en:

```text
/etc/ssh/sshd_config
```

También pueden usarse archivos separados en:

```text
/etc/ssh/sshd_config.d/
```

Antes de modificar SSH, hacer copia:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.original
sudo chmod a-w /etc/ssh/sshd_config.original
```

Validar configuración antes de reiniciar:

```bash
sudo sshd -t
```

Reiniciar SSH:

```bash
sudo systemctl restart ssh
```

Generar clave SSH desde el equipo cliente:

```bash
ssh-keygen -t ed25519
```

Copiar clave al servidor:

```bash
ssh-copy-id usuario@IP_DEL_SERVIDOR
```

Buena práctica:

- Preferir claves SSH sobre contraseñas.
- No editar SSH sin mantener una sesión abierta de respaldo.
- Probar la configuración antes de reiniciar el servicio.
- Revisar intentos de acceso en `/var/log/auth.log`.

---

## 10. Firewall con UFW

UFW es una herramienta sencilla para administrar reglas de firewall.

Ver estado:

```bash
sudo ufw status verbose
```

Permitir SSH antes de activar el firewall:

```bash
sudo ufw allow OpenSSH
```

Activar firewall:

```bash
sudo ufw enable
```

Permitir un puerto web:

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

Permitir un puerto específico:

```bash
sudo ufw allow 8080/tcp
```

Eliminar una regla:

```bash
sudo ufw delete allow 8080/tcp
```

Buena práctica:

- Permitir SSH antes de activar UFW en servidores remotos.
- Abrir solo los puertos necesarios.
- Revisar reglas después de instalar nuevos servicios.
- Recordar que Docker puede publicar puertos y alterar reglas de red.

---

## 11. Usuarios y permisos

Ver usuario actual:

```bash
whoami
```

Ver grupos:

```bash
groups
```

Crear usuario:

```bash
sudo adduser soporte
```

Agregar usuario al grupo sudo:

```bash
sudo usermod -aG sudo soporte
```

Bloquear usuario:

```bash
sudo passwd -l soporte
```

Desbloquear usuario:

```bash
sudo passwd -u soporte
```

Ver últimos accesos:

```bash
last
```

Ver intentos de autenticación:

```bash
sudo less /var/log/auth.log
```

Buena práctica:

- Usar cuentas personales en lugar de compartir usuarios.
- Dar permisos `sudo` solo cuando sea necesario.
- Revisar usuarios antiguos o que ya no se usan.
- Usar contraseñas seguras o autenticación por clave.

---

## 12. Respaldos

Un servidor sin respaldos no está realmente administrado.

Un plan de respaldo debe definir:

- Qué datos se respaldan.
- Cada cuánto se respaldan.
- Dónde se guardan.
- Cómo se restauran.
- Quién verifica que funcionen.

Carpetas comunes para respaldar:

| Ruta | Motivo |
|---|---|
| `/etc` | Configuración del sistema. |
| `/home` | Datos de usuarios. |
| `/srv` | Datos de servicios. |
| `/var/www` | Sitios web. |
| `/var/lib` | Datos de aplicaciones y bases de datos. |

Ejemplo con `rsync`:

```bash
sudo rsync -av --delete /etc/ /backup/etc/
```

Respaldar sitio web:

```bash
sudo rsync -av /var/www/ /backup/var-www/
```

Crear archivo comprimido de configuración:

```bash
sudo tar -czf /backup/etc-$(date +%F).tar.gz /etc
```

Verificar contenido:

```bash
tar -tzf /backup/etc-$(date +%F).tar.gz | head
```

Buena práctica:

- Probar restauraciones, no solo crear respaldos.
- Guardar copias fuera del servidor.
- Automatizar respaldos cuando sea posible.
- Proteger respaldos que contengan contraseñas o datos personales.

---

## 13. Control de cambios en /etc

Los archivos de `/etc` contienen configuración del sistema. Una mala edición puede romper servicios importantes.

Instalar `etckeeper`:

```bash
sudo apt install -y etckeeper
```

Inicializar si hace falta:

```bash
cd /etc
sudo etckeeper init
sudo etckeeper commit "Configuracion inicial"
```

Guardar cambios luego de editar configuraciones:

```bash
sudo etckeeper commit "Cambio en configuracion SSH"
```

Buena práctica:

- Registrar cambios importantes.
- Hacer copia antes de modificar archivos críticos.
- Anotar por qué se cambió una configuración.

---

## 14. Monitoreo de recursos

Ver procesos:

```bash
top
```

Instalar herramientas útiles:

```bash
sudo apt install -y htop iotop net-tools
```

Monitor interactivo:

```bash
htop
```

Ver memoria:

```bash
free -h
```

Ver carga y tiempo encendido:

```bash
uptime
```

Ver procesos con mayor consumo:

```bash
ps aux --sort=-%mem | head
ps aux --sort=-%cpu | head
```

Ver uso de disco por procesos:

```bash
sudo iotop
```

Buena práctica:

- Investigar consumos anormales antes de matar procesos.
- Revisar si el consumo alto es temporal o constante.
- Relacionar consumo con logs del servicio.

---

## 15. Red y conectividad

Ver interfaces:

```bash
ip addr
```

Ver rutas:

```bash
ip route
```

Probar gateway:

```bash
ping -c 4 192.168.1.1
```

Probar Internet por IP:

```bash
ping -c 4 1.1.1.1
```

Probar DNS:

```bash
ping -c 4 ubuntu.com
```

Ver puertos en escucha:

```bash
sudo ss -tulnp
```

Ver DNS:

```bash
resolvectl status
```

Buena práctica:

- Distinguir problemas de IP, gateway y DNS.
- Revisar puertos abiertos después de instalar servicios.
- Documentar IP fija, gateway y DNS del servidor.

---

## 16. Tareas programadas

Ver tareas cron del usuario:

```bash
crontab -l
```

Editar tareas:

```bash
crontab -e
```

Ver cron del sistema:

```bash
ls /etc/cron.*
```

Ver timers de systemd:

```bash
systemctl list-timers
```

Ejemplo de tarea diaria con cron:

```cron
0 3 * * * /usr/local/bin/backup.sh
```

Buena práctica:

- Usar rutas absolutas en scripts.
- Guardar logs de tareas automáticas.
- Probar los scripts manualmente antes de programarlos.

---

## 17. Mantenimiento de Docker en servidores

Si el servidor usa Docker, revisar contenedores:

```bash
docker ps
docker ps -a
```

Ver uso de espacio:

```bash
docker system df
```

Ver logs de un contenedor:

```bash
docker logs nombre
```

Actualizar imágenes de un proyecto Compose:

```bash
docker compose pull
docker compose up -d
```

Limpiar recursos sin uso:

```bash
docker system prune
```

Buena práctica:

- No borrar volúmenes sin verificar datos.
- Mantener archivos Compose respaldados.
- Revisar puertos publicados.
- Configurar logs para evitar crecimiento excesivo.

---

## 18. Lista de mantenimiento sugerida

### Diario

- Revisar servicios críticos.
- Verificar espacio en disco.
- Revisar alertas o logs importantes.

Comandos:

```bash
systemctl --failed
df -h
journalctl -p warning -b
```

### Semanal

- Aplicar actualizaciones pendientes.
- Revisar usuarios y accesos.
- Revisar firewall.
- Verificar respaldos.

Comandos:

```bash
sudo apt update
apt list --upgradable
sudo ufw status verbose
last
```

### Mensual

- Probar restauración de respaldos.
- Revisar servicios instalados.
- Limpiar paquetes no usados.
- Revisar documentación de cambios.

Comandos:

```bash
sudo apt autoremove
systemctl list-unit-files --state=enabled
sudo du -h --max-depth=1 /var | sort -h
```

---

## 19. Resumen de comandos

| Comando | Función |
|---|---|
| `sudo apt update` | Actualiza la lista de paquetes. |
| `sudo apt upgrade` | Instala actualizaciones. |
| `apt list --upgradable` | Lista paquetes actualizables. |
| `systemctl --failed` | Muestra servicios fallidos. |
| `journalctl -xe` | Muestra logs recientes con errores/contexto. |
| `df -h` | Muestra espacio en disco. |
| `free -h` | Muestra memoria disponible. |
| `sudo ufw status verbose` | Muestra estado del firewall. |
| `sudo ss -tulnp` | Muestra puertos abiertos. |
| `test -f /var/run/reboot-required && cat /var/run/reboot-required` | Indica si hace falta reiniciar. |
| `sudo apt autoremove` | Elimina paquetes ya no necesarios. |
| `sudo rsync -av origen/ destino/` | Copia datos preservando atributos. |

---

## 20. Fuentes consultadas

- Ubuntu Server documentation: gestión de paquetes con APT.  
  https://documentation.ubuntu.com/server/how-to/software/package-management/
- Ubuntu Server documentation: actualizaciones automáticas con `unattended-upgrades`.  
  https://documentation.ubuntu.com/server/how-to/software/automatic-updates/
- Ubuntu Server documentation: sugerencias de seguridad.  
  https://documentation.ubuntu.com/server/explanation/security/security_suggestions/
- Ubuntu Server documentation: OpenSSH Server.  
  https://documentation.ubuntu.com/server/how-to/security/openssh-server/
- Ubuntu Server documentation: respaldos y control de versiones.  
  https://documentation.ubuntu.com/server/how-to/backups/
- Ubuntu Community Help Wiki: UFW.  
  https://help.ubuntu.com/community/UFW
- Ubuntu Community Hub: discusión sobre frecuencia de actualizaciones automáticas.  
  https://discourse.ubuntu.com/t/how-often-does-apt-check-for-upgrades/54736

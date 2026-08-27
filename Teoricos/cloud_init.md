---
layout: default
title: "Cloud-init en Ubuntu Server"
---

[← Volver al portal](../)

# Cloud-init en Ubuntu Server

---

## 1. ¿Qué es cloud-init?

**cloud-init** es un servicio utilizado por Ubuntu Server para realizar la configuración inicial automática de un sistema durante el primer arranque.

Su nombre viene del mundo de la nube, pero también puede aparecer en máquinas virtuales locales, imágenes preparadas para servidores, laboratorios, VirtualBox, Proxmox, VMware, OpenStack, AWS, Azure, Google Cloud y otros entornos de virtualización.

Cuando una máquina inicia por primera vez, cloud-init puede recibir datos de configuración y aplicarlos automáticamente. Por ejemplo:

- Crear usuarios.
- Definir contraseñas o claves SSH.
- Configurar el hostname.
- Configurar red.
- Instalar paquetes.
- Ejecutar comandos iniciales.
- Escribir archivos de configuración.
- Preparar el sistema para acceso remoto.

En Ubuntu Server, cloud-init es especialmente importante en instalaciones automatizadas y en imágenes listas para desplegar.

---

## 2. ¿Para qué sirve?

cloud-init sirve para evitar configurar manualmente cada servidor desde cero.

En lugar de instalar un sistema, iniciar sesión y ejecutar comandos uno por uno, se puede entregar un archivo de configuración con las instrucciones iniciales. El sistema lee ese archivo durante el arranque y aplica los cambios.

Esto permite:

- Desplegar servidores más rápido.
- Repetir instalaciones con la misma configuración.
- Reducir errores humanos.
- Preparar muchas máquinas con usuarios, red y paquetes ya definidos.
- Automatizar laboratorios o entornos de prueba.
- Integrar Ubuntu Server con plataformas cloud.

Ejemplo de uso:

Una empresa necesita crear 20 servidores Ubuntu con el mismo usuario administrador, SSH habilitado y algunos paquetes instalados. Con cloud-init, esa configuración puede escribirse una vez y reutilizarse en todas las máquinas.

---

## 3. ¿Cuándo se ejecuta?

cloud-init se ejecuta principalmente durante el arranque del sistema.

Sus tareas se dividen en etapas:

| Etapa | Función |
|---|---|
| `init-local` | Detecta información local antes de configurar red. |
| `init` | Obtiene datos de configuración y prepara el sistema. |
| `config` | Aplica configuraciones como usuarios, paquetes y archivos. |
| `final` | Ejecuta comandos finales y deja el sistema listo. |

En la mayoría de los casos, muchas acciones se ejecutan solo en el primer arranque. Otras pueden ejecutarse nuevamente según la configuración.

---

## 4. Servicios relacionados

En Ubuntu Server, cloud-init funciona mediante servicios de `systemd`.

Para ver su estado:

```bash
systemctl status cloud-init
```

También pueden existir estos servicios:

```bash
systemctl status cloud-init-local
systemctl status cloud-config
systemctl status cloud-final
```

Para listar servicios relacionados:

```bash
systemctl list-units | grep cloud
```

---

## 5. Archivos y directorios importantes

cloud-init utiliza varios archivos y directorios:

| Ruta | Uso |
|---|---|
| `/etc/cloud/cloud.cfg` | Configuración principal. |
| `/etc/cloud/cloud.cfg.d/` | Configuraciones adicionales. |
| `/var/lib/cloud/` | Estado interno y datos aplicados. |
| `/var/log/cloud-init.log` | Log principal de cloud-init. |
| `/var/log/cloud-init-output.log` | Salida de comandos ejecutados. |

Ver logs:

```bash
sudo less /var/log/cloud-init.log
sudo less /var/log/cloud-init-output.log
```

Ver configuración principal:

```bash
less /etc/cloud/cloud.cfg
```

---

## 6. ¿Cómo se configura?

cloud-init se configura normalmente con archivos YAML.

El formato más común comienza con:

```yaml
#cloud-config
```

Debajo se escriben las opciones que se quieren aplicar al sistema.

Ejemplo básico:

```yaml
#cloud-config
hostname: servidor-lab
users:
  - name: usuario
    groups: sudo
    shell: /bin/bash
    sudo: ALL=(ALL) NOPASSWD:ALL
package_update: true
packages:
  - curl
  - vim
  - htop
```

Este archivo indica:

- Cambiar el hostname a `servidor-lab`.
- Crear un usuario llamado `usuario`.
- Agregarlo al grupo `sudo`.
- Usar `/bin/bash` como shell.
- Actualizar la lista de paquetes.
- Instalar `curl`, `vim` y `htop`.

---

## 7. Ejemplo: crear usuario y habilitar SSH

```yaml
#cloud-config
hostname: ubuntu-lab
users:
  - name: usuario
    groups: sudo
    shell: /bin/bash
    sudo: ALL=(ALL) NOPASSWD:ALL
    lock_passwd: false
ssh_pwauth: true
packages:
  - openssh-server
runcmd:
  - systemctl enable --now ssh
```

Este ejemplo:

- Define el hostname `ubuntu-lab`.
- Crea el usuario `usuario`.
- Habilita autenticación SSH con contraseña.
- Instala OpenSSH Server.
- Activa el servicio SSH.

---

## 8. Ejemplo: ejecutar comandos al primer arranque

```yaml
#cloud-config
package_update: true
package_upgrade: true
packages:
  - nginx
  - net-tools
runcmd:
  - systemctl enable --now nginx
  - echo "Servidor preparado con cloud-init" > /var/www/html/index.html
```

Este ejemplo:

- Actualiza la lista de paquetes.
- Instala actualizaciones disponibles.
- Instala `nginx` y `net-tools`.
- Activa el servidor web.
- Crea una página HTML simple.

---

## 9. Configuración de red

cloud-init también puede configurar red, aunque en Ubuntu Server la configuración final suele quedar reflejada en archivos de Netplan.

Ejemplo conceptual:

```yaml
#cloud-config
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

Esta configuración:

- Desactiva DHCP en `enp0s3`.
- Asigna la IP `192.168.1.50/24`.
- Define el gateway `192.168.1.1`.
- Configura servidores DNS.

---

## 10. ¿Dónde se entrega la configuración?

cloud-init puede recibir configuración desde diferentes fuentes de datos, llamadas **datasources**.

Algunas fuentes comunes son:

- Imagen ISO adicional con archivos de configuración.
- Plataforma cloud.
- Sistema de virtualización.
- Metadata server.
- Archivos locales.

En laboratorios, una forma común es usar una ISO pequeña con dos archivos:

```text
user-data
meta-data
```

El archivo `user-data` contiene la configuración cloud-init.

Ejemplo de `user-data`:

```yaml
#cloud-config
hostname: ubuntu-lab
package_update: true
packages:
  - htop
```

Ejemplo de `meta-data`:

```yaml
instance-id: ubuntu-lab-01
local-hostname: ubuntu-lab
```

---

## 11. Comandos útiles

Ver estado general:

```bash
cloud-init status
```

Ver estado con espera hasta que termine:

```bash
cloud-init status --wait
```

Ver versión instalada:

```bash
cloud-init --version
```

Analizar tiempos de ejecución:

```bash
cloud-init analyze blame
```

Mostrar resumen de etapas:

```bash
cloud-init analyze show
```

Limpiar el estado para volver a probar cloud-init:

```bash
sudo cloud-init clean
```

Reiniciar después de limpiar:

```bash
sudo reboot
```

---

## 12. Problemas frecuentes

### cloud-init queda esperando una fuente de datos

Puede ocurrir si la máquina busca metadata de una nube que no existe en el entorno local.

Verificar estado:

```bash
cloud-init status
```

Revisar logs:

```bash
sudo less /var/log/cloud-init.log
```

### La configuración YAML no se aplica

Revisar:

- Que el archivo empiece con `#cloud-config`.
- Que la indentación use espacios y no tabuladores.
- Que los nombres de interfaces de red sean correctos.
- Que los paquetes existan en los repositorios configurados.

### La red cambia después del primer arranque

Revisar archivos en:

```bash
/etc/netplan/
```

También revisar si cloud-init administra la red:

```bash
grep -R "network" /etc/cloud/
```

---

## 13. ¿Conviene deshabilitar cloud-init?

Depende del escenario.

cloud-init es útil cuando:

- La VM se crea desde una imagen cloud.
- Se necesita automatización inicial.
- Se despliegan muchas máquinas similares.
- La plataforma de virtualización entrega datos de configuración.

Puede deshabilitarse cuando:

- La máquina ya fue configurada manualmente.
- No se usa automatización de arranque.
- La VM es fija y no se volverá a clonar.
- Se quiere reducir tiempo de arranque.
- Se quieren liberar algunos recursos del sistema.

En una instalación de laboratorio con VirtualBox, si no se usa configuración automática, cloud-init puede no ser necesario después de dejar el servidor funcionando.

---

## 14. Cómo deshabilitar cloud-init

### Opción 1: deshabilitar cloud-init con archivo de configuración

Crear el archivo:

```bash
sudo touch /etc/cloud/cloud-init.disabled
```

Reiniciar:

```bash
sudo reboot
```

Verificar:

```bash
cloud-init status
```

Esta es una forma simple y reversible.

Para volver a habilitarlo:

```bash
sudo rm /etc/cloud/cloud-init.disabled
sudo reboot
```

---

### Opción 2: deshabilitar servicios con systemd

Deshabilitar servicios:

```bash
sudo systemctl disable cloud-init-local.service
sudo systemctl disable cloud-init.service
sudo systemctl disable cloud-config.service
sudo systemctl disable cloud-final.service
```

Detener servicios activos:

```bash
sudo systemctl stop cloud-init-local.service
sudo systemctl stop cloud-init.service
sudo systemctl stop cloud-config.service
sudo systemctl stop cloud-final.service
```

Verificar:

```bash
systemctl list-unit-files | grep cloud
```

Para volver a habilitarlos:

```bash
sudo systemctl enable cloud-init-local.service
sudo systemctl enable cloud-init.service
sudo systemctl enable cloud-config.service
sudo systemctl enable cloud-final.service
```

---

### Opción 3: desinstalar cloud-init

También se puede eliminar el paquete:

```bash
sudo apt remove cloud-init
```

Para eliminar archivos de configuración asociados:

```bash
sudo apt purge cloud-init
```

Luego se pueden borrar datos residuales:

```bash
sudo rm -rf /etc/cloud/
sudo rm -rf /var/lib/cloud/
```

> Precaución: desinstalar cloud-init no es recomendable si la máquina depende de una plataforma cloud o de automatizaciones externas.

---

## 15. Liberar recursos después de deshabilitarlo

Después de deshabilitar cloud-init, reiniciar el sistema:

```bash
sudo reboot
```

Verificar que no esté ejecutándose:

```bash
systemctl status cloud-init
```

Verificar tiempo de arranque:

```bash
systemd-analyze
```

Ver servicios que más tardaron:

```bash
systemd-analyze blame
```

Ver memoria disponible:

```bash
free -h
```

---

## 16. Resumen

cloud-init es una herramienta de automatización para preparar Ubuntu Server durante el primer arranque.

Permite configurar usuarios, red, paquetes, comandos, claves SSH y otros aspectos iniciales del sistema.

Es muy útil en nubes, virtualización y despliegues repetitivos. En una VM de laboratorio que ya fue configurada manualmente, puede deshabilitarse para simplificar el arranque y liberar recursos.

Comandos principales:

| Comando | Función |
|---|---|
| `cloud-init status` | Ver estado de cloud-init. |
| `cloud-init status --wait` | Esperar a que termine la configuración. |
| `cloud-init analyze blame` | Ver qué etapas tardan más. |
| `sudo cloud-init clean` | Limpiar estado para volver a probar. |
| `sudo touch /etc/cloud/cloud-init.disabled` | Deshabilitar cloud-init. |
| `sudo rm /etc/cloud/cloud-init.disabled` | Volver a habilitar cloud-init. |
| `sudo apt remove cloud-init` | Desinstalar cloud-init. |

# Práctica guiada: Infraestructura Linux virtualizada (SRV-BD, SRV-WEB, SRV-SMB)
---

> Esta práctica está diseñada como preparación directa para el [Caso de evaluación: Infraestructura Linux virtualizada con errores](../Eval/3_caso_evaluacion_infraestructura_errores.html). En ese caso, la empresa ficticia **TecnInutil SRL** comete 19 errores (10 de planificación, 9 de implementación) al montar tres servidores para el cliente **Comercial del Sur**. Acá vas a **implementar el mismo escenario, pero bien hecho**, evitando cada uno de esos errores.

## 1. Objetivos de aprendizaje

Al finalizar esta práctica, podrás:

1. Planificar recursos de virtualización (RAM, CPU, disco) sin sobrecomprometer el equipo anfitrión.
2. Elegir la distribución y edición de Linux adecuada según el rol del servidor.
3. Configurar el modo de red correcto para que servidores virtualizados sean alcanzables desde una LAN.
4. Instalar tres servidores Ubuntu Server de forma independiente, sin duplicar discos ni identidades de máquina.
5. Configurar IP estática con Netplan usando sintaxis YAML válida.
6. Aplicar buenas prácticas de seguridad: contraseñas robustas, principio de mínimo privilegio, hardening básico de servicios.
7. Instalar y configurar MySQL, Apache y Samba de forma completa y verificada (no solo "instalado y corriendo").
8. Usar snapshots como punto de restauración antes de pasar a producción.
9. Definir un plan de backup mínimo viable.
10. Validar los tres servicios desde un cliente externo en la red, no solo desde el propio servidor.

---

## 2. Requisitos previos

- Una computadora con VirtualBox 7.x instalado.
- Al menos **16 GB de RAM** en el equipo anfitrión (con 8 GB alcanza en modo reducido, ver sección 4.1).
- Al menos **60 GB de espacio libre en disco**, idealmente en una partición o unidad distinta a la del sistema operativo anfitrión.
- Conexión a red (Wi-Fi o cable) con acceso a routeo/switch, para poder usar adaptador puente. Si el aula no lo permite, ver la alternativa de **Red interna** en la sección 5.3.
- Haber completado la práctica [1_actividad_instalacion_ubuntu_server_virtualbox.md](1_actividad_instalacion_ubuntu_server_virtualbox.md) o tener conocimientos equivalentes de instalación de Ubuntu Server, VirtualBox y Netplan.
- Descargar previamente la ISO de **Ubuntu Server 22.04 LTS** (una sola vez, se reutiliza para las tres VMs).

---

## 3. El escenario

La consigna es la misma que en el caso de evaluación: **Comercial del Sur**, una empresa con 30 equipos en red local, necesita tres servidores Linux virtualizados sobre un anfitrión con Windows 11 y VirtualBox.

| Servidor | Función | Software principal |
|---|---|---|
| `srv-bd` | Base de datos relacional | MySQL Server |
| `srv-web` | Servidor web | Apache HTTP Server |
| `srv-smb` | Archivos compartidos | Samba |

La diferencia con el caso de TecnInutil SRL es que **acá vamos a documentar cada decisión y justificarla**, en lugar de improvisar.

---

## 4. Parte 1 — Planificación correcta de recursos

### 4.1. Regla general de asignación de RAM

Nunca hay que asignar a las VMs toda la RAM disponible del anfitrión. El sistema operativo anfitrión (Windows 11), el propio VirtualBox y las aplicaciones que uses (navegador, editor, etc.) también necesitan memoria.

> **Regla práctica:** no asignar a las VMs, en conjunto, más del 60-70 % de la RAM total del host. El resto queda como margen para el anfitrión.

Con un host de 16 GB, una asignación razonable es:

| Parámetro | `srv-bd` | `srv-web` | `srv-smb` | Total VMs | RAM libre para el host |
|---|---|---|---|---|---|
| RAM asignada | 3 GB | 2 GB | 2 GB | 7 GB | 9 GB |

Si tu equipo tiene 8 GB de RAM, reducí a 2 GB / 1 GB / 1 GB (4 GB total) y bajá también los núcleos de CPU a 1 por VM.

**Por qué importa:** en el caso de TecnInutil SRL, la suma de RAM asignada (6+4+4 = 14 GB) dejaba solo 2 GB para un host de 16 GB con Windows 11, lo que provoca lentitud severa o directamente que las VMs no puedan arrancar.

### 4.2. Elección de sistema operativo — consistencia y propósito

Usá **la misma distribución y versión LTS en los tres servidores**: Ubuntu Server 22.04 LTS.

- **Server, no Desktop:** un servidor no necesita entorno gráfico. Ubuntu Desktop consume RAM y disco innecesarios y amplía la superficie de ataque con paquetes que no se van a usar.
- **LTS, no una versión interina:** las versiones no-LTS (como 23.10) tienen ~9 meses de soporte y no están pensadas para producción. Un servidor de una empresa real necesita soporte extendido (5 años en LTS).

| Parámetro | `srv-bd` | `srv-web` | `srv-smb` |
|---|---|---|---|
| Sistema operativo | Ubuntu **Server** 22.04 LTS | Ubuntu **Server** 22.04 LTS | Ubuntu **Server** 22.04 LTS |
| CPU (núcleos) | 2 | 1 | 1 |
| Disco virtual | 20 GB dinámico (VDI) | 20 GB dinámico (VDI) | 20 GB dinámico (VDI) |

### 4.3. Hostnames únicos

Cada VM necesita un nombre de host **distinto**, que además identifique su función:

| VM | Hostname |
|---|---|
| Base de datos | `srv-bd` |
| Servidor web | `srv-web` |
| Servidor de archivos | `srv-smb` |

**Por qué importa:** dos equipos con el mismo hostname en la misma red generan conflictos de resolución de nombres (NetBIOS, mDNS, logs confusos, problemas al administrar por SSH con alias). El instalador de Ubuntu Server pide el hostname durante la instalación; escribilo ahí directamente, no lo dejes en el valor por defecto.

### 4.4. Ruta y organización del almacenamiento

Antes de crear la primera VM:

1. Verificá el espacio libre real en el disco donde vas a guardar las VMs (`60 GB` mínimo para las tres, con margen).
2. Si el host tiene una sola partición (`C:\`), como en el caso de TecnInutil SRL, evaluá si conviene:
   - liberar espacio o mover archivos personales antes de empezar, o
   - usar un disco externo/segunda partición si existe.
3. Creá una carpeta dedicada y organizada, por ejemplo:

```text
D:\VMs\srv-bd\
D:\VMs\srv-web\
D:\VMs\srv-smb\
```

Una carpeta por VM evita mezclar discos virtuales y facilita backups o migraciones futuras.

**Por qué importa:** guardar todo en `Documentos` sobre una única partición del sistema, sin verificar espacio disponible, es lo que hizo TecnInutil SRL. Si la partición del sistema se llena, Windows y las VMs dejan de funcionar correctamente al mismo tiempo.

### 4.5. Direccionamiento IP

Antes de instalar, definí un plan de IPs estáticas para los tres servidores, dentro del rango de tu red y **fuera del pool DHCP** del router (consultalo en la configuración del router o con tu docente):

| VM | IP estática planificada |
|---|---|
| `srv-bd` | `192.168.1.101/24` |
| `srv-web` | `192.168.1.102/24` |
| `srv-smb` | `192.168.1.103/24` |

**Por qué importa:** un servidor con IP por DHCP puede cambiar de dirección al reiniciarse, rompiendo cualquier configuración que dependa de esa IP (clientes SMB, apps que consultan la base de datos, accesos SSH guardados). Los servidores siempre deben tener IP predecible.

---

## 5. Parte 2 — Creación de las tres VMs

### 5.1. Instalación independiente (sin copiar discos)

Creá **cada una de las tres VMs desde cero**, usando la misma ISO de Ubuntu Server 22.04 LTS:

1. VirtualBox → **Nueva** → nombre `srv-bd` (o `srv-web` / `srv-smb`), tipo Linux, versión Ubuntu (64-bit).
2. Asignar RAM y CPU según la tabla de la sección 4.1/4.2.
3. Crear disco virtual nuevo VDI, dinámico, 20 GB, guardado en la carpeta correspondiente de la sección 4.4.
4. Montar la ISO de Ubuntu Server 22.04 LTS y completar la instalación manual, indicando el hostname correcto en cada una (sección 4.3).

> **Nunca copies el archivo `.vdi` de una VM para "crear" otra con el Explorador de archivos.** Cada disco virtual tiene un UUID único que VirtualBox usa para identificarlo internamente; copiar el archivo a mano produce un UUID duplicado (VirtualBox lo rechaza o genera conflictos al registrar el disco) y, además, heredás el contenido, los datos y hasta las claves SSH del host de origen — no un servidor limpio e independiente.

Si en algún momento necesitás duplicar una VM como base para otra (por ejemplo, para ahorrar tiempo en un laboratorio), la forma correcta es:

```bash
VBoxManage clonevm "srv-web" --name "srv-smb" --register --mode all
```

Esto genera un clon completo con **UUIDs nuevos** para la VM y sus discos. Aun así, para este caso concreto lo recomendado sigue siendo una instalación limpia por servidor, porque `srv-smb` no comparte ningún rol con `srv-web`.

### 5.2. Instalación de Guest Additions (opcional pero recomendado)

En cada VM, una vez instalada:

```bash
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

Luego montá el CD de Guest Additions desde el menú de VirtualBox y ejecutá el instalador correspondiente.

### 5.3. Modo de red: Bridged (o Red interna como alternativa de laboratorio)

Comercial del Sur necesita que los 30 equipos de su LAN alcancen a los tres servidores. Con el adaptador **NAT** (el que usó TecnInutil SRL), cada VM queda aislada detrás de una NAT privada de VirtualBox: solo la propia VM puede salir a Internet, pero **ningún otro equipo de la red puede conectarse a ella**.

Configurá el adaptador de red de cada VM en **Adaptador puente (Bridged Adapter)**, seleccionando la interfaz de red física real del host (Wi-Fi o Ethernet):

```text
VirtualBox → Configuración de la VM → Red → Adaptador 1
  Conectado a: Adaptador puente
  Nombre: (tu interfaz de red física)
```

> **Alternativa de aula:** si la red del centro no permite bridging (Wi-Fi con aislamiento de clientes, por ejemplo), usá **Red interna** (Internal Network) con el mismo nombre en las tres VMs, más un cuarto adaptador NAT en cada una solo para salida a Internet (actualizaciones). Esto simula una LAN aislada entre los tres servidores y te permite igualmente validar la comunicación entre ellos y desde un cliente adicional conectado a esa misma red interna.

**Por qué importa:** el modo de red no es un detalle menor — define si el servicio que vas a instalar después va a poder ser usado por alguien más o no.

---

## 6. Parte 3 — Configuración de red con Netplan

Identificá primero el nombre real de la interfaz de red en cada VM (puede no ser `enp0s3` en modo bridged):

```bash
ip a
```

Editá el archivo de Netplan (generalmente `/etc/netplan/00-installer-config.yaml`). Usá **espacios, nunca tabuladores**, para la indentación — YAML no admite tabs y el archivo va a fallar al aplicarlo.

Ejemplo para `srv-bd` (adaptar la interfaz y la IP según corresponda a cada servidor):

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.1.101/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

> **Importante:** definí `dhcp4: no` cuando asignás una dirección estática. Nunca dejes `dhcp4: true` junto con una IP fija en `addresses` — son dos formas de asignación en conflicto y el comportamiento resultante es impredecible (a veces gana el DHCP, a veces la estática, según el renderer y el momento del arranque).

Validá la sintaxis antes de aplicar, y luego aplicá:

```bash
sudo netplan try
sudo netplan apply
```

`netplan try` muestra un preview y revierte automáticamente si algo falla — es más seguro que aplicar directo, sobre todo si estás conectado por SSH.

Repetí este procedimiento en `srv-web` (`192.168.1.102/24`) y `srv-smb` (`192.168.1.103/24`), cada una con `dhcp4: no`.

---

## 7. Parte 4 — Hardening básico antes de instalar servicios

Antes de instalar MySQL, Apache o Samba, dejá una base mínima de seguridad en los tres servidores:

```bash
sudo apt update && sudo apt upgrade -y
sudo adduser admintech
sudo usermod -aG sudo admintech
```

Cuando `adduser` pida la contraseña, usá una **contraseña robusta y distinta en cada servidor** (mínimo 12 caracteres, combinando mayúsculas, minúsculas, números y símbolos), o mejor aún, configurá autenticación SSH por clave pública y deshabilitá el login por contraseña.

> **Nunca reutilices la misma contraseña simple (como `123456`) en varios usuarios o servicios.** Si se compromete una credencial, no debe servir para acceder a todo lo demás. Esto incluye la contraseña del usuario del sistema y la del usuario root de cualquier base de datos: **deben ser independientes**.

Firewall básico (ajustá los puertos según el rol de cada servidor):

```bash
sudo ufw allow OpenSSH
sudo ufw enable
```

Más adelante, en cada servidor, se agregan solo los puertos que ese servidor específico necesita (3306 en `srv-bd`, 80 en `srv-web`, 139/445 en `srv-smb`), no todos los puertos en todos los servidores.

---

## 8. Parte 5 — `srv-bd`: instalación y configuración de MySQL

### 8.1. Instalación

```bash
sudo apt update
sudo apt install -y mysql-server
sudo systemctl enable --now mysql
```

> No existe un subcomando `apt download` para instalar software (descarga el `.deb` sin instalarlo, y no es lo que se necesita acá). Los comandos correctos son `apt update` para refrescar el índice de paquetes y `apt install` para instalar.

### 8.2. Hardening obligatorio

```bash
sudo mysql_secure_installation
```

Respondé:
- Establecer una contraseña robusta para `root` de MySQL (**distinta** a la del usuario del sistema).
- Eliminar usuarios anónimos: sí.
- Deshabilitar login remoto de `root`: sí.
- Eliminar la base `test`: sí.
- Recargar privilegios: sí.

### 8.3. Usuario de aplicación con privilegios mínimos

El acceso de las aplicaciones de red **nunca debe hacerse con el usuario root**. Creá un usuario dedicado, con permisos solo sobre la base que necesita:

```sql
sudo mysql
CREATE DATABASE comercial_sur;
CREATE USER 'app_comercial'@'192.168.1.0/255.255.255.0' IDENTIFIED BY 'Contraseña-Robusta-Unica!23';
GRANT SELECT, INSERT, UPDATE, DELETE ON comercial_sur.* TO 'app_comercial'@'192.168.1.0/255.255.255.0';
FLUSH PRIVILEGES;
EXIT;
```

Restringir el usuario a la subred (`192.168.1.0/255.255.255.0`) en lugar de `'%'` limita desde dónde se puede autenticar, aunque la contraseña se filtre.

### 8.4. Habilitar acceso remoto real (si las apps lo necesitan)

Si la consigna requiere acceso desde otros equipos de la red, hay que **cambiar** `bind-address` — dejarlo en `127.0.0.1` (el valor por defecto) impide cualquier conexión remota, incluso después de crear usuarios de red, porque MySQL directamente no escucha en la interfaz de red.

Editar `/etc/mysql/mysql.conf.d/mysqld.cnf`:

```ini
bind-address            = 192.168.1.101
```

Reiniciar el servicio y abrir el puerto solo a la LAN:

```bash
sudo systemctl restart mysql
sudo ufw allow from 192.168.1.0/24 to any port 3306
```

### 8.5. Verificación desde otro equipo de la red

```bash
mysql -h 192.168.1.101 -u app_comercial -p comercial_sur
```

Probar la conexión **desde otra máquina de la LAN**, no desde el propio `srv-bd` — es la única forma real de confirmar que el servicio quedó accesible para quien lo necesita.

---

## 9. Parte 6 — `srv-web`: instalación y configuración de Apache

```bash
sudo apt update
sudo apt install -y apache2
sudo systemctl enable --now apache2
sudo ufw allow 'Apache'
```

> Todo comando de administración de paquetes o servicios necesita `sudo`. Ejecutar `apt update` sin privilegios elevados falla directamente por permisos y no refresca el índice de paquetes.

### Verificación desde un cliente externo

```bash
curl http://192.168.1.102
```

Ejecutalo **desde otra computadora de la red**, no con `curl http://localhost` en el propio servidor. Probar en localhost solo confirma que Apache arrancó, no que el firewall, la red y el modo de adaptador estén realmente permitiendo el acceso desde afuera — que es lo que le importa al cliente.

---

## 10. Parte 7 — `srv-smb`: instalación y configuración de Samba

```bash
sudo apt update
sudo apt install -y samba
```

### 10.1. Permisos del recurso compartido

Creá un grupo dedicado para quienes van a usar el recurso, en lugar de dejarlo restringido solo a `root`:

```bash
sudo mkdir /srv/compartido
sudo groupadd compartido
sudo usermod -aG compartido admintech
sudo chown root:compartido /srv/compartido
sudo chmod 2775 /srv/compartido
```

El bit `setgid` (el `2` inicial en `2775`) hace que los archivos nuevos creados dentro de la carpeta hereden el grupo `compartido` automáticamente.

> Si en `smb.conf` vas a permitir escritura (`read only = no`) e incluso acceso de invitado (`guest ok = yes`), los permisos del sistema de archivos tienen que **acompañar** esa intención. Con `chmod 700 root:root`, solo el usuario `root` puede escribir en la carpeta: cualquier usuario de Samba (autenticado o invitado) que intente guardar un archivo va a recibir un error de permiso denegado, sin importar lo que diga `smb.conf`. La configuración de Samba y los permisos de Linux se validan en conjunto — no alcanza con configurar uno solo de los dos.

### 10.2. Configuración de Samba

Editá `/etc/samba/smb.conf` y agregá al final (recomendado: acceso autenticado, no de invitado, para un entorno empresarial):

```ini
[global]
   workgroup = WORKGROUP
   server string = Servidor de Archivos Comercial del Sur

[compartido]
   path = /srv/compartido
   browseable = yes
   read only = no
   guest ok = no
   valid users = @compartido
```

Creá la contraseña Samba del usuario que va a acceder (contraseña distinta a la del sistema):

```bash
sudo smbpasswd -a admintech
```

> Si tu docente pide explícitamente un recurso de invitado sin autenticación, es una decisión válida siempre que quede documentada como tal — pero entonces los permisos de archivo también deben permitir escritura a `others`, y hay que asumir que **cualquiera** en la red podrá escribir ahí.

### 10.3. Habilitar el servicio al inicio

```bash
sudo systemctl enable --now smbd
sudo systemctl enable --now nmbd
sudo ufw allow Samba
```

> No alcanza con `systemctl start` y verificar `systemctl status`. Sin `systemctl enable`, el servicio no vuelve a levantarse solo si el servidor se reinicia — y un servidor de archivos que deja de responder después de un corte de luz es un problema real para 30 personas trabajando.

### 10.4. Verificación desde un cliente externo

Desde Windows: `\\192.168.1.103\compartido`

Desde otro Linux: `smbclient -L //192.168.1.103 -U admintech`

---

## 11. Parte 8 — Snapshot antes de producción

Antes de anunciarle a "Comercial del Sur" que los servidores están listos para usarse:

1. Apagar cada VM (`sudo shutdown now`, o apagado limpio desde VirtualBox).
2. En VirtualBox, ir a **Instantáneas** → **Tomar instantánea**.
3. Nombrarla de forma descriptiva, por ejemplo: `srv-bd - MySQL instalado y verificado - antes de produccion`.

**Por qué importa:** un snapshot es un punto de restauración. Si algo sale mal después de la puesta en producción (una actualización rompe un servicio, un cambio de configuración tiene un error), se puede volver atrás en minutos en lugar de reconstruir el servidor desde cero. TecnInutil SRL pasó directo a producción sin ningún punto de restauración ni etapa de prueba.

---

## 12. Parte 9 — Plan mínimo de backup

Un plan de puesta en producción no está completo sin backup. Como mínimo:

| Qué respaldar | Herramienta sugerida | Frecuencia sugerida |
|---|---|---|
| Base de datos MySQL | `mysqldump` hacia un archivo fechado | Diario |
| Archivos de configuración (`/etc/netplan`, `/etc/mysql`, `/etc/samba`, `/etc/apache2`) | `tar` o `rsync` a un destino externo a la VM | Ante cada cambio relevante |
| Contenido de `/srv/compartido` | `rsync` a un destino externo a la VM | Diario o semanal, según el volumen de cambios |

Ejemplo simple con `cron` para el dump diario de MySQL:

```bash
sudo crontab -e
```

```cron
0 2 * * * mysqldump -u root -p'contraseña' comercial_sur > /respaldos/comercial_sur_$(date +\%F).sql
```

> El backup no sirve si vive en el mismo disco que puede fallar. Copiá los respaldos generados a un destino externo a la VM (otro disco, un recurso compartido, almacenamiento en la nube del centro, etc.) de forma periódica.

---

## 13. Parte 10 — Validación final desde la LAN

Antes de dar por terminada la infraestructura, probá **los tres servicios desde una cuarta máquina** conectada a la misma red (puede ser el propio host, otro compañero, o una VM cliente adicional):

| Servicio | Comando de verificación | Resultado esperado |
|---|---|---|
| Conectividad general | `ping 192.168.1.101` / `.102` / `.103` | Responden los tres |
| SSH | `ssh admintech@192.168.1.101` | Conecta con la contraseña/clave configurada |
| MySQL | `mysql -h 192.168.1.101 -u app_comercial -p` | Conecta y permite consultar `comercial_sur` |
| Apache | `curl http://192.168.1.102` | Devuelve el HTML de la página |
| Samba | `smbclient -L //192.168.1.103 -U admintech` | Lista el recurso `compartido` y permite autenticarse |

Documentá los resultados: capturas de pantalla o salida de consola de cada prueba, para incluir como evidencia de la práctica.

---

## 14. Comparación: errores de TecnInutil SRL vs. lo que hicimos acá

| # | Error de TecnInutil SRL | Qué hicimos distinto en esta práctica |
|---|---|---|
| 1 | RAM de las VMs (14 GB) casi agota los 16 GB del host | Asignación con margen (≤70 % de la RAM del host) |
| 2 | `srv-bd` con Ubuntu **Desktop** | Ubuntu **Server** en los tres servidores |
| 3 | `srv-web` con Ubuntu 23.10 (no LTS) | Ubuntu Server 22.04 **LTS** en los tres |
| 4 | Adaptador **NAT** en los tres, inaccesibles desde la LAN | Adaptador **puente** (o Red interna en el aula) |
| 5 | `srv-bd` y `srv-web` con IP por DHCP | IP estática planificada en los tres servidores |
| 6 | Hostname `servidor` repetido en los tres | Hostnames únicos: `srv-bd`, `srv-web`, `srv-smb` |
| 7 | Todo guardado en `Documentos` de una única partición sin verificar espacio | Carpeta dedicada por VM, con espacio verificado previamente |
| 8 | `srv-smb` creado copiando el `.vdi` de `srv-web` a mano | VMs independientes desde cero (o `VBoxManage clonevm` si se clona) |
| 9 | Contraseña `123456` compartida entre sistema y MySQL root | Contraseñas robustas y distintas por usuario/servicio |
| 10 | Sin etapa de prueba, sin snapshot, sin plan de backup | Snapshot previo a producción + plan de backup documentado |
| 11 | Netplan de `srv-bd` con **tabuladores** en el YAML | Indentación con espacios, validado con `netplan try` |
| 12 | `sudo apt download` (comando inexistente/incorrecto) | `sudo apt update && sudo apt install` |
| 13 | MySQL sin `mysql_secure_installation` | Hardening ejecutado y verificado |
| 14 | `bind-address = 127.0.0.1` pese a requerir acceso desde apps de red | `bind-address` configurado a la IP del servidor, con firewall restringido a la LAN |
| 15 | `apt update` sin `sudo` en `srv-web` | Todos los comandos administrativos con `sudo` |
| 16 | Apache verificado solo con `curl localhost` | Verificado con `curl` desde otro equipo de la LAN |
| 17 | Netplan de `srv-smb` con `dhcp4: true` **y** IP estática a la vez | `dhcp4: no` cuando se define una IP fija |
| 18 | `/srv/compartido` en `chmod 700 root:root` con `guest ok = yes` en Samba | Permisos de grupo (`2775`) coherentes con la configuración de Samba |
| 19 | Samba nunca habilitado con `systemctl enable` | `systemctl enable --now smbd nmbd` |

---

## 15. Entregable de la práctica

Para cada uno de los tres servidores, entregar:

1. Captura de la configuración de red de la VM en VirtualBox (modo de adaptador).
2. Contenido del archivo de Netplan aplicado.
3. Salida de los comandos de verificación de la sección 13, ejecutados **desde otra máquina** de la red.
4. Captura de las instantáneas creadas en VirtualBox.
5. Un párrafo breve por servidor explicando una decisión de configuración tomada y por qué (por ejemplo, por qué se restringió el usuario de MySQL a la subred en lugar de usar `'%'`).

> Esta práctica no reemplaza al caso de evaluación — es su preparación. Una vez completada, vas a poder reconocer cada uno de los 19 errores de TecnInutil SRL porque los evitaste con tus propias manos.

---

## 16. Anexo — Referencia rápida de comandos

```bash
# Red
ip a
sudo netplan try
sudo netplan apply

# MySQL
sudo mysql_secure_installation
sudo mysql
sudo systemctl enable --now mysql

# Apache
sudo systemctl enable --now apache2
curl http://IP_DEL_SERVIDOR

# Samba
sudo systemctl enable --now smbd nmbd
sudo smbpasswd -a usuario
smbclient -L //IP_DEL_SERVIDOR -U usuario

# Firewall
sudo ufw allow OpenSSH
sudo ufw allow from 192.168.1.0/24 to any port 3306
sudo ufw allow 'Apache'
sudo ufw allow Samba
sudo ufw enable
sudo ufw status
```

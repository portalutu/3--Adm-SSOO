# Actividad práctica: Instalación y configuración de Ubuntu Server en VirtualBox
---

## 1. Objetivos de aprendizaje

Al finalizar la actividad, se espera que el estudiante pueda:

1. Descargar correctamente una imagen ISO de Ubuntu Server.
2. Crear una máquina virtual en VirtualBox con recursos adecuados.
3. Instalar Ubuntu Server de forma manual.
4. Comprender el concepto de instalación desatendida y sus diferencias con una instalación manual.
5. Instalar las Guest Tools o Guest Additions en la máquina virtual.
6. Configurar la interfaz de red de la VM en modo bridged.
7. Asignar una IP fija usando Netplan.
8. Verificar conectividad local y externa mediante comandos de red.
9. Documentar el proceso realizado con capturas, comandos y conclusiones.

---

## 2. Requisitos previos

Antes de comenzar, cada equipo debe contar con:

- Una computadora con VirtualBox instalado.
- Conexión a Internet.
- Al menos 4 GB de RAM disponible en el equipo anfitrión.
- Al menos 25 GB de espacio libre en disco.
- Permisos para descargar archivos e instalar software.
- Conocimientos básicos de:
  - Sistemas operativos.
  - Archivos ISO.
  - Máquinas virtuales.
  - Direcciones IP.
  - Terminal Linux.

---

## 3. Conceptos clave antes de comenzar

### 3.1. ¿Qué es Ubuntu?

Ubuntu es una distribución GNU/Linux basada en Debian, desarrollada y mantenida por Canonical junto con una amplia comunidad de usuarios y desarrolladores. Es una de las distribuciones Linux más utilizadas en entornos educativos, personales, empresariales y de servidores.

Una distribución GNU/Linux es un sistema operativo completo que integra el kernel Linux, herramientas del proyecto GNU, gestor de paquetes, utilidades de administración, repositorios de software y documentación. En el caso de Ubuntu, se busca ofrecer un sistema estable, documentado y relativamente sencillo de instalar y administrar.

### 3.2. Justificación del uso de Ubuntu Linux en esta actividad

Se utilizará Ubuntu Server porque permite trabajar con un entorno realista de administración de sistemas, pero accesible para estudiantes que se están iniciando en GNU/Linux, redes y soporte IT.

Las principales razones pedagógicas y técnicas son:

- **Amplia documentación:** Ubuntu cuenta con documentación oficial, foros, tutoriales y ejemplos de uso, lo que facilita el aprendizaje autónomo y la resolución de problemas.
- **Uso frecuente en entornos reales:** Ubuntu Server se utiliza en servidores web, servicios cloud, laboratorios, contenedores, automatización, bases de datos y despliegue de aplicaciones.
- **Compatibilidad con VirtualBox:** funciona correctamente en máquinas virtuales y permite realizar prácticas sin modificar el equipo físico.
- **Base técnica sólida:** permite trabajar con conceptos importantes como usuarios, permisos, servicios, red, paquetes, procesos, logs y terminal.
- **Gestión de paquetes sencilla:** utiliza `apt`, lo que simplifica la instalación, actualización y eliminación de software.
- **Apropiado para enseñanza:** permite comenzar con tareas simples y avanzar luego hacia administración de servicios, scripting, seguridad y redes.
- **Relación directa con el campo laboral:** muchos procedimientos aprendidos en Ubuntu Server son transferibles a otras distribuciones Linux usadas profesionalmente.

Desde el punto de vista didáctico, Ubuntu Server permite que el estudiante comprenda cómo se instala, configura y administra un servidor real, sin depender de una interfaz gráfica y trabajando directamente con herramientas de consola.

### 3.3. Comparativa entre diferentes versiones de Ubuntu

Ubuntu se distribuye en varias ediciones o sabores, cada una orientada a un tipo de uso específico. Para esta actividad usaremos **Ubuntu Server**, pero es importante conocer las diferencias principales.

| Versión / edición | Uso principal | Interfaz gráfica | Público objetivo | Ventajas | Limitaciones |
|---|---|---:|---|---|---|
| **Ubuntu Desktop** | Uso general en PC o notebook | Sí | Usuarios finales, estudiantes, oficinas | Fácil de usar, entorno gráfico completo, buena compatibilidad de hardware | Consume más recursos que una instalación de servidor |
| **Ubuntu Server** | Servidores, servicios de red, infraestructura | No por defecto | Administradores de sistemas, soporte IT, laboratorios, servidores | Ligero, estable, orientado a consola, ideal para servicios y prácticas de administración | Requiere mayor uso de terminal |
| **Ubuntu LTS** | Entornos que necesitan estabilidad prolongada | Depende de la edición | Empresas, educación, servidores, producción | Soporte extendido, actualizaciones de seguridad por varios años, recomendado para producción | Puede no incluir siempre las versiones más recientes de algunos paquetes |
| **Ubuntu Interim / no LTS** | Pruebas, hardware reciente, software más nuevo | Depende de la edición | Usuarios avanzados, pruebas técnicas | Paquetes más recientes, útil para experimentar novedades | Menor tiempo de soporte, menos recomendable para servidores estables |
| **Ubuntu Core** | IoT, dispositivos embebidos, appliances | No tradicional | Industria, dispositivos, edge computing | Sistema mínimo, transaccional, basado en snaps, enfoque en seguridad | No es ideal para aprender administración tradicional de Linux |
| **Ubuntu Cloud Images** | Nube y virtualización automatizada | No por defecto | Cloud, OpenStack, AWS, Azure, Proxmox, automatización | Imágenes listas para despliegue rápido, integración con cloud-init | Menos didácticas para una primera instalación manual |
| **Kubuntu / Xubuntu / Lubuntu** | Escritorio con diferentes entornos gráficos | Sí | Usuarios de escritorio | Permiten elegir interfaz y consumo de recursos | No están orientadas específicamente a servidores |

### 3.4. ¿Por qué no usamos Ubuntu Desktop para esta práctica?

Ubuntu Desktop es útil para aprender Linux desde una interfaz gráfica, pero esta práctica busca simular el trabajo con un servidor. En un servidor real, muchas veces no se instala entorno gráfico para ahorrar recursos y reducir superficie de ataque.

Ubuntu Server obliga a trabajar con terminal, archivos de configuración y comandos. Esto permite desarrollar habilidades necesarias para soporte IT, administración de sistemas y redes.

### 3.5. ¿Qué es Ubuntu Server?

Ubuntu Server es una edición de Ubuntu orientada al uso en servidores. A diferencia de Ubuntu Desktop, normalmente no incluye entorno gráfico por defecto. Se administra principalmente mediante consola o conexión remota.

Se utiliza en servidores web, bases de datos, servicios de red, virtualización, contenedores, almacenamiento, automatización y muchas otras tareas propias de infraestructura IT.

### 3.6. ¿Qué es una imagen ISO?

Una imagen ISO es un archivo que contiene una copia completa de un medio de instalación, como un DVD o pendrive de arranque. En VirtualBox, la ISO se puede montar como si fuera una unidad óptica virtual para iniciar la instalación del sistema operativo.

### 3.7. ¿Qué es una máquina virtual?

Una máquina virtual es una computadora simulada por software. Tiene CPU, memoria RAM, disco, tarjeta de red y otros componentes virtuales. Permite instalar y probar sistemas operativos sin modificar directamente el equipo físico.

### 3.8. ¿Qué es una instalación manual?

Una instalación manual es aquella en la que el usuario responde paso a paso las preguntas del instalador: idioma, teclado, red, disco, usuario, contraseña, paquetes y opciones adicionales.

### 3.9. ¿Qué es una instalación desatendida?

Una instalación desatendida automatiza parte o todo el proceso de instalación mediante un archivo de respuestas o configuración previa. En Ubuntu Server moderno se suele usar **Autoinstall**, basado en archivos de configuración YAML.

En esta actividad veremos la diferencia conceptual y prepararemos la VM para poder utilizar una ISO en modo normal. La instalación principal se realizará manualmente, porque permite comprender mejor cada decisión técnica.

### 3.10. ¿Qué son las Guest Tools o Guest Additions?

Las Guest Tools son componentes que mejoran la integración entre la máquina virtual y el equipo anfitrión. En VirtualBox se conocen como **Guest Additions**. Permiten mejoras como mejor soporte de pantalla, portapapeles compartido, carpetas compartidas y controladores optimizados.

En Ubuntu Server, al no tener interfaz gráfica, algunas funciones visuales no serán tan relevantes, pero sí es útil instalar paquetes de soporte para integración y administración.

### 3.11. ¿Qué significa modo bridged?

El modo **bridged** o **adaptador puente** conecta la máquina virtual directamente a la red física del equipo anfitrión. La VM se comporta como si fuera otro equipo conectado al mismo router o switch.

Esto permite que otros equipos de la red puedan comunicarse con la VM, siempre que el firewall y la configuración de red lo permitan.

### 3.12. ¿Qué es Netplan?

Netplan es la herramienta utilizada en Ubuntu para definir la configuración de red mediante archivos YAML. Permite configurar interfaces de red, direcciones IP, gateways, DNS y otros parámetros.

Los archivos de Netplan suelen encontrarse en:

```bash
/etc/netplan/
```

---

## 4. Parte 1: Descargar Ubuntu Server

### Paso 1: Ingresar al sitio oficial

1. Abrir un navegador web.
2. Ingresar al sitio oficial de Ubuntu:

```text
https://ubuntu.com/download/server
```

3. Seleccionar la versión LTS recomendada.

> **Nota:** LTS significa *Long Term Support*. Es una versión con soporte extendido, recomendada para servidores y entornos de producción.

### Paso 2: Descargar la ISO

1. Descargar el archivo ISO de Ubuntu Server.
2. Guardarlo en una carpeta fácil de ubicar, por ejemplo:

```text
Descargas/ISOs/
```

3. Verificar que el archivo descargado tenga extensión:

```text
.iso
```

Ejemplo:

```text
ubuntu-24.04.x-live-server-amd64.iso
```

### Evidencia solicitada

Registrar en el informe:

- Nombre exacto del archivo ISO descargado.
- Versión de Ubuntu Server.
- Captura de pantalla de la ISO descargada.

---

## 5. Parte 2: Crear la máquina virtual en VirtualBox

### Paso 1: Abrir VirtualBox

1. Abrir Oracle VirtualBox.
2. Hacer clic en **Nueva**.

### Paso 2: Definir nombre y tipo de sistema

Completar los campos de la siguiente manera:

- **Nombre:** `Ubuntu-Server-Lab`
- **Carpeta:** dejar la predeterminada o seleccionar una carpeta de trabajo.
- **Imagen ISO:** seleccionar la ISO descargada.
- **Tipo:** Linux.
- **Versión:** Ubuntu (64-bit).

Si VirtualBox ofrece la opción de instalación desatendida, se puede observar que solicita datos como usuario, contraseña y hostname.

Para esta actividad se recomienda marcar la opción:

```text
Omitir instalación desatendida / Skip Unattended Installation
```

Esto permitirá realizar una instalación manual y comprender cada paso.

### Paso 3: Asignar memoria RAM

Configurar:

- **RAM mínima:** 2048 MB.
- **RAM recomendada:** 4096 MB, si el equipo anfitrión lo permite.

No asignar toda la memoria del equipo físico, porque el sistema anfitrión también necesita recursos para funcionar.

### Paso 4: Asignar procesadores

Configurar:

- **CPU mínima:** 1 núcleo.
- **CPU recomendada:** 2 núcleos.

### Paso 5: Crear disco duro virtual

Seleccionar:

- **Crear un disco duro virtual ahora.**
- **Tamaño mínimo:** 20 GB.
- **Tamaño recomendado:** 25 GB o más.
- **Tipo:** VDI, si VirtualBox lo solicita.
- **Reserva:** Dinámica, salvo indicación contraria del docente.

### Paso 6: Revisar configuración final

Antes de iniciar la VM, verificar:

- Nombre correcto.
- ISO cargada.
- RAM suficiente.
- CPU asignada.
- Disco creado.

### Evidencia solicitada

Registrar en el informe:

- Nombre de la VM.
- RAM asignada.
- Cantidad de CPU asignadas.
- Tamaño del disco virtual.
- Captura de la configuración general de la VM.

---

## 6. Parte 3: Configurar la red de la VM antes de instalar

### Paso 1: Abrir configuración de red

1. Seleccionar la VM.
2. Hacer clic en **Configuración**.
3. Ir a **Red**.
4. Seleccionar **Adaptador 1**.

### Paso 2: Seleccionar modo bridged

En **Conectado a**, elegir:

```text
Adaptador puente / Bridged Adapter
```

En **Nombre**, seleccionar la tarjeta de red física que está conectada a Internet. Puede ser:

- Wi-Fi.
- Ethernet.

> **Importante:** En algunos equipos, el modo bridged con Wi-Fi puede presentar limitaciones según el adaptador, el sistema operativo anfitrión o la red del centro educativo.

### Paso 3: Verificar opciones avanzadas

En opciones avanzadas se recomienda:

- **Tipo de adaptador:** dejar el valor predeterminado.
- **Modo promiscuo:** denegar, salvo indicación del docente.
- **Cable conectado:** activado.

### Evidencia solicitada

Registrar en el informe:

- Captura de pantalla de la configuración de red en modo bridged.
- Nombre del adaptador físico seleccionado.

---

## 7. Parte 4: Instalación manual de Ubuntu Server

### Paso 1: Iniciar la máquina virtual

1. Seleccionar la VM.
2. Hacer clic en **Iniciar**.
3. Esperar a que cargue el instalador de Ubuntu Server.

### Paso 2: Seleccionar idioma

Elegir el idioma del instalador. Se recomienda:

```text
English
```

> **Nota:** Aunque se puede instalar en español, en servidores reales es frecuente encontrar sistemas en inglés. Esto facilita la búsqueda de documentación técnica y mensajes de error.

### Paso 3: Seleccionar distribución de teclado

Seleccionar la distribución correspondiente al teclado del laboratorio.

Opciones frecuentes:

```text
Spanish
Latin American
```

Probar caracteres importantes:

```text
/
-
_
:
@
```

Estos caracteres son necesarios para trabajar en terminal.

### Paso 4: Elegir tipo de instalación

Seleccionar:

```text
Ubuntu Server
```

No seleccionar una versión mínima si el docente no lo indica.

### Paso 5: Configuración de red durante la instalación

El instalador intentará obtener una IP automáticamente mediante DHCP.

En este punto se puede aceptar la configuración automática, porque luego configuraremos la IP fija manualmente con Netplan.

Verificar que la interfaz aparezca activa. El nombre de interfaz puede ser similar a:

```text
enp0s3
```

o

```text
enp0s8
```

### Paso 6: Configuración de proxy

Si la red no usa proxy, dejar vacío y continuar.

### Paso 7: Mirror de Ubuntu

Aceptar el mirror propuesto por defecto, salvo indicación del docente.

### Paso 8: Particionado del disco

Seleccionar:

```text
Use an entire disk
```

Luego seleccionar el disco virtual creado.

> **Importante:** Estamos trabajando sobre un disco virtual. No se modifica el disco real del equipo anfitrión.

Confirmar la escritura de cambios en disco cuando el instalador lo solicite.

### Paso 9: Crear usuario administrador

Completar los datos solicitados. Ejemplo:

```text
Your name: Estudiante UTU
Server name: ubuntu-lab
Username: estudiante
Password: una contraseña segura
```

Recomendaciones:

- Usar nombres simples, sin espacios ni tildes para hostname y usuario.
- Recordar la contraseña.
- No usar contraseñas vacías.

### Paso 10: Configurar SSH

Cuando el instalador pregunte por OpenSSH Server, seleccionar:

```text
Install OpenSSH server
```

Esto permitirá administrar el servidor remotamente desde otra terminal.

No es obligatorio importar claves SSH en esta práctica.

### Paso 11: Selección de paquetes adicionales

Si el instalador ofrece instalar paquetes o snaps adicionales, dejarlos sin seleccionar, salvo indicación del docente.

### Paso 12: Finalizar instalación

Esperar a que finalice la instalación.

Al terminar, seleccionar:

```text
Reboot Now
```

Cuando el sistema lo indique, retirar la ISO virtual si VirtualBox no lo hace automáticamente.

---

## 8. Parte 5: Primer inicio de Ubuntu Server

### Paso 1: Iniciar sesión

Cuando aparezca la consola de login, ingresar:

```text
usuario: estudiante
contraseña: la definida durante la instalación
```

### Paso 2: Actualizar el sistema

Ejecutar:

```bash
sudo apt update
sudo apt upgrade -y
```

Explicación:

- `sudo` ejecuta el comando con permisos administrativos.
- `apt update` actualiza la lista de paquetes disponibles.
- `apt upgrade -y` instala actualizaciones disponibles y responde automáticamente que sí.

### Paso 3: Instalar herramientas básicas

Ejecutar:

```bash
sudo apt install -y curl wget nano vim net-tools htop openssh-server
```

Explicación breve:

- `curl` y `wget`: descargan recursos desde la terminal.
- `nano` y `vim`: editores de texto.
- `net-tools`: incluye comandos clásicos como `ifconfig` y `netstat`.
- `htop`: monitor de procesos.
- `openssh-server`: servicio SSH para administración remota.

### Evidencia solicitada

Registrar:

- Captura del login exitoso.
- Captura de la actualización del sistema.
- Comando usado para instalar herramientas básicas.

---

## 9. Parte 6: Instalar Guest Tools / Guest Additions

En Ubuntu Server, las Guest Additions pueden requerir algunos paquetes previos.

### Paso 1: Instalar dependencias

Ejecutar:

```bash
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

Explicación:

- `build-essential`: instala herramientas de compilación.
- `dkms`: permite recompilar módulos del kernel automáticamente.
- `linux-headers-$(uname -r)`: instala encabezados del kernel actual.

### Paso 2: Insertar imagen de Guest Additions desde VirtualBox

En la ventana de la VM:

1. Ir al menú **Dispositivos**.
2. Seleccionar **Insertar imagen de CD de las Guest Additions**.

VirtualBox montará una ISO virtual dentro de la VM.

### Paso 3: Crear punto de montaje

Ejecutar:

```bash
sudo mkdir -p /mnt/cdrom
```

### Paso 4: Montar el CD virtual

Ejecutar:

```bash
sudo mount /dev/cdrom /mnt/cdrom
```

Si aparece un mensaje indicando que el medio es de solo lectura, es normal.

### Paso 5: Ejecutar instalador

Ejecutar:

```bash
cd /mnt/cdrom
sudo ./VBoxLinuxAdditions.run
```

### Paso 6: Reiniciar la VM

Ejecutar:

```bash
sudo reboot
```

### Paso 7: Verificar instalación

Luego del reinicio, ejecutar:

```bash
lsmod | grep vbox
```

Si aparecen módulos como `vboxguest`, la integración fue detectada.

### Alternativa: instalar paquetes desde repositorios

En algunos casos, especialmente en servidores, puede utilizarse:

```bash
sudo apt install -y virtualbox-guest-utils
sudo reboot
```

El docente indicará cuál método utilizar si hay problemas con la instalación desde la ISO.

### Evidencia solicitada

Registrar:

- Comando utilizado para instalar dependencias.
- Resultado de `lsmod | grep vbox`.
- Problemas encontrados, si los hubo.

---

## 10. Parte 7: Identificar la interfaz de red

Antes de configurar IP fija, necesitamos identificar el nombre real de la interfaz de red.

Ejecutar:

```bash
ip addr
```

Buscar una interfaz con nombre similar a:

```text
enp0s3
```

Ejemplo de salida simplificada:

```text
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 192.168.1.120/24
```

En este ejemplo:

- La interfaz es `enp0s3`.
- La IP actual es `192.168.1.120`.
- La máscara `/24` equivale a `255.255.255.0`.

También se puede ejecutar:

```bash
ip route
```

Ejemplo:

```text
default via 192.168.1.1 dev enp0s3
```

Esto indica que el gateway o puerta de enlace es:

```text
192.168.1.1
```

### Evidencia solicitada

Registrar:

- Nombre de la interfaz de red.
- IP obtenida inicialmente por DHCP.
- Gateway detectado.

---

## 11. Parte 8: Planificar la IP fija

Antes de modificar Netplan, completar esta tabla:

| Parámetro | Valor de ejemplo | Valor en mi VM |
|---|---:|---:|
| Interfaz | `enp0s3` |  |
| IP fija deseada | `192.168.1.50` |  |
| Máscara/CIDR | `/24` |  |
| Gateway | `192.168.1.1` |  |
| DNS primario | `1.1.1.1` |  |
| DNS secundario | `8.8.8.8` |  |

> **Advertencia:** La IP fija elegida no debe estar siendo usada por otro equipo. Si dos equipos tienen la misma IP, se produce un conflicto de red.

### Cómo elegir una IP fija razonable

1. Identificar el rango de red actual con:

```bash
ip addr
```

2. Identificar el gateway con:

```bash
ip route
```

3. Elegir una IP del mismo rango.

Ejemplo:

Si la VM recibe:

```text
192.168.1.120/24
```

y el gateway es:

```text
192.168.1.1
```

una IP fija posible podría ser:

```text
192.168.1.50/24
```

siempre que no esté ocupada.

### Comprobar si una IP responde antes de usarla

Antes de asignar una IP, probar:

```bash
ping -c 4 192.168.1.50
```

Si no responde, probablemente esté libre, aunque no es garantía absoluta.

---

## 12. Parte 9: Configurar IP fija con Netplan

### Paso 1: Ver archivos de Netplan

Ejecutar:

```bash
ls /etc/netplan/
```

Puede aparecer un archivo como:

```text
00-installer-config.yaml
```

O similar.

### Paso 2: Hacer copia de seguridad

Antes de editar, hacer una copia:

```bash
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.bak
```

Si el archivo tiene otro nombre, adaptar el comando.

### Paso 3: Editar el archivo

Abrir con nano:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

### Paso 4: Configuración de ejemplo

Reemplazar el contenido por una configuración similar a esta, adaptando los valores a la red real:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
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

> **Importante:** YAML depende de la indentación. No usar tabuladores. Usar espacios.

### Paso 5: Aplicar configuración

Ejecutar:

```bash
sudo netplan try
```

Si la configuración funciona, aceptar los cambios.

Luego ejecutar:

```bash
sudo netplan apply
```

### Paso 6: Verificar IP fija

Ejecutar:

```bash
ip addr
```

Verificar que aparezca la IP fija configurada.

Luego verificar la ruta por defecto:

```bash
ip route
```

Debe aparecer una línea similar a:

```text
default via 192.168.1.1 dev enp0s3
```

---

## 13. Parte 10: Verificar conectividad

### Prueba 1: Verificar conectividad con el gateway

Ejecutar:

```bash
ping -c 4 192.168.1.1
```

Si responde, la VM llega al router o puerta de enlace.

### Prueba 2: Verificar salida a Internet por IP

Ejecutar:

```bash
ping -c 4 1.1.1.1
```

Si responde, hay salida a Internet por IP.

### Prueba 3: Verificar resolución DNS

Ejecutar:

```bash
ping -c 4 ubuntu.com
```

Si responde, DNS funciona correctamente.

### Prueba 4: Verificar servicio SSH

Ejecutar:

```bash
systemctl status ssh
```

Si el servicio está activo, debería verse:

```text
active (running)
```

### Prueba 5: Conectarse por SSH desde el equipo anfitrión

Desde una terminal del equipo anfitrión, ejecutar:

```bash
ssh estudiante@192.168.1.50
```

Reemplazar `estudiante` y `192.168.1.50` por los datos reales de la VM.

### Evidencia solicitada

Registrar capturas o copiar la salida de:

```bash
ip addr
ip route
ping -c 4 192.168.1.1
ping -c 4 1.1.1.1
ping -c 4 ubuntu.com
systemctl status ssh
```

---

## 14. Parte 11: Instalación desatendida con VirtualBox y Ubuntu Server

Esta sección es de análisis y comparación. No es obligatorio completar una instalación desatendida funcional, salvo indicación del docente.

### 14.1. Instalación desatendida desde VirtualBox

Al crear una VM nueva y seleccionar una ISO compatible, VirtualBox puede ofrecer opciones de instalación desatendida. En ese modo, VirtualBox solicita datos antes de iniciar la instalación:

- Nombre de usuario.
- Contraseña.
- Hostname.
- Dominio.
- Instalación de Guest Additions.

Luego intenta automatizar parte del proceso.

### 14.2. Ventajas

- Ahorra tiempo.
- Reduce pasos repetitivos.
- Permite crear varias VMs similares.
- Disminuye errores de tipeo en instalaciones repetidas.

### 14.3. Desventajas

- El estudiante comprende menos decisiones internas de la instalación.
- Puede fallar según la ISO, versión de VirtualBox o sistema operativo.
- No siempre deja la configuración exactamente como se necesita.
- En servidores reales puede requerir archivos de configuración más avanzados.

### 14.4. Instalación desatendida en Ubuntu Server con Autoinstall

Ubuntu Server moderno puede automatizar instalaciones mediante **Autoinstall**, usando archivos YAML con respuestas de instalación.

Ejemplo conceptual simplificado:

```yaml
#cloud-config
autoinstall:
  version: 1
  identity:
    hostname: ubuntu-lab
    username: estudiante
    password: "$6$hash-de-la-password"
  ssh:
    install-server: true
  storage:
    layout:
      name: direct
```

Este ejemplo no debe copiarse directamente sin adaptación, porque la contraseña debe estar cifrada y la configuración completa depende del escenario.

### 14.5. Comparación entre instalación manual y desatendida

| Criterio | Instalación manual | Instalación desatendida |
|---|---|---|
| Nivel de control | Alto durante el proceso | Alto si el archivo está bien preparado |
| Dificultad inicial | Baja/media | Media/alta |
| Valor didáctico inicial | Muy alto | Medio |
| Velocidad para muchas VMs | Baja | Alta |
| Riesgo de error humano repetitivo | Mayor | Menor |
| Uso profesional | Casos puntuales | Despliegues repetitivos o masivos |

### Pregunta de reflexión

¿Por qué en una empresa o centro de datos puede ser mejor usar instalaciones desatendidas, mientras que en una primera práctica de clase conviene realizar la instalación manual?

---

## 15. Resolución de problemas frecuentes

### Problema 1: La VM no inicia desde la ISO

Verificar:

- Que la ISO esté cargada en la unidad óptica virtual.
- Que el orden de arranque tenga primero la unidad óptica.
- Que la ISO no esté corrupta.

### Problema 2: No hay conexión de red durante la instalación

Verificar:

- Que el adaptador de red esté habilitado.
- Que esté activada la opción **Cable conectado**.
- Que se haya seleccionado el adaptador físico correcto.
- Probar temporalmente NAT si bridged no funciona en la red del centro.

### Problema 3: Netplan da error al aplicar

Revisar:

- Indentación del archivo YAML.
- Nombre correcto de la interfaz.
- Que no se hayan usado tabuladores.
- Que la IP tenga formato CIDR, por ejemplo `/24`.

Comando útil:

```bash
sudo netplan generate
```

Si hay errores de sintaxis, este comando puede mostrarlos.

### Problema 4: Hay IP fija pero no Internet

Verificar:

```bash
ip route
```

Debe existir una ruta por defecto.

También revisar DNS:

```bash
resolvectl status
```

### Problema 5: No puedo conectarme por SSH

Verificar en la VM:

```bash
systemctl status ssh
```

Si no está instalado:

```bash
sudo apt install -y openssh-server
```

Si está detenido:

```bash
sudo systemctl enable --now ssh
```

---

## 16. Comandos principales utilizados

| Comando | Función |
|---|---|
| `sudo apt update` | Actualiza la lista de paquetes disponibles. |
| `sudo apt upgrade -y` | Instala actualizaciones. |
| `ip addr` | Muestra interfaces e IPs. |
| `ip route` | Muestra rutas de red. |
| `ls /etc/netplan/` | Lista archivos de configuración Netplan. |
| `sudo nano archivo` | Edita un archivo como administrador. |
| `sudo netplan try` | Prueba configuración de red temporalmente. |
| `sudo netplan apply` | Aplica configuración de red. |
| `ping -c 4 destino` | Prueba conectividad. |
| `systemctl status ssh` | Verifica estado del servicio SSH. |
| `sudo reboot` | Reinicia el sistema. |

---

## 17. Entrega de la actividad

Cada estudiante o dupla deberá entregar un informe breve en formato PDF, Markdown o documento de texto, con los siguientes apartados:

### 17.1. Datos generales

- Nombre del estudiante o integrantes.
- Grupo.
- Fecha.
- Nombre de la VM.

### 17.2. Descripción del entorno

- Versión de VirtualBox.
- Versión de Ubuntu Server instalada.
- RAM asignada.
- CPU asignadas.
- Tamaño del disco virtual.
- Tipo de red configurada.

### 17.3. Evidencias técnicas

Incluir capturas o salidas de comandos:

```bash
ip addr
ip route
ping -c 4 192.168.1.1
ping -c 4 1.1.1.1
ping -c 4 ubuntu.com
systemctl status ssh
```

### 17.4. Archivo Netplan final

Copiar el contenido final del archivo YAML utilizado.

Ejemplo:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
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

### 17.5. Problemas encontrados

Responder:

1. ¿Qué problemas aparecieron durante la instalación o configuración?
2. ¿Cómo se resolvieron?
3. ¿Qué comando fue más útil para diagnosticar la red?
4. ¿Qué diferencia hay entre DHCP e IP fija?
5. ¿Por qué el modo bridged permite que la VM se comporte como otro equipo de la red?

### 17.6. Conclusión personal

Redactar un párrafo breve respondiendo:

> ¿Qué aprendí al instalar y configurar un servidor Ubuntu en una máquina virtual?

---

## 18. Criterios de evaluación

| Criterio | Excelente | Aceptable | En proceso |
|---|---|---|---|
| Creación de VM | VM correctamente creada, recursos adecuados y configuración documentada. | VM creada con mínimos errores o poca documentación. | VM incompleta o mal configurada. |
| Instalación de Ubuntu Server | Instalación completa, usuario funcional y sistema actualizado. | Instalación funcional con alguna omisión menor. | Instalación incompleta o no funcional. |
| Configuración de red | Bridged e IP fija correctamente configurados con Netplan. | Red funcional con errores menores de documentación. | Sin conectividad o configuración incorrecta. |
| Uso de comandos | Ejecuta y comprende los comandos principales. | Ejecuta comandos, pero con explicación parcial. | Requiere asistencia permanente. |
| Evidencias | Presenta capturas, salidas y archivo Netplan completo. | Presenta evidencias parciales. | Evidencias insuficientes. |
| Análisis y reflexión | Explica problemas, soluciones y aprendizajes con claridad. | Explica parcialmente el proceso. | No logra explicar lo realizado. |
| Trabajo responsable | Trabaja con autonomía, orden y cuidado del entorno. | Trabaja con algunas intervenciones docentes. | Presenta dificultades de organización o seguimiento. |

---

## 19. Desafío opcional

Para estudiantes que finalicen antes, realizar una de las siguientes extensiones:

### Opción A: Cambiar el hostname

Ejecutar:

```bash
sudo hostnamectl set-hostname server-lab-apellido
```

Verificar:

```bash
hostnamectl
```

### Opción B: Crear un nuevo usuario

Ejecutar:

```bash
sudo adduser soporte
sudo usermod -aG sudo soporte
```

Verificar:

```bash
groups soporte
```

### Opción C: Instalar un servidor web básico

Ejecutar:

```bash
sudo apt install -y nginx
systemctl status nginx
```

Desde otro equipo de la red, probar en navegador:

```text
http://IP_DE_LA_VM
```

### Opción D: Crear una instantánea de la VM

En VirtualBox:

1. Apagar la VM.
2. Ir a **Instantáneas**.
3. Crear una instantánea llamada:

```text
Ubuntu Server instalado y configurado
```

Explicar para qué sirve una instantánea en un entorno de laboratorio.

---

## 20. Cierre de la actividad

Esta práctica permite comprender cómo se prepara un servidor GNU/Linux desde cero en un entorno seguro de virtualización. La instalación en una VM permite experimentar, equivocarse, corregir y repetir el proceso sin afectar el equipo físico.

La configuración de red con IP fija es un paso fundamental para cualquier servidor, porque permite que otros equipos lo encuentren siempre en la misma dirección. Esto es necesario para servicios como SSH, web, bases de datos, archivos compartidos, DNS, DHCP, monitoreo y administración remota.

Al finalizar, cada estudiante habrá construido un pequeño entorno de servidor que podrá reutilizarse para futuras prácticas de administración, scripting, redes, servicios y seguridad.

---

# Anexo A: Información curricular

## A.1. Propósito de la actividad

En esta actividad vamos a instalar **Ubuntu Server** dentro de una máquina virtual creada con **Oracle VirtualBox**. Luego configuraremos la máquina para que pueda comunicarse con la red externa usando el modo **Adaptador puente / Bridged Adapter** y asignaremos una **dirección IP fija** mediante **Netplan**.

El objetivo es que cada estudiante comprenda el proceso completo de instalación de un sistema operativo de servidor, desde la descarga de la imagen ISO hasta la configuración básica de red y la verificación de conectividad.

Esta práctica simula una situación real de soporte IT o administración de sistemas: preparar un servidor GNU/Linux funcional, conectado a una red local y listo para ser administrado por consola.

## A.2. Relación con competencias del MCN y del programa

Esta propuesta se alinea con el enfoque competencial del Marco Curricular Nacional, ya que moviliza saberes técnicos, procedimientos prácticos, análisis de problemas, comunicación de resultados y trabajo autónomo o colaborativo.

### Competencias generales del MCN abordadas

- **Pensamiento computacional:** al seguir procedimientos técnicos, interpretar configuraciones y resolver errores de instalación o red.
- **Pensamiento crítico:** al comparar decisiones de configuración, detectar fallas y justificar cambios.
- **Pensamiento científico:** al observar resultados, probar hipótesis y verificar conectividad mediante comandos.
- **Comunicación:** al registrar evidencias, explicar pasos realizados y documentar la configuración final.
- **Iniciativa y orientación a la acción:** al instalar, configurar, corregir y validar un sistema operativo funcional.
- **Relación con los otros:** cuando la actividad se realiza en duplas o con apoyo entre compañeros.

### Competencias tecnológicas vinculadas

Esta actividad se relaciona especialmente con:

- Instalación y configuración inicial de sistemas operativos GNU/Linux.
- Uso de máquinas virtuales como entorno de laboratorio.
- Configuración básica del sistema.
- Configuración de red.
- Uso de terminal y comandos de administración.
- Resolución de problemas básicos en entornos virtualizados.


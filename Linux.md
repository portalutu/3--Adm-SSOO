# Introducción a Linux para Administración de Sistemas Operativos

Documento de apoyo para estudiantes del curso **Administración de Sistemas Operativos**.

Este material presenta los conceptos fundamentales de Linux desde la perspectiva de la **administración de sistemas**, explicando su arquitectura, organización interna y mecanismos de gestión.

---

# 1. ¿Qué es Linux?

**Linux** es un sistema operativo tipo **Unix**, libre y de código abierto.

Fue creado en 1991 por **Linus Torvalds** como un núcleo (kernel) que posteriormente se combinó con herramientas del proyecto **GNU** para formar sistemas operativos completos.

Hoy en día Linux es ampliamente utilizado en:

* servidores
* supercomputadoras
* dispositivos embebidos
* infraestructura de Internet
* contenedores y cloud computing

Ejemplos de distribuciones Linux:

* Ubuntu
* Debian
* Fedora
* Red Hat
* Arch Linux

---

# 2. Arquitectura de Linux

La arquitectura de Linux se organiza en capas.

## 2.1 Hardware

Es la capa física del sistema:

* CPU
* memoria
* discos
* dispositivos de red

---

## 2.2 Kernel

El **kernel** es el núcleo del sistema operativo.

Sus funciones principales son:

* gestión de procesos
* gestión de memoria
* gestión de dispositivos
* control del sistema de archivos
* comunicación entre hardware y software

Linux utiliza un **kernel monolítico modular**, lo que significa que muchas funciones están dentro del kernel pero pueden cargarse como módulos.

---

## 2.3 System Libraries

Son bibliotecas que permiten a los programas interactuar con el kernel.

Ejemplo:

* glibc

---

## 2.4 User Space

Es el espacio donde se ejecutan las aplicaciones.

Incluye:

* shells
* utilidades del sistema
* aplicaciones
* servicios

---

# 3. Runlevels y modos de ejecución

En sistemas Linux tradicionales (SysV init) los **runlevels** determinan el estado operativo del sistema.

| Runlevel | Descripción          |
| -------- | -------------------- |
| 0        | Apagar el sistema    |
| 1        | Modo monousuario     |
| 2        | Multiusuario sin red |
| 3        | Multiusuario con red |
| 4        | No utilizado         |
| 5        | Modo gráfico         |
| 6        | Reinicio             |

En sistemas modernos se utiliza **systemd**, que reemplaza runlevels por **targets**.

Ejemplos:

* multi-user.target
* graphical.target

---

# 4. Usuarios en Linux

Linux es un sistema **multiusuario**.

Existen diferentes tipos de usuarios.

## 4.1 Usuario root

El usuario **root** tiene control total del sistema.

Puede:

* modificar archivos del sistema
* instalar software
* administrar usuarios

---

## 4.2 Usuarios normales

Son los usuarios que utilizan el sistema diariamente.

Ejemplo:

* estudiantes
* operadores

---

## 4.3 Usuarios de servicio

Son usuarios creados para ejecutar servicios del sistema.

Ejemplo:

* mysql
* nginx

---

# 5. Daemons

Un **daemon** es un proceso que se ejecuta en segundo plano proporcionando servicios.

Ejemplos:

| Daemon | Función            |
| ------ | ------------------ |
| sshd   | acceso remoto      |
| cron   | tareas programadas |
| httpd  | servidor web       |

Los daemons generalmente se inician cuando el sistema arranca.

---

## Comandos para gestionar daemons y procesos

Para comprender cómo funcionan los daemons es importante conocer los comandos que permiten **ver, controlar y administrar procesos del sistema**.

### ps

Muestra una lista de procesos activos.

Ejemplo:

```
ps aux
```

Explicación:

* `a` muestra procesos de todos los usuarios
* `u` muestra el propietario del proceso
* `x` muestra procesos sin terminal

Este comando permite identificar procesos como `sshd`, `cron` o `nginx`.

---

### top

Herramienta interactiva que muestra el **uso de recursos del sistema en tiempo real**.

Ejemplo:

```
top
```

Información visible:

* uso de CPU
* uso de memoria
* procesos activos
* PID de cada proceso

Es útil para detectar procesos que consumen muchos recursos.

---

### htop

Versión mejorada y visual de `top`.

Ejemplo:

```
htop
```

Características:

* interfaz más clara
* navegación con teclado
* permite finalizar procesos fácilmente

---

### kill

Permite **terminar un proceso** utilizando su PID.

Ejemplo:

```
kill 1234
```

Esto envía una señal al proceso para detenerlo.

También se puede forzar el cierre con:

```
kill -9 1234
```

---

### systemctl

En sistemas modernos con **systemd**, este comando permite gestionar servicios (daemons).

Ejemplos:

Iniciar un servicio:

```
systemctl start ssh
```

Detener un servicio:

```
systemctl stop ssh
```

Reiniciar un servicio:

```
systemctl restart ssh
```

Ver estado del servicio:

```
systemctl status ssh
```

---

### journalctl

Permite consultar los **logs generados por servicios gestionados por systemd**.

Ejemplo:

```
journalctl -u ssh
```

Esto muestra los registros del servicio SSH.

---

## Ejemplo práctico

Supongamos que queremos verificar si el servidor SSH está funcionando.

1. Verificar el servicio

```
systemctl status ssh
```

2. Ver procesos activos

```
ps aux | grep sshd
```

3. Ver consumo de recursos

```
top
```

Esto permite comprender cómo **los daemons funcionan como procesos del sistema** y cómo los administradores pueden gestionarlos.

---|---|
| sshd | acceso remoto |
| cron | tareas programadas |
| httpd | servidor web |

Los daemons generalmente se inician cuando el sistema arranca.

---

# 6. Procesos

Un **proceso** es un programa en ejecución.

Cada proceso tiene:

* PID (Process ID)
* estado
* uso de memoria

Estados comunes de procesos:

| Estado   | Descripción                |
| -------- | -------------------------- |
| Running  | ejecutándose               |
| Sleeping | esperando                  |
| Stopped  | detenido                   |
| Zombie   | terminado pero no liberado |

Comandos útiles:

* ps
* top
* htop
* kill

---

# 7. Storage Tree (Árbol de almacenamiento)

Linux organiza el sistema de archivos como un **árbol jerárquico**.

Todo comienza en el directorio raíz:

```
/
```

Desde este punto se organizan todos los dispositivos, directorios y archivos del sistema.

A continuación se muestra una vista simplificada del **árbol de almacenamiento del sistema Linux**, con una breve explicación de cada directorio principal.

```
/
├── bin        -> comandos esenciales del sistema disponibles para todos los usuarios
├── boot       -> archivos necesarios para el arranque del sistema (kernel, bootloader)
├── dev        -> archivos que representan dispositivos del sistema
├── etc        -> archivos de configuración del sistema y servicios
├── home       -> directorios personales de los usuarios
│   └── usuario -> archivos personales del usuario
├── lib        -> bibliotecas esenciales utilizadas por programas del sistema
├── lib64      -> bibliotecas para sistemas de 64 bits
├── media      -> puntos de montaje para dispositivos removibles (USB, CD)
├── mnt        -> puntos de montaje temporales utilizados por administradores
├── opt        -> software adicional instalado manualmente
├── proc       -> sistema de archivos virtual con información de procesos
├── root       -> directorio personal del usuario root
├── run        -> información temporal del sistema en ejecución
├── sbin       -> comandos administrativos del sistema
├── srv        -> datos utilizados por servicios del sistema
├── sys        -> información del hardware gestionada por el kernel
├── tmp        -> archivos temporales
├── usr        -> aplicaciones y utilidades del sistema
│   ├── bin    -> programas disponibles para usuarios
│   ├── sbin   -> utilidades administrativas
│   ├── lib    -> bibliotecas de programas
│   └── share  -> datos compartidos por aplicaciones
└── var        -> datos variables generados por el sistema
    ├── log    -> archivos de registro del sistema
    ├── spool  -> colas de impresión o correo
    └── cache  -> archivos de cache de aplicaciones
```

Este árbol representa la estructura definida por el **Filesystem Hierarchy Standard (FHS)**, que establece cómo deben organizarse los directorios en sistemas Linux.

Comprender este árbol permite a los administradores localizar rápidamente:

* configuraciones del sistema
* archivos de log
* aplicaciones instaladas
* bibliotecas del sistema
* dispositivos

---

# 8. Resource Tree

En Linux **todo es tratado como un archivo**. Esto significa que muchos recursos del sistema pueden consultarse o gestionarse a través de directorios especiales que representan información del kernel y del hardware.

Estos directorios forman lo que podemos llamar el **árbol de recursos del sistema**, ya que permiten acceder a información sobre:

* procesos
* memoria
* CPU
* dispositivos
* kernel
* hardware

La mayoría de estos directorios son **sistemas de archivos virtuales**, generados dinámicamente por el kernel.

---

## Directorios principales del Resource Tree

### /proc

Directorio virtual que contiene información sobre **procesos y estado del sistema**.

Ejemplos:

| Archivo       | Descripción                           |
| ------------- | ------------------------------------- |
| /proc/cpuinfo | información detallada del procesador  |
| /proc/meminfo | uso de memoria del sistema            |
| /proc/uptime  | tiempo que el sistema lleva encendido |
| /proc/[PID]   | información de un proceso específico  |

Nota: si existe el proceso 1200, el directorio `/proc/1200` contiene archivos con su estado, memoria utilizada y argumentos de ejecución.

---

### /sys

Sistema de archivos virtual llamado **sysfs** que expone información del hardware y del kernel.

Permite ver y en algunos casos modificar parámetros del sistema.

Ejemplos:

| Archivo            | Descripción                           |
| ------------------ | ------------------------------------- |
| /sys/class/net     | lista de interfaces de red            |
| /sys/class/thermal | sensores de temperatura               |
| /sys/block         | dispositivos de almacenamiento        |
| /sys/devices       | dispositivos detectados por el kernel |

Nota: el archivo `/sys/class/net/eth0/operstate` muestra si la interfaz de red está activa o no.

---

### /dev

Directorio que contiene **archivos de dispositivo** que representan hardware del sistema.

En Linux los dispositivos también se manejan como archivos.

Ejemplos:

| Archivo   | Descripción                    |
| --------- | ------------------------------ |
| /dev/sda  | primer disco del sistema       |
| /dev/sda1 | primera partición del disco    |
| /dev/null | dispositivo que descarta datos |
| /dev/tty  | terminal del sistema           |

Nota: escribir datos en `/dev/null` los descarta completamente.

---

### /run

Directorio que almacena información **temporal del sistema en ejecución**.

Contiene archivos generados durante el arranque del sistema.

Ejemplos:

| Archivo      | Descripción                    |
| ------------ | ------------------------------ |
| /run/systemd | estado del gestor de servicios |
| /run/utmp    | usuarios conectados al sistema |

Nota: el contenido de `/run` se pierde cuando el sistema se reinicia.

---

### /tmp

Directorio utilizado por aplicaciones para almacenar **archivos temporales**.

Ejemplos:

| Archivo          | Descripción                         |
| ---------------- | ----------------------------------- |
| /tmp/cache123    | archivos temporales de programas    |
| /tmp/install.log | registros temporales de instalación |

Nota: muchos sistemas limpian automáticamente este directorio al reiniciar.

---

### /var

Directorio que contiene **datos variables generados por el sistema y servicios**.

Ejemplos:

| Archivo           | Descripción                  |
| ----------------- | ---------------------------- |
| /var/log/syslog   | registro general del sistema |
| /var/log/auth.log | registros de autenticación   |
| /var/spool        | colas de impresión o correo  |

Nota: los administradores consultan frecuentemente `/var/log` para diagnosticar problemas.

---

### /sys/fs

Subárbol de `sysfs` que muestra información sobre **sistemas de archivos y control de almacenamiento**.

Ejemplos:

| Archivo        | Descripción                     |
| -------------- | ------------------------------- |
| /sys/fs/cgroup | control de recursos de procesos |
| /sys/fs/ext4   | información de sistemas ext4    |

Nota: los **cgroups** permiten limitar CPU y memoria de procesos o contenedores.

---

## Resumen

Estos directorios permiten a los administradores inspeccionar el estado del sistema directamente desde la línea de comandos.

Herramientas como:

* `cat`
* `ls`
* `top`
* `htop`
* `df`

leen información proveniente de estos sistemas virtuales.

Comprender el **resource tree** ayuda a entender cómo Linux expone internamente la información del sistema.

---

# 9. Carpetas principales del sistema

Carpetas principales del sistema

| Carpeta | Función                     |
| ------- | --------------------------- |
| /       | raíz del sistema            |
| /bin    | comandos básicos            |
| /boot   | archivos de arranque        |
| /etc    | configuraciones del sistema |
| /home   | directorios de usuarios     |
| /var    | datos variables             |
| /usr    | aplicaciones y librerías    |
| /tmp    | archivos temporales         |
| /dev    | dispositivos                |
| /proc   | información de procesos     |

---

# 10. Permisos de almacenamiento

Linux utiliza un sistema de permisos basado en tres niveles:

* propietario (user)
* grupo (group)
* otros (others)

Cada uno de estos niveles puede tener tres tipos de permisos.

| Permiso | Significado         |
| ------- | ------------------- |
| r       | lectura (read)      |
| w       | escritura (write)   |
| x       | ejecución (execute) |

---

## Representación simbólica

Los permisos suelen representarse con una cadena de 10 caracteres.

Ejemplo:

```
-rwxr-xr--
```

Interpretación:

| Parte | Significado              |
| ----- | ------------------------ |
| -     | tipo de archivo          |
| rwx   | permisos del propietario |
| r-x   | permisos del grupo       |
| r--   | permisos de otros        |

Esto significa:

* propietario: lectura, escritura y ejecución
* grupo: lectura y ejecución
* otros: solo lectura

---

## Valores numéricos de los permisos

Cada permiso tiene un valor numérico asociado.

| Permiso | Valor |
| ------- | ----- |
| r       | 4     |
| w       | 2     |
| x       | 1     |

Los permisos se calculan **sumando estos valores**.

Ejemplo:

| Permisos | Cálculo   | Valor |
| -------- | --------- | ----- |
| rwx      | 4 + 2 + 1 | 7     |
| rw-      | 4 + 2     | 6     |
| r-x      | 4 + 1     | 5     |
| r--      | 4         | 4     |

---

## Permisos en formato numérico (octal)

En Linux es común representar permisos con **tres números**.

Formato:

```
usuario grupo otros
```

Ejemplo:

```
754
```

Interpretación:

| Número | Permisos |
| ------ | -------- |
| 7      | rwx      |
| 5      | r-x      |
| 4      | r--      |

Por lo tanto:

* usuario: lectura, escritura, ejecución
* grupo: lectura y ejecución
* otros: solo lectura

---

## Ejemplos prácticos

### Ejemplo 1

Permisos simbólicos:

```
rwxr-xr-x
```

Cálculo:

```
rwx = 4 + 2 + 1 = 7
r-x = 4 + 1 = 5
r-x = 4 + 1 = 5
```

Resultado:

```
755
```

Este es un permiso típico para **programas ejecutables**.

---

### Ejemplo 2

Permisos simbólicos:

```
rw-r--r--
```

Cálculo:

```
rw- = 4 + 2 = 6
r-- = 4
r-- = 4
```

Resultado:

```
644
```

Este es un permiso común para **archivos de texto o configuración**.

---

### Ejemplo 3

Permisos simbólicos:

```
rwx------
```

Cálculo:

```
rwx = 7
--- = 0
--- = 0
```

Resultado:

```
700
```

Solo el propietario puede acceder al archivo.

---

## Cambio de permisos

El comando utilizado para modificar permisos es:

```
chmod
```

Ejemplos:

Permitir ejecución de un script:

```
chmod 755 script.sh
```

Archivo privado del usuario:

```
chmod 700 archivo.txt
```

---

# 11. Sistemas de archivos en Linux

Sistemas de archivos en Linux

Linux soporta diferentes sistemas de archivos.

## ext4

Sistema de archivos más utilizado.

Características:

* journaling
* buena estabilidad
* buen rendimiento

---

## XFS

Optimizado para grandes volúmenes de datos.

Características:

* alto rendimiento
* escalabilidad

---

## Btrfs

Sistema de archivos moderno con funciones avanzadas.

Características:

* snapshots
* compresión
* verificación de integridad

---

## Comparación

| Feature       | ext4 | XFS      | Btrfs |
| ------------- | ---- | -------- | ----- |
| Journaling    | Sí   | Sí       | Sí    |
| Snapshots     | No   | No       | Sí    |
| Compresión    | No   | No       | Sí    |
| Escalabilidad | Alta | Muy alta | Alta  |

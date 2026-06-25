# Guía docente — Clave de errores
## Caso de evaluación: Infraestructura Linux virtualizada

> ⚠ Uso exclusivo docente — no distribuir al alumnado

**Asignatura:** Administración de Sistemas Operativos  
**Nivel:** 3.º año  
**Total de errores:** 19 (10 de planificación + 9 de implementación)

---

## Parte A — Errores de planificación (EP)

### EP-01 — SRV-BD y SRV-WEB con DHCP

**Ubicación:** Tabla A.2, columna "Asignación de IP"  
**Error:** Con DHCP la IP puede cambiar tras cualquier reinicio; aplicaciones y clientes que dependan de esa IP dejan de funcionar.  
**Corrección:** IP estática vía Netplan en todos los servidores.

---

### EP-02 — Adaptador NAT en todas las VMs

**Ubicación:** Tabla A.2, columna "Adaptador de red"  
**Error:** En modo NAT los equipos de la LAN no pueden iniciar conexiones hacia la VM; el servidor es invisible para los clientes.  
**Corrección:** Usar **Adaptador puente (Bridged)**.

---

### EP-03 — Ubuntu Desktop para SRV-BD

**Ubicación:** Tabla A.2, SO de SRV-BD  
**Error:** Entorno gráfico consume 1-2 GB RAM extra, aumenta superficie de ataque y es innecesario en un servidor.  
**Corrección:** Ubuntu Server 22.04 LTS.

---

### EP-04 — Ubuntu 23.10 (no LTS) para SRV-WEB

**Ubicación:** Tabla A.2, SO de SRV-WEB  
**Error:** Ubuntu 23.10 tiene 9 meses de soporte. Sin actualizaciones de seguridad después de eso.  
**Corrección:** Ubuntu Server 22.04 LTS o 24.04 LTS.

---

### EP-05 — Sobreasignación de RAM (14 GB de 16 GB)

**Ubicación:** Tabla A.2 — 6+4+4 = 14 GB en host de 16 GB  
**Error:** Windows host necesita 4-6 GB. Quedan 2 GB; el host se vuelve inestable y las VMs sufren swapping.  
**Corrección:** No superar 60-70% de la RAM del host para VMs.

---

### EP-06 — Disco de 8 GB para SRV-BD

**Ubicación:** Tabla A.2, disco de SRV-BD  
**Error:** El SO ocupa 3-4 GB; quedan 4 GB para datos, logs y crecimiento. Insuficiente para producción.  
**Corrección:** Mínimo 40-50 GB (VDI dinámico: solo ocupa lo que se usa).

---

### EP-07 — VMs almacenadas en C:\

**Ubicación:** Ruta de almacenamiento en tabla A.2  
**Error:** Los VDI crecen dinámicamente; llenar C:\ colapsa Windows y corrompe las VMs.  
**Corrección:** Partición o disco separado con espacio suficiente.

---

### EP-08 — Clonar copiando el archivo .vdi

**Ubicación:** Sección A.3, paso 3  
**Error:** La copia hereda el mismo UUID; VirtualBox no puede agregar dos discos con igual UUID. Además hereda hostname y claves SSH del original.  
**Corrección:** Usar la función **Clonar** de VirtualBox (clic derecho → Clonar), que regenera UUIDs automáticamente.

---

### EP-09 — Hostname "servidor" idéntico en las tres VMs

**Ubicación:** Tabla A.2, columna Hostname  
**Error:** Tres máquinas con el mismo hostname causan conflictos de resolución de nombres en la red.  
**Corrección:** Hostnames únicos: `srv-bd`, `srv-web`, `srv-smb`.

---

### EP-10 — Sin plan de snapshots antes de producción

**Ubicación:** Sección A.5  
**Error:** Sin instantáneas no hay punto de retorno ante fallos en producción.  
**Corrección:** Crear snapshots tras instalar el SO base y tras configurar cada servicio.

---

## Parte B — Errores de implementación (EI)

### EI-01 — YAML con tabuladores en Netplan (SRV-BD, B.1)

YAML no admite tabuladores como indentación. `netplan apply` falla con error de sintaxis.  
**Corrección:** Reemplazar tabs por espacios (2 por nivel). Detectar con `cat -A archivo.yaml` (tabs aparecen como `^I`).

---

### EI-02 — Actualización del sistema con "apt download" en lugar de "apt upgrade" (SRV-BD, B.2)

`apt download` descarga el archivo .deb de un paquete específico; no actualiza los paquetes instalados del sistema. El sistema queda sin actualizaciones de seguridad aplicadas.  
**Corrección:** Usar `sudo apt upgrade` (o `sudo apt full-upgrade`) para actualizar los paquetes instalados.

---

### EI-03 — dhcp4:true con dirección estática (SRV-SMB, B.5)

Configuración contradictoria. El resultado (IP que prevalece, rutas, DNS) es impredecible.  
**Corrección:** `dhcp4: false` si se quiere IP estática.

---

### EI-04 — MySQL bind-address = 127.0.0.1 (SRV-BD, B.2)

MySQL solo acepta conexiones locales (loopback). SRV-WEB no puede conectarse a la base de datos.  
**Corrección:** Cambiar `bind-address` a `0.0.0.0` o a la IP de la interfaz. Reiniciar MySQL.

---

### EI-05 — Actualización con apt sin sudo (SRV-WEB, B.4)

`apt update` ejecutado sin `sudo` falla con error de permisos (requiere acceso root para actualizar la lista de paquetes). El resto de los comandos de instalación no contarán con la lista de repositorios actualizada.  
**Corrección:** `sudo apt update`

---

### EI-06 — Sin estrategia de backup en la planificación (A.5)

El plan no contempla ningún mecanismo de respaldo de los datos ni de los archivos de configuración. Ante una falla de disco, un ransomware o un error humano, toda la información de producción se pierde sin posibilidad de recuperación.  
**Corrección:** Definir una política de backup: qué se respalda (bases de datos, archivos compartidos, configuraciones), con qué frecuencia, en qué medio (almacenamiento externo, NAS, nube) y cómo se verifica la integridad de los respaldos.

---

### EI-07 — chmod 700 en directorio compartido (SRV-SMB, B.6)

Solo root puede acceder al directorio. Los usuarios de Samba no tienen permisos de lectura ni escritura.  
**Corrección:** `chmod 755 /srv/compartido` para acceso de invitado, o ajustar propietario al usuario de Samba.

---

### EI-08 — workgroup = WORKGROUP en Samba (SRV-SMB, B.6)

La red usa el grupo `EMPRESA`. El servidor no aparece en "Entorno de red" de los clientes Windows.  
**Corrección:** `workgroup = EMPRESA` en smb.conf. Reiniciar `smbd nmbd`.

---

### EI-09 — MySQL root sin contraseña (SRV-BD, B.2)

Cualquier usuario local puede conectarse a MySQL como root sin autenticarse. Acceso irrestricto a todos los datos.  
**Corrección:** Ejecutar `sudo mysql_secure_installation`.

---

## Tabla resumen completa

| Código | Error | Sección | Categoría |
|--------|-------|---------|-----------|
| EP-01 | SRV-BD y SRV-WEB con DHCP | A.2 | Red / IP estática |
| EP-02 | Adaptador NAT en todas las VMs | A.2 | VirtualBox / Modo de red |
| EP-03 | Ubuntu Desktop en SRV-BD | A.2 | Sistema operativo |
| EP-04 | Ubuntu 23.10 no-LTS en SRV-WEB | A.2 | Sistema operativo |
| EP-05 | 14 GB RAM para VMs en host de 16 GB | A.2 | VirtualBox / Recursos |
| EP-06 | Disco de 8 GB para SRV-BD | A.2 | VirtualBox / Recursos |
| EP-07 | VMs almacenadas en C:\ | A.1 / A.2 | VirtualBox / Almacenamiento |
| EP-08 | Clonado manual del VDI | A.3 | VirtualBox / UUIDs |
| EP-09 | Hostname idéntico en las tres VMs | A.2 | Linux / Red |
| EP-10 | Sin plan de snapshots | A.5 | VirtualBox / Proceso |
| EI-01 | YAML Netplan con tabuladores | B.1 | SRV-BD / Sintaxis |
| EI-02 | apt download en lugar de apt upgrade | B.2 | SRV-BD / Gestión de paquetes |
| EI-03 | dhcp4:true con IP estática | B.5 | SRV-SMB / Netplan |
| EI-04 | MySQL bind-address 127.0.0.1 | B.2 | SRV-BD / Configuración |
| EI-05 | apt update sin sudo | B.4 | SRV-WEB / Gestión de paquetes |
| EI-06 | Sin estrategia de backup en la planificación | A.5 | Planificación / Continuidad y recuperación |
| EI-07 | chmod 700 en carpeta compartida | B.6 | SRV-SMB / Permisos |
| EI-08 | workgroup = WORKGROUP | B.6 | SRV-SMB / Configuración |
| EI-09 | MySQL root sin contraseña | B.2 | SRV-BD / Seguridad |

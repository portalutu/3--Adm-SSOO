# Comandos principales de Linux

Este material sirve como guía rápida para practicar en la terminal. Los comandos están agrupados por categorías y cada uno incluye una explicación simple y un ejemplo de uso.

## Recomendaciones antes de empezar

- Linux distingue mayúsculas de minúsculas: "Archivo.txt" no es lo mismo que "archivo.txt".
- Escribí los comandos exactamente como aparecen.
- Si un comando no funciona, leé el mensaje de error: muchas veces indica qué corregir y como.
- Podés obtener ayuda de un comando usando "man nombre_del_comando" o "nombre_del_comando --help".

## 1. Ubicación y navegación

| Comando | Explicación | Ejemplo |
|---|---|---|
| "pwd" | Muestra la ruta de la carpeta actual. | "pwd" |
| "ls" | Lista los archivos y carpetas del lugar actual. | "ls" |
| "ls -l" | Muestra la lista en formato detallado. | "ls -l" |
| "ls -a" | Muestra también los archivos ocultos. | "ls -a" |
| "cd carpeta" | Entra en una carpeta. | "cd Documentos" |
| "cd .." | Sube un nivel en la estructura de carpetas. | "cd .." |
| "cd ~" | Vuelve al directorio personal del usuario. | "cd ~" |
| "tree" | Muestra carpetas y subcarpetas en forma de árbol. | "tree" |

## 2. Crear archivos y directorios

| Comando | Explicación | Ejemplo |
|---|---|---|
| "mkdir nombre" | Crea una carpeta nueva. | "mkdir practicas" |
| "mkdir -p ruta" | Crea varias carpetas en una ruta, incluso si faltan intermedias. | "mkdir -p curso/linux/comandos" |
| "touch archivo" | Crea un archivo vacío o actualiza su fecha. | "touch notas.txt" |
| "cp origen destino" | Copia un archivo. | "cp notas.txt respaldo.txt" |
| "cp -r origen destino" | Copia una carpeta completa. | "cp -r practicas practicas_backup" |
| "mv origen destino" | Mueve o renombra archivos y carpetas. | "mv notas.txt apuntes.txt" |
| "rm archivo" | Elimina un archivo. | "rm apuntes.txt" |
| "rm -r carpeta" | Elimina una carpeta y su contenido. | "rm -r practicas_backup" |
| "rmdir carpeta" | Elimina una carpeta vacía. | "rmdir carpeta_vacia" |

## 3. Ver contenido de archivos

| Comando | Explicación | Ejemplo |
|---|---|---|
| "cat archivo" | Muestra el contenido completo de un archivo. | "cat notas.txt" |
| "less archivo" | Permite leer un archivo largo por partes. | "less /etc/services" |
| "head archivo" | Muestra las primeras 10 líneas. | "head notas.txt" |
| "head -n 5 archivo" | Muestra una cantidad específica de líneas iniciales. | "head -n 5 notas.txt" |
| "tail archivo" | Muestra las últimas 10 líneas. | "tail notas.txt" |
| "tail -n 20 archivo" | Muestra una cantidad específica de líneas finales. | "tail -n 20 notas.txt" |
| "nl archivo" | Muestra el contenido con números de línea. | "nl notas.txt" |

## 4. Buscar información

| Comando | Explicación | Ejemplo |
|---|---|---|
| "find ruta -name "nombre"" | Busca archivos o carpetas por nombre. | "find . -name "notas.txt"" |
| "grep texto archivo" | Busca una palabra o frase dentro de un archivo. | "grep Linux notas.txt" |
| "grep -i texto archivo" | Busca sin distinguir mayúsculas y minúsculas. | "grep -i linux notas.txt" |
| "grep -r texto carpeta" | Busca texto dentro de varios archivos de una carpeta. | "grep -r contraseña Documentos" |
| "which comando" | Indica dónde está instalado un comando. | "which bash" |
| "history" | Muestra el historial de comandos usados. | "history" |

## 5. Permisos y propietarios

| Comando | Explicación | Ejemplo |
|---|---|---|
| "ls -l" | Muestra permisos, propietario y grupo de archivos. | "ls -l" |
| "chmod permisos archivo" | Cambia los permisos de un archivo o carpeta. | "chmod 644 notas.txt" |
| "chmod +x archivo" | Da permiso de ejecución. | "chmod +x script.sh" |
| "chown usuario archivo" | Cambia el propietario de un archivo. | "sudo chown alumno notas.txt" |
| "chgrp grupo archivo" | Cambia el grupo de un archivo. | "sudo chgrp estudiantes notas.txt" |

## 6. Procesos y sistema

| Comando | Explicación | Ejemplo |
|---|---|---|
| "ps" | Muestra los procesos activos de la sesión actual. | "ps" |
| "ps aux" | Muestra todos los procesos del sistema. | "ps aux" |
| "top" | Muestra procesos en tiempo real. | "top" |
| "htop" | Versión más visual de "top" si está instalada. | "htop" |
| "kill PID" | Finaliza un proceso según su número de identificación. | "kill 2451" |
| "kill -9 PID" | Fuerza el cierre de un proceso. | "kill -9 2451" |
| "uname -a" | Muestra información del sistema operativo. | "uname -a" |
| "date" | Muestra la fecha y hora del sistema. | "date" |
| "uptime" | Indica cuánto tiempo lleva encendido el sistema. | "uptime" |

## 7. Usuarios y sesión

| Comando | Explicación | Ejemplo |
|---|---|---|
| "whoami" | Muestra el usuario actual. | "whoami" |
| "id" | Muestra información del usuario y sus grupos. | "id" |
| "who" | Muestra qué usuarios están conectados. | "who" |
| "passwd" | Permite cambiar la contraseña del usuario actual. | "passwd" |
| "sudo comando" | Ejecuta un comando con permisos de administrador. | "sudo apt update" |
| "su - usuario" | Cambia a otro usuario iniciando su entorno. | "su - root" |

## 8. Red y conectividad

| Comando | Explicación | Ejemplo |
|---|---|---|
| "ip a" | Muestra las direcciones IP de la máquina. | "ip a" |
| "ping host" | Comprueba si hay conexión con otro equipo o sitio web. | "ping google.com" |
| "ssh usuario@equipo" | Se conecta de forma remota a otro equipo. | "ssh alumno@192.168.1.20" |
| "scp origen destino" | Copia archivos entre equipos por red. | "scp notas.txt alumno@192.168.1.20:/home/alumno/" |
| "curl url" | Descarga o consulta contenido desde una URL. | "curl https://example.com" |
| "wget url" | Descarga un archivo desde internet. | "wget https://example.com/archivo.zip" |

## 9. Paquetes y software

| Comando | Explicación | Ejemplo |
|---|---|---|
| "apt update" | Actualiza la lista de paquetes disponibles. | "sudo apt update" |
| "apt upgrade" | Instala actualizaciones de paquetes ya instalados. | "sudo apt upgrade" |
| "apt install paquete" | Instala un programa. | "sudo apt install htop" |
| "apt remove paquete" | Desinstala un programa. | "sudo apt remove htop" |
| "apt search palabra" | Busca paquetes disponibles. | "apt search editor" |

## 10. Compresión y archivos empaquetados

| Comando | Explicación | Ejemplo |
|---|---|---|
| "tar -cvf archivo.tar carpeta" | Empaqueta archivos o carpetas en un solo archivo ".tar". | "tar -cvf practicas.tar practicas" |
| "tar -xvf archivo.tar" | Extrae un archivo ".tar". | "tar -xvf practicas.tar" |
| "tar -czvf archivo.tar.gz carpeta" | Empaqueta y comprime en formato ".tar.gz". | "tar -czvf practicas.tar.gz practicas" |
| "tar -xzvf archivo.tar.gz" | Extrae un archivo ".tar.gz". | "tar -xzvf practicas.tar.gz" |
| "zip archivo.zip archivo" | Comprime en formato ".zip". | "zip documentos.zip notas.txt" |
| "unzip archivo.zip" | Extrae un archivo ".zip". | "unzip documentos.zip" |

## 11. Redirecciones y tuberías

| Comando | Explicación | Ejemplo |
|---|---|---|
| ">" | Guarda la salida de un comando en un archivo y lo reemplaza si existe. | "ls > lista.txt" |
| ">>" | Agrega la salida al final de un archivo. | "date >> registro.txt" |
| "|" | Envía la salida de un comando como entrada de otro. | "ls -l | less" |
| "echo texto" | Muestra un texto en pantalla. | "echo Hola" |
| "echo texto > archivo" | Escribe texto en un archivo. | "echo "Práctica 1" > notas.txt" |

## 12. Ayuda y documentación

| Comando | Explicación | Ejemplo |
|---|---|---|
| "man comando" | Abre el manual de un comando. | "man ls" |
| "comando --help" | Muestra ayuda rápida del comando. | "ls --help" |
| "info comando" | Muestra documentación más extensa en algunos casos. | "info grep" |

## Ejemplos de prácticas simples

### Práctica 1: crear una carpeta y un archivo

"""bash
mkdir practica_terminal
cd practica_terminal
touch ejercicio1.txt
ls -l
"""

### Práctica 2: escribir y leer contenido

"""bash
echo "Estoy aprendiendo comandos de Linux" > ejercicio1.txt
cat ejercicio1.txt
"""

### Práctica 3: copiar, mover y borrar

"""bash
cp ejercicio1.txt copia.txt
mv copia.txt respaldo.txt
rm respaldo.txt
"""

### Práctica 4: buscar texto en un archivo

"""bash
echo "Linux es un sistema operativo" > sistema.txt
grep Linux sistema.txt
"""

## Buenas prácticas en la terminal

- Revisá bien el comando antes de presionar Enter.
- Tené cuidado con "rm" y especialmente con "rm -r", porque eliminan contenido.
- Usá "ls" y "pwd" con frecuencia para saber dónde estás trabajando.
- Si necesitás ayuda, probá primero con "man" o "--help".

## Comandos para recordar

Si recién empezás, estos son los más importantes para memorizar primero:

"pwd", "ls", "cd", "mkdir", "touch", "cp", "mv", "rm", "cat", "grep", "chmod", "ps", "top", "ping", "ssh", "man"

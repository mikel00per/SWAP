# Practica 2 | Clonar la información de un sitio web
En esta practica veremos como 
1. Aprender a copiar archivos mediante ssh
2. Clonar contenido entre máquinas
3. Configurar el ssh para acceder a máquinas remotas sin contraseña
4. Establecer tareas en cron

Esto será util para mantener actualizada la máquina 2 con la información proveniente 
de la máqina 1 gracias a la orden rysnc y cron, haciendo que se actualice cada
cierto tiempo manteniendo sí la integridad. 

## Copiar Archivos por ssh
Una de las formas de sincronizar informaciónentre dos máquinas sería enviar por ssh 
de una maquina a otra, en un comprimido, aquello que deseemos, para ello usaremos la 
orden:

    tar czf - directorio | ssh equipodestino 'cat > ~/tar.tgz'

Aquí está un ejemplo, recordar que la ip de las máquinas son 192.168.1.111 y 192.168.1.222,
respectivamente. 

![tar-ssh](https://github.com/mikel00per/SWAP/blob/master/Practica%202/comprimido-ssh.png)

Esto no sirve para mover grandes volúmenes de información por lo que será necesario usar 
la orden rsync que se usa en entornos reales. 

## Clonando contenido entre máquinas
La otra forma de copiar grandes volúmenes de información es usando rsync. Si no tenemos
instalada la herramienta unicamente ejecutar: 

    sudo apt-get install rsync
    
Tras esto unicamente tendremos que usar la orden siguiente para mantener actualizado el
directorio de nuestro servidor entre la máquina1 y la máquina2. Evidentemente este comando
será usado en la máquina2.

    rsync -avz -e ssh ipmaquina1:/var/www/ /var/www/ 
    
Un ejemplo real de su uso es se muestra en la imagen siguiente

![rsync](https://github.com/mikel00per/SWAP/blob/master/Practica%202/rsync_wwwhttp.png)

Como podemos ver nos pide contraseña para acceder y el arvhivo Arvhivo_Maquina_1.html y
los demás se transfieren. Es un inconveniente estar continuamente introduciendo
la contraseña así que debemos configurar el ssh para poder usar rysic de forma 
automática.

## Configuración del ssh 
Los pasos son realmente simples, unicamente debemos introducir los siquientes comandos.

    ssh-keygen -b 4096 -t rsa # Generará dos archivos en el directorio ./ssh llamados
                              # id_rsa y id_rsa.pub. Por defecto no introducir nada
                              # solo pulsar enter.
                              
Tras esto copiamos la ID de la maquina a la que queremos conectarnos automáticamente 
(recordemos que mi máquina1 tiene la ip 192.168.1.111)

    ssh-copy-id ipmaquina1
    
Una vez hecho esto la siguiente vez que hagamos ssh a la ip de la máquina1 no pedirá su
contraseña pues la recordará. Aqui una imagen de un ejemplo real, el primer comando no se
aprecia pero es el sh-keygen.

![tar-ssh](https://github.com/mikel00per/SWAP/blob/master/Practica%202/shh-id_conexion-sin-contrase%C3%B1a.png)


## Cron y Rsync 
Por último veremos como programar tareas con cron. Para hacerlo tendremos que editar el archivo
cron o bien de nuestro usuario o bien el cron del sistema. 












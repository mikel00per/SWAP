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

Para programar tareas para nuestro usuario deberemos usar el comando

    crontab -l

El formato para indicar el tiempo en el que se debe ejecutar una tarea el siguiente:

    # m h dom mon dow user command
    #
    # m corresponde al minuto en que se va a ejecutar el script, el valor va de 0 a 59
    # h la hora exacta, se maneja el formato de 24 horas, los valores van de 0 a 23, siendo 0 las 12:00 de la medianoche.
    # hace referencia al día del mes, por ejemplo se puede especificar 15 si se quiere ejecutar cada dia 15
    # dow significa el día de la semana, puede ser numérico (0 a 7, donde 0 y 7 son domingo) o las 3 primeras letras del día en inglés: mon, tue, wed, thu, fri, sat, sun
    # define el usuario que va a ejecutar el comando, puede ser root, u otro usuario diferente siempre y cuando tenga permisos de ejecución del script.
    # refiere al comando o a la ruta absoluta del script a ejecutar, ejemplo: /home/usuario/scripts/actualizar.sh, si acaso llama a un script este debe ser ejecutable

**Importante:** el '\*'  indicará el valor "todo". Así, un * en el campo de minuto indicará todos los minutos de la hora.

Si queremos programar tareas para el el sistema en general y no para nuestro usuario
sería conveniente añadirlo en el archivo /etc/crontab

    sudo nano /etc/crontab

Tras modificar los archivos sería convniente reiniciar el servicio o reiniciar el sistema

    sudo /etc/init.d/crontab restart
    ó
    reboot

Un ejemplo real sería mantener actualizado el contenido de un directorio con rysnc entre
dos máquinas. Como la conexion ssh ya no pide contraseña si nos conectados desde la
máquina 2 a la máquina 1 podremos automatizar la tarea. Aquí lo muestro.

![rysnc-cron](https://github.com/mikel00per/SWAP/blob/master/Practica%202/Archivo-cront-editado.png)

*Nota:* La orden superior a la de rsync añade cada minuto el comentario *Practica2!!* en un fichero.

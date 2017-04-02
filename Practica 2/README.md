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

![tar-ssh](https://github.com/mikel00per/SWAP/blob/master/Practica%201/interfacespng.png)

Esto no sirve para mover grandes volúmenes de información por lo que será necesario usar 
la orden rsync que se usa en entornos reales. 

![](http://www.ubuntugeek.com/step-by-step-ubuntu-12-04-precise-lamp-server-setup.html)

## Cambiar IP máquinas servidoras.

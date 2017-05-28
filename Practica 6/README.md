# Practica 6 | Discos en RAID
En esta práctica configuraremos dos discos en RAID 1 por software, usando una
maquina virtual con Ubuntu server. Esta configuración RAID ofrece una gran
seguridad al replicar los datos en los dos discos.

Los objetivos concretos de esta práctica son:
1. Configurar dos discos en RAID 1. Los discos se añadirán a un sistema ya
instalado y funcionando, de forma que en total tendremos tres discos.
2. Hacer pruebas de retirar y añadir un disco “en caliente”, y comprobar que el
RAID sigue funcionando correctamente.

## Añadiendo dos discos nuevos a la máquina
Lo primero que debemos hacer es añadir a nuestra máquina virtual dos nuevos
discos. Dependiendo del software de virtualización se hará de una forma o de
otra. En todo caso la máquina deberá estar apagada.

En virtualbox simplemente tendremos que:

      Selecionar máquina -> Configuración -> Almacenamiento


![discos-nuevos](https://github.com/mikel00per/SWAP/blob/master/Practica%206/nuevos-discos.png)

Tras esto hacer click como en la imagen y configurar el tamaño y tipo de disco
en mi caso he dejado el tipo y tam por defecto.

## Configurando el RAID por software.
Una vez añadidos los disco hay que iniciar la máquina y descargar la herramienta
mdadm

    sudo apt install mdadm

Tras instalarla deberemos de crear el RAID 1 además les daremos formato:

    sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc
    sudo mkfs /dev/md0

![creando-raid](https://github.com/mikel00per/SWAP/blob/master/Practica%206/creando-raid.png)

Por defecto se establecerá a ext2. Ahora creo un directorio para poder montar
los discos. Para ello:

    sudo mkdir /dat
    sudo mount /dev/md0 /dat

Para saber si se ha realizado correctamente

    sudo mount

![estado-md0](https://github.com/mikel00per/SWAP/blob/master/Practica%206/estado-md0.png)

Además para ver el estado real usamos

    sudo mdadm --detail /dev/md0

![estado-mdad](https://github.com/mikel00per/SWAP/blob/master/Practica%206/estado-mdad.png)

Para hacer que el disco se monte automaticamente lo primero que debemos hacer
es copiar el UUID del disco para ello listamos /dev/disk/by-uuid/ tras esto
modificamos el archivo /etc/fstab sigiendo la estructura:

    UUID=ccbbbbcc-dddd-eeee-ffff-aaabbbcccddd /dat ext2 defaults 0 0
    ls -l /dev/disk/by-uuid/
    sudo nano /etc/fstab

![fstab](https://github.com/mikel00per/SWAP/blob/master/Practica%206/fstab.png)

Tras esto es recomendable reiniciar para ver si hay algún error.

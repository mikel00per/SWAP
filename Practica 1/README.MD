#Practica 1 | Preparación de herramientas

En esta practica hemos creado dos máquinas virtuales con ubuntu server. Además
he podido instalar LAMP sin necesidad de usar apt-get, en el instalador ya
aparecía la opción. El proceso ha sido realmente sencillo.

## Pasos realizados:

1. Descarga e instalación de VirtualBox, gracias al SO Manjaro unicamente he
tenido que hacer 'yaourt virtual-box' he instalar las dependencias opcionales.

2. Creación de las dos máquinas virtuales. Ambas creadas en base a los
iso's aportados por el profesor. Unicamente darle a Nueva máquina, y
siguiente, siguiente. Tras la creación de esta he ido a Configuración -> Almacenamiento
-> Controlador -> *Pinchas en el disco vacío* -> **Seleccionas la ISO del
profesor** y finalmente iniciamos la máquina por primera vez.

3. A continuación solo he instalado ubuntu como el tutorial aportado recomienda  [aquí](http://www.ubuntugeek.com/step-by-step-ubuntu-12-04-precise-lamp-server-setup.html)
lo único que no hecho ha sido poner la ip estática.

4. Una vez instalado el SO he instalado el curl con el comando: sudo apt-get
install curl, a diferencia de otras páginas que decía de usar el mismo comando
pero con php5-curl. Además he probado el comando usando la ip de la máquina. Para
ver la ip he usado ipconfig.

5. He creado el fichero hola.html pero en ver de ponerlo en la ruta _/var/www_ ha
sido en _/var/www/html_ para que así el comando curl http://120.0.2.15/hola.html funcione
mostrando una salida como esta:

![curl](https://github.com/M-M-W/SWAP/blob/master/Practica%201/curl.png)

6. Por último he ejecutado los comando descritos en el guión, apache2 -v y
ps aux | grep apache.

![apache_rules](https://github.com/M-M-W/SWAP/blob/master/Practica%201/Practica1.png)


**NOTA:** No he cambiado los tipos de red de las máquinas ya que no se decía nada de ello en ningún momento, por defecto he usado NAT. 

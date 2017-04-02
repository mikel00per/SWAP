# Practica 1 | Preparación de herramientas

En esta práctica hemos creado dos máquinas virtuales con Ubuntu Server para
poder usarlas como máquinas servidores a las que posteriormente
le añadiremos un  balanceador de carga.

## Pasos realizados:

1. Descargar e instalar VirtualBox. Según la distribunción se hará de una
forma u otra, en mi caso unicamente he tenido que ejecutar pacman -S virtual-box
además he instalado las dependencias opcionales.

2. Creación de las dos máquinas virtuales. Ambas creadas en base a los
iso's aportados por el profesor. Unicamente darle a Nueva máquina, y
usar las opciones predeterminadas. Tras la creación de esta he ido a Configuración -> Almacenamiento -> Controlador -> *Pinchas en el disco vacío* -> **Seleccionas la ISO del profesor** y finalmente iniciamos la máquina por primera vez.

3. A continuación solo he instalado ubuntu como el tutorial aportado recomienda  [aquí](http://www.ubuntugeek.com/step-by-step-ubuntu-12-04-precise-lamp-server-setup.html)

4. Una vez instalado el SO he instalado el curl con el comando: sudo apt-get
install curl, a diferencia de otras páginas que decía de usar el mismo comando
pero con php5-curl. Además he probado el comando usando la ip de la máquina. Para
ver la ip he usado ipconfig.

5. Para hacer que las máquinas se vean será necesario añadir un nuevo adaptador
de red a nuestras dos máqinas, deben de ser de tipo Red Interna y permitir MV.
Posteriormente tendremos que añadir la interfaz y configurar la ip como
estática. Modificamos el archivo /etc/network/interfaces con el editor que más
nos guste. Lo dejamos como en la imagen

![curl]()

6. Para comprobar si se ven las máquinas virtuales crearemos un archivo, hola.html
en el directorio "/var/www/html/". Tras esto ejecutaremos el comando como vemos
en la imagen. 

![curl](https://github.com/M-M-W/SWAP/blob/master/Practica%201/curl.png)

6. Por último he ejecutado los comando descritos en el guión, apache2 -v y
ps aux | grep apache.

![apache_rules](https://github.com/M-M-W/SWAP/blob/master/Practica%201/Practica1.png)


**NOTA:** No he cambiado los tipos de red de las máquinas ya que no se decía nada de ello en ningún momento, por defecto he usado NAT.

# Practica 1 | Preparación de herramientas

En esta práctica hemos creado dos máquinas virtuales con Ubuntu Server para
poder usarlas como máquinas servidores a las que posteriormente
le añadiremos un  balanceador de carga.

## Instalación Ubuntu y VirtualBox

Lo primeero es descargar e instalar VirtualBox. Según la distribunción se hará de una
forma u otra, en mi caso unicamente he tenido que ejecutar pacman -S virtual-box
además he instalado las dependencias opcionales.

Tras esto crear dos máquinas virtuales. Ambas creadas en base a los iso's aportados 
por el profesor. Unicamente darle a Nueva máquina, y usar las opciones predeterminadas. 
Tras la creación de estas hay que ir a Configuración -> Almacenamiento -> Controlador -> *Pinchas en el disco vacío* -> **Seleccionas la ISO del profesor** y finalmente iniciamos la máquina por primera vez.

A continuación solo he instalado ubuntu como el tutorial aportado recomienda [aquí](http://www.ubuntugeek.com/step-by-step-ubuntu-12-04-precise-lamp-server-setup.html)

## Cambiar IP máquinas servidoras. 
Para poder acceder a posteriori a las máquinas a través de sus ips lo que hay que hacer es necesario 
añadir un nuevo adaptador de red a nuestras dos máqinas, deben de ser de tipo Red Interna y permitir MV.
Posteriormente tendremos que añadir la interfaz de red y configurar la ip como estática. Modificamos el 
archivo /etc/network/interfaces con el editor que másnos guste. Lo dejamos como en la imagen:

![curl](https://github.com/mikel00per/SWAP/blob/master/Practica%201/interfacespng.png)

**NOTA:** La máquina 1 tendrá la ip 192.168.1.111 y la dos 192.168.1.222. Seguramente si el
adaptador 1 es una NAT perderemos el intertnet por lo que habrá que poner en el adaptador 1
Red NAT y recargar la dirección MAC aunque no será necesario tener internet. 

![curl](https://github.com/mikel00per/SWAP/blob/master/Practica%201/ifconfig.png)

Una vez instalado el SO hay hay que instalar cURL con el comando: sudo apt-get install curl. Así
podremos hacer acceder a la dirección ip que hemos configurado anteriormente. Lo único que deberemos
hacer es crear un archivo, hola.html en el directorio "/var/www/html/". Tras esto ejecutar el comando como vemos
en la imagen. 

![curl](https://github.com/mikel00per/SWAP/blob/master/Practica%201/ipscambiadas_y_cur.png)

## Estado del servidor apache. 
Por último para saber el estado del servidor apache he ejecutado: apache2 -v y ps aux | grep apache.

![apache_rules](https://github.com/M-M-W/SWAP/blob/master/Practica%201/apache-status)


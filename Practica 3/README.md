# Practica 3 | Balanceo de carga
En esta práctica configuraremos una red entre varias máquinas de forma que
tengamos un balanceador que reparta la carga entre varios servidores finales.

El problema a solucionar es la sobrecarga de los servidores. Se puede balancear
cualquier protocolo, pero dado que esta asignatura se centra en las tecnologías web,
balancearemos los servidores HTTP que tenemos configurados. De esta forma
conseguiremos una infraestructura redundante y de alta disponibilidad.

## Crear máquina balanceadora.
Lo primero que debemos hacer es crear una nueva máquina virtual que servirá
para balancear la carga entre nuestras dos máquinas servidoras. Para ello
deberemos seguir el tutorial que se ve en el [guion uno](https://github.com/mikel00per/SWAP/tree/master/Practica%201)
a diferencia de que ya no queremos un sevidor LAMP por lo que unicamente necesitaremos
instalar el ssh, las librerias comunes de linux y DNS (esto cuando instalemos
ubuntu en nuestro disco virtual).

**Nota:** Será necesario que esta máquina tenga red interna y conexion a internet
para instalar las herramientas que necesitemos más adelante.

**Nota 2:** Es muy importante añadir la interfaz de red interna enp0s8 al archivo
/etc/network/interfaces

## Instalación de nginx
Para instalarlo lo haremos por el protocolo normal en Ubuntu:

    sudo apt-get update && sudo apt-get upgrade &&
    sudo apt-get install nginx

**Nota**: Es importante que el puerto 80 esté libre por eso no deberemos tener
ningún programa o proceso que esté haciendo uso de él. Es por ello que no hemos
instalado LAMP en esta máquina, aparte de no ser necesario.

## Configurando nginx
Para configuar nginx tendremos que modificar el contenido de /etc/nginx/conf.d/default.conf
eliminandolo y poner lo que citaré a continuación. Puede que el archivo default.conf
no exista, por lo que deberemos crearlo.

    touch /etc/nginx/conf.d/default.conf

Hay que tener en cuenta que fuera de conf.d encontramos nginx.conf que integra las
configuraciones del directorio conf.d

Editamos pues el contenido de /etc/nginx/conf.d/default.conf con lo siguiente:

    upstream apaches {
      server xxx.xxx.x.xxx; # Máquina 1
      server xxx.xxx.x.xxx; # Máquina 2
    }

    server{
      listen 80;
      server_name balanceador;
      access_log /var/log/nginx/balanceador.access.log;
      error_log /var/log/nginx/balanceador.error.log;
      root /var/www/;
      location /
      {
        proxy_pass http://apaches;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
      }
    }

En mi caso la ip del server serán 192.168.1.111 y 192.168.1.222, un ejemplo real
de esto es el siguiente:

![config-nginx](https://github.com/mikel00per/SWAP/blob/master/Practica%203/configuracion_nginx.png)

Para probar que esto funciona deberemos de usar el comando de cURL hacia nuestras
máquinas para ver si estas se están viendo.

    curl http://ip-maquina
    ó
    curl ip-maquina

Tras esto se mostrará el index.html de cada máquina. En mi caso personal he editado
ambos index para que se aprecie la diferencia al hacer el curl.

![cURL-nexinx](https://github.com/mikel00per/SWAP/blob/master/Practica%203/cURL-neginx.png)

### Otras opciones para nginx
Podremos añadir diferentes características a nginx. Si queremos que las máquinas
soporte más carga de trabajo usaremos una configuración como esta:

    # Cada 3 peticiones la máquina 1 soporta una y la 2, dos.
    upstream apaches {
      server 172.16.168.130 weight=1;
      server 172.16.168.131 weight=2;
    }

Para hacer que la misma máquina soporte las peticiones provenientes de otra y
no cambie de servidor, deberemos ajustarlo ya que sino podrían darse errores.

    upstream apaches {
      ip_hash;
      server 172.16.168.130;
      server 172.16.168.131;
    }

Para realizar una conexion con persistencia de múltiples peticiones HTTP en lugar
de abrir una conexión nueva cada vez deberemos de usar:

    upstream apaches {
      server 172.16.168.130;
      server 172.16.168.131;
      keepalive 3;
    }

Entre otras muchas opciones que podemos encontrar en el guión del pdf.

En mi entorno la máquina 1 ha de recibir el doble de las peticiones que la
máquina dos por lo que la configuración deberemos establecer el weight a 2 y 1
respectivamente.

![config-nginx2](https://github.com/mikel00per/SWAP/blob/master/Practica%203/configuracion_nginx-2.png)

Es muy importante eliminar el archivo /etc/nginx/sites-enables/default ya
que sino continuamente nos aparecerá la página de inicio de nginx

    sudo rm /etc/nginx/sites-enables/default

Aquí un ejemplo real de como el balanceador distribuye las peticiones entre
la máquina1 y máquina2, en ambas existen dos archivos distintos con distintos
mensajes.

![prueba-nginx](https://github.com/mikel00per/SWAP/blob/master/Practica%203/prueba-nginx-weight.png)

## Instalación de haproxy
Para instalarlo lo haremos por el protocolo normal en Ubuntu:

    sudo apt-get update && sudo apt-get upgrade &&
    sudo apt-get install nginx

Es importante tener en cuenta que es necesario parar el servicio de nginx si
lo tenemos instalado o cualquier proceso que ocupe el puerto 80.

    sudo /etc/init.d/nginx stop

## Configurando haproxy
Una vez instalado haproxy pasamos a configurarlo, para hacerlo debemos editar
el archivo /etc/haproxy/haproxy.cfg

    sudo nano /etc/haproxy/haproxy.cfg

Que deberemos borrar y añadir la siguiente configuración que es más simple,
además como podemos observar indicamos explicitamente el puerto que queremos
usar, en este caso el 80, que es el de por defecto

    global
      daemon
      maxconn 256

    defaults
      mode http
      timeout connect 4000
      timeout client 42000
      timeout server 43000

    frontend http-in
      bind \*:80
      default_backend servers

    backend servers
      server m1 xxx.xxx.x.xxx:80 maxconn 32
      server m2 xxx.xxx.x.xxx:80 maxconn 32

Aquí un ejemplo de como lo he configurado en mi máquina balanceadora

![confi-haproxy](https://github.com/mikel00per/SWAP/blob/master/Practica%203/confi-haproxy.png)

Una vez modificado nuestro archivo de configuración deberemos reiniciar el
servicio cargando la configuración nueva con la siguiente orden.

    sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg

Para probar que nuestra configuración funciona correctamente crearemos unos
ficheros en ambos servidores llamado haproxy, exatamente como la prueba del
nginx, tras esto haremos curl a nuestra máquina balanceadora para ver si
funciona.

    curl http://ipMaquinaBalanceadora/haproxy.html

![prueba-haproxy](http://github.com/mikel00per/SWAP/blob/master/Practica%203/confin-haproxy.png)

## Probando a fondo nuestro balanceador
Para probar nuestro balanceador vamos a usar Apache Benchmark desde otra máquina
nueva que hará peticiones a nuestro balanceador y él se encargará de dirigirlas
a un lugar u otro. Para ello usaremos nginx ya que lo hemos configurado
ligeramente más.

Para la nueva máquina deberemos instalar Apache ya que la herramienta (comando)
ab (Apache Benchmark) viene dentro de este. He creado la máquina de la misma
forma que describo en la practica 1.

Una vez tengamos la máquina y configurada la red interna deberemos usar el
comando ab de la siguiente forma:

    ab -n 1000 -c 10 http://ipMaquinaBalanceadora/<archivo>.html

    -n indica el número de peticiones
    -c indica que las peticiones se harán concurrentemente, de 10 en 10


Aquí un ejemplo de la ejecución del comando en el entorno personal que he
estado describiendo hasta ahora. El comando ejecutado ha sido

    ab -g resultado.csv -n 1000 -c 10 http://192.168.1.233 > ab-text.txt

Así podré consultar los resultados desde el fichero de texto ya que por terminal
se pierde, además podremos generar un csv para poder generar una grafica con los
resultado.

![prueba-nginx](https://github.com/mikel00per/SWAP/blob/master/Practica%203/pruebaABngix1.png)
![prueba-nginx2](https://github.com/mikel00per/SWAP/blob/master/Practica%203/pruebaABngix2.png)

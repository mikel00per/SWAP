# Practica 3 | Balanceo de carga
En esta práctica configuraremos una red entre varias máquinas de forma que
tengamos un balanceador que reparta la carga entre varios servidores finales.

El problema a solucionar es la sobrecarga de los servidores. Se puede balancear
cualquier protocolo, pero dado que esta asignatura se centra en las tecnologías web,
balancearemos los servidores HTTP que tenemos configurados.

De esta forma conseguiremos una infraestructura redundante y de alta disponibilidad.

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
respectivamente. Además he sumado en una las configuraciones citadas hasta el
momento.

![config-nginx2](https://github.com/mikel00per/SWAP/blob/master/Practica%203/confinguracion-nginx-2.png)

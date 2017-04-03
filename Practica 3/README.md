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
deberemos seguir el tutorial que se ve en el [guion uno](http://www.ubuntugeek.com/step-by-step-ubuntu-12-04-precise-lamp-server-setup.html)
a diferencia de que ya no queremos un sevidor LAMP por lo que unicamente necesitaremos
instalar el ssh, las librerias comunes de linux y DNS (esto cuando instalemos
ubuntu en nuestro disco virtual).

**Nota:** Será necesario que esta máquina tenga red interna y conexion a internet
para instalar las herramientas que necesitemos más adelante.

## Instalación de nginx
Para instalarlo lo haremos por el protocolo normal en Ubuntu:

    sudo apt-get update && sudo apt-get upgrade &&
    sudo apt-get install nginx

*NOTA*: Es importante que el puerto 80 esté libre por eso no deberemos tener
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
      server 172.16.168.130;
      server 172.16.168.131;
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

En mi caso la ip del server serán 192.168.1.111 y 192.168.1.222

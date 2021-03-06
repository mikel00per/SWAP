# Practica 4 | Asegurar la granja web
El objetivo de esta práctica es llevar a cabo la configuración de seguridad de
la granja web. Para ello, llevaremos a cabo las siguientes tareas para ello
instalaremos un certificado SSL para configurar le aceso HTTPS a los
servidores y configuraremos el cortafuegos.

## Generar e instalar un certificado SSL autofirmado
Para cada una de las máquinas servidoras tendremos que instalar el certificado.
El procedimiento es el siguiente, primero deberemos introducir la orden

    a2enmod ssl

Tras esto reiniciamos el servicio apache.

    service apache2 restart

Para instalalo introduciremos por último
    mkdir /etc/apache2/ssl

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt

Tras esto nos pedirá cierta información como el nombre, ciudad, correo, etc.

![install-ssl](https://github.com/mikel00per/SWAP/blob/master/Practica%204/install-ssl.png)

Ahora debemos modificar ciertos archivos de apache para indicar cual es nuestro
certificado para que apache lo lea y lo tenga en cuenta. Para ello editaremos el
fichero siguiente con nano modificando las líneas del SSLCertificateFile y
SSLCertificateKeyFile por las rutas del key y del certificado.

    nano /etc/apache2/sites-available/default-ssl.conf

![conf-ssl](https://github.com/mikel00per/SWAP/blob/master/Practica%204/ssl-conf.png)

Posteriormente activamos el sitio y reiniciamos Apache.
    a2ensite default-ssl
    service apache2 reload

Papara comprobar que se ha instalado correctamente y que podemos acceder con
nuestro certificado nos podemos cepcionar con ello con el comando curl.

    curl http:<ip-maquina>      // Mostará el acceso por http
    curl https:<ip-maquina>     // Mostará el acceso por https pero como nuestro
                                // certificado es autofirmado pondrá pegas por lo que
                                // deberemos añar la opción "-k"
    curl -k https:<ip-maquina>

Aquí se puede ver:
![comprobacion-ssl](https://github.com/mikel00per/SWAP/blob/master/Practica%204/comprobacion-ssl.png)

Por último solo queda copiar las claves y certificados de esta máquina en todas
las demás, en el balanceador y en la otra máquina servidora ya sea por rsync o por ssh.

    rsync -avz -e ssh <ip_maquina_configurada>:/etc/apache2/ssl /etc/apache2/ssl

**Nota**: Es necesario hacerlo como superusuario ya que deberemos ternerpermisos
de administrador para el directorio apache2.

Por último modificar el fichero de configuración antes mencionado indicando el
nuevo nombre de los archivos y su ubicación.

## Configuración del cortafuegos
Para configurar el cortafuegos existen muchas tipos de configuraciones, yo he
optado por seguir la del guión de practicas con algunas modificaciones. Para ello
hay que crear un script y hacer que el rc del usuario lo ejecute cada vez que se
inicie sesión.

El script usado es el siguiente

    #!/bin/bash
    # (1) Eliminar todas las reglas (configuración limpia)
    iptables -F
    iptables -X  

    # (2) Política por defecto: denegar todo el tráfico
    iptables -P INPUT DROP
    iptables -P OUTPUT DROP
    iptables -P FORWARD DROP

    # (3) Permitir cualquier acceso desde localhost (interface lo)
    iptables -A INPUT -i lo -j ACCEPT
    iptables -A OUTPUT -o lo -j ACCEPT

    # (4) Permitir la salida con conexiones establecidas y relacionadas
    iptables -A INPUT  -m state --state ESTABLISHED,RELATED -j ACCEPT
    iptables -A OUTPUT -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT

    # (5) Puerto 22 para ssh
    iptables -A INPUT -p tcp --dport 22 -j ACCEPT
    iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT

    # (6) Abrir los puertos HTTP/HTTPS
    iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT
    iptables -A INPUT -p tcp --dport 443 -j ACCEPT
    iptables -A OUTPUT -p tcp --sport 443 -j ACCEPT  

Tras esto deberemos hacer que el script se ejecute en cada arranque. Para ello
lo añadiremos al init.d para que se ejecute al iniciar con el resto de
componentes en el sistema.

    sudo mv <ruta_al_script>/script.sh /etc/init.d/

Tras esto introducimos el script en el crontab para que lo ejecute:

    sudo crontab -e

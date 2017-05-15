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

![install-ssl](http://)

Ahora debemos modificar ciertos archivos de apache para indicar cual es nuestro
certificado para que apache lo lea y lo tenga en cuenta. Para ello editaremos el
fichero siguiente con nano modificando las líneas del SSLCertificateFile y
SSLCertificateKeyFile por las rutas del key y del certificado.

    /etc/apache2/sites-available/default-ssl.conf

![conf-ssl](http://)

Posteriormente activamos el sitio y reiniciamos Apache.
    a2ensite default-ssl
    service apache2 reload

## Configuración del cortafuegos

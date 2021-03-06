# Practica 5 | Replicación Base de Datos MySQL

Tendremos posiblemente decenas de clientes y servicios parados sin posibilidad de
recuperar sus datos si no hemos preparado un buen plan de contingencias. Tener un
servidor de backup con MySQL actuando como esclavo de replicación es una solución
asequible y no consume demasiado ancho de banda en un sitio web de tráfico normal,
además de que no afecta al rendimiento del maestro en el sistema en producción.
Los objetivos concretos de esta práctica son:

1. Copiar archivos de copia de seguridad mediante ssh.
2. Clonar manualmente BD entre máquinas.
3. Configurar la estructura maestro-esclavo entre dos máquinas para realizar el clonado automático de la información.

## Creando una Base de datos
El procedimiento en este apartado lo haremos sobre nuestra máquina servidora
numero uno. Lo primero que haremos será crear en la consola de mysql con el
comando

    mysql -u root -p

Tras esto crearemos la base de datos llamada contactos

    create database contactos

Crearemos una tabla llamada datos que contendrá el nombre del contacto y su
telefono aunque antes deberemos de afirmar sobre que base de datos vamos a
trabajar

    use contactos;
    create table datos(nombre varchar(100), tlf int);

Para insertar datos usaremos el comando siguiente cuya estructura es sencilla
e intuitiva:

    insert into datos(nombre,tlf) values ("pepe", 9563246322);

Ahora vamos a mostrar todo el contenido de nuestra tabla datos, en mi caso
he introducido algunos contactos.

![create-db](https://github.com/mikel00per/SWAP/blob/master/Practica%205/create-db.png)

## Replicando una Base de Datos de MySQL

Lo primero que haremos antes de poder copiar una base de datos a un destino
determinado será bloquear la base de datos para que no sea modificada durante
este proceso, para ello entramos en la consola de mysql escribimos:

    $ mysql -u root -p
    mysql> FLUSH TABLES WITH READ LOCK;
    mysql> exit

Ahora ya podemos hacer un respaldo de la base de datos con el comando __mysqldump__:

    mysqldump respaldo -u root -p > ~/respaldo.sql

Para desbloquear la base de datos deberemos de hacer:

    mysql -u root –p
    mysql> UNLOCK TABLES;
    mysql> exit

![respaldo-bd](https://github.com/mikel00per/SWAP/blob/master/Practica%205/respaldo-bd.png)

## Replicando copia de BD en una segunda máquina a mano
Tras haber hecho el respaldo inicial vamos ahora a copiarlo a una segunda máquina
para antes deberemos haber creado en la otra máquina la base de datos debido
a que el fichero .sql de replicado no crea la base de datos, ha de estar creada.

    mysql -u root –p
    mysql> CREATE DATABASE contactosDOS;
    mysql> exit

Copiamos el fichero respaldo.sql para restaurar la copia, esto lo haremos desde
la maquina2, hay que entender que las máquinas han de verse

    scp maquina1:/tmp/ejemplodb.sql /tmp/

![copiado-realizado](https://github.com/mikel00per/SWAP/blob/master/Practica%205/copiado-realizado.png)


## Replicando copia de BD en una segunda máquina con Maestro-Esclavo
La opción anterior funciona perfectamente, pero es algo que realiza un operador
a mano. Sin embargo, MySQL tiene la opción de configurar el demonio para hacer
replicación de las BD sobre un esclavo a partir de los datos que almacena el
maestro.

Para configurar la configuracion de mysql para maestro esclavo deberemos
modificar el archivo. Hay que tener en cuenta que la máquina 1 será el
maestro y la máquina 2 el esclavo

    sudo nano /etc/mysql/my.cnf
    ó (deoendiendo la versión)
    sudo nano /etc/mysql/mysql.conf.d/mysql.cnf

Las siguiente lisneas son para la configuración del maestro

    #bind-address (Comentarlo)
    log_error = /var/log/mysql/error.log
    server-id = 1
    log_bin = /var/log/mysql/bin.log
    /etc/init.d/mysql restart

La configuración para la máquina2 será la siguiente:
    #bind-address (Comentarlo)
    log_error = /var/log/mysql/error.log
    server-id = 2
    log_bin = /var/log/mysql/bin.log
    /etc/init.d/mysql restart

![conf-mysql](https://github.com/mikel00per/SWAP/blob/master/Practica%205/conf-mysql.png)

**NOTA**: Solo si estamos en versiones inferiores a la 5.5 deberemos de modificar
lo siguiente

    Master-host = 192.168.45.140
    Master-user = usuariobd
    Master-password = 123456

Para saber la version de mysql: 'mysql --version'

Ahora vamos a crear los usuarios de nuestra base de datos, este procedimiento
lo haremos en la maquina maestra, en nuestro caso la maquina 1.

    mysql> CREATE USER esclavo IDENTIFIED BY 'esclavo';
    mysql> GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';
    mysql> FLUSH PRIVILEGES;
    mysql> FLUSH TABLES;
    mysql> FLUSH TABLES WITH READ LOCK;


En la máquina esclava escribimos la sentencia para cambiar e identificar
al maestro, además iniciamos al esclavo.

    mysql> CHANGE MASTER TO MASTER_HOST='192.168.31.200', MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=501, MASTER_PORT=3306;
    mysql> START SLAVE;

Aqui podemos ver un ejemplo de ello en mis máquinas:

![mysql-slave](https://github.com/mikel00per/SWAP/blob/master/Practica%205/mysql-slave.png)


Por último volvemos al maestro y activamos sus tablas

    mysql> UNLOCK TABLES;

Para comprobar que todo va correctamente en el esclavo escribimos lo siguiente

    mysql> SHOW SLAVE STATUS\

Como podemos observar en la variable Seconds_Behind_Master (es la que nos importa)
su valor es 0 por lo que hay conexion.

![status-slave](https://github.com/mikel00per/SWAP/blob/master/Practica%205/status-slave.png)

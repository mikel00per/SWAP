# SWAP 2016/2017
Asignatura de SWAP cursada en 2017 con Pedro Castillo. Y cursada por
Antonio Miguel Morillo Chica.

## Ejercicios
Ejercicios de las clases de teoría. Los ejercicios se pueden encontrar en las
transparencias.

## Trabajo Teoría
Trabajo realizado por Alberto Rivera Peña, Antonio Jimenez Arguello y Antonio 
Miguel Morillo Chica sobre los honeypots. Una medida para el aprendizaje sobre
incrusiones y ataques a servidores además de una medida de seguridad real. 

## Practica 1 | Preparación de herramientas
Crearemos dos máquinas virtuales con ubuntu server que deberán poder comunicarse
internamente. Modificaremos pues las ips para que sean distintas y estáticas.

## Practica 2 | Clonar la información de un sitio web
Los objetivos concretos de esta segunda práctica son:
1. Aprender a copiar archivos mediante ssh.
2. Clonar contenido entre máquinas.
3. Configurar el ssh para acceder a máquinas remotas sin contraseña.
4. Establecer tareas en cron.

## Practica 3 | Balanceo de carga
En esta práctica configuraremos una red entre varias máquinas de forma que
tengamos un balanceador que reparta la carga entre varios servidores finales.
El problema a solucionar es la sobrecarga de los servidores. Se puede balancear
cualquier protocolo, pero dado que esta asignatura se centra en las tecnologías web,
balancearemos los servidores HTTP que tenemos configurados.
De esta forma conseguiremos una infraestructura redundante y de alta disponibilidad.

## Practica 4 | Asegurar la granja web
El objetivo de esta práctica es llevar a cabo la configuración de seguridad de la
granja web. Para ello, llevaremos a cabo las siguientes tareas para ello
instalaremos un certificado SSL para configurar le aceso HTTPS a los servidores y
configuraremos el cortafuegos.

## Practica 5 | Replicación de bases de datos MySQL
Tendremos posiblemente decenas de clientes y servicios parados sin posibilidad de
recuperar sus datos si no hemos preparado un buen plan de contingencias. Tener un
servidor de backup con MySQL actuando como esclavo de replicación es una solución
asequible y no consume demasiado ancho de banda en un sitio web de tráfico normal,
además de que no afecta al rendimiento del maestro en el sistema en producción.
Los objetivos concretos de esta práctica son:
1. Copiar archivos de copia de seguridad mediante ssh.
2. Clonar manualmente BD entre máquinas.
3. Configurar la estructura maestro-esclavo entre dos máquinas para realizar el
clonado automático de la información.

## Practica 6 | Discos en RAID
En esta práctica configuraremos dos discos en RAID 1 por software, usando una
maquina virtual con Ubuntu server. Esta configuración RAID ofrece una gran
seguridad al replicar los datos en los dos discos.

Los objetivos concretos de esta práctica son:
1. Configurar dos discos en RAID 1. Los discos se añadirán a un sistema ya
instalado y funcionando, de forma que en total tendremos tres discos.
2. Hacer pruebas de retirar y añadir un disco “en caliente”, y comprobar que el
RAID sigue funcionando correctamente.

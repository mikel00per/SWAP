## Tema 1 | Introducción y conceptos básicos

**Ejercicio 1**: Buscar información sobre las tareas o servicios web comentados
a principio del tema.

  + **Apache**:  es el Servidor Web más utilizado, líder con el mayor número de
  instalaciones a nivel mundial muy por delante de otras soluciones como el IIS
  (Internet Information Server) de Microsoft. Apache es un proyecto de código
  abierto y uso gratuito, multiplataforma, muy robusto y que destaca por su
  seguridad y rendimiento. Además, Apache nos permite configurar un Hosting
  Virtual basado en IPs o en nombres, es decir, tener varios sitios web en un
  mismo equipo (por ejemplo: nombreweb1.com, nombreweb2.com,….) o, establecer
  distintos niveles de control de acceso a la información incluyendo el soporte
  a cifrado SSL utilizando protocolo seguro HTTPS.

  + **Ngingx**: es un servidor web HTTP de código abierto que también incluye
  servicios de correo electrónico con acceso al Internet Message Protocol (IMAP)
  y al servidor Post Office Protocol (POP). Además, NGINX está listo para ser
  utilizado como un proxy inverso. En este modo, NGINX se utiliza para equilibrar
  la carga entre los servidores back-end, o para proporcionar almacenamiento en
  caché para un servidor back-end lento.

  + **Hypertext** Transfer Protocol o HTTP (en español protocolo de transferencia
    de hipertexto) es el protocolo usado en cada transacción de la World Wide Web.
    HTTP fue desarrollado por el World Wide Web Consortium y la Internet
    Engineering Task Force, colaboración que culminó en 1999 con la publicación
    de una serie de RFC, siendo el más importante de ellos el RFC 2616, que
    especifica la versión 1.1. HTTP define la sintaxis y la semántica que
    utilizan los elementos de software de la arquitectura web (clientes,
    servidores, proxies) para comunicarse. Es un protocolo orientado a
    transacciones y sigue el esquema petición-respuesta entre un cliente y un
    servidor. Al cliente que efectúa la petición (un navegador web o un spider)
    se lo conoce como "user agent" (agente del usuario). A la información
    transmitida se la llama recurso y se la identifica mediante un localizador
    uniforme de recursos (URL). Los recursos pueden ser archivos, el resultado
    de la ejecución de un programa, una consulta a una base de datos, la
    traducción automática de un documento, etc.

  + **Cherekee**: El servidor web Cherokee es uno de los servidores de nueva
  generación llamados ligeros que mejora notablemente el rendimiento ofrecido
  por el más usado Apache HTTPD soportando más usuarios concurrentes, aceptando
  más peticiones por segundo y consumiendo menos memoria. Quizá no tenga toda la
  versatilidad de un servidor Apache HTTPD pero para la mayoría de los escenarios
  es más que suficiente. algunas de las características que soporta: FastCGI,
  SCGI, PHP, CGI, SSI, TLS y conexiones cifradas SSL, host virtuales,
  autenticación, codificación al vuelo, balanceo de carga, archivos de log
  compatibles con apache, balanceador de base de datos, actualizaciones sin
  parada del servicio, proxy HTTP inverso y mucho más.

  + **Node.js**: Node.js es una librería y entorno de ejecución de E/S dirigida
  por eventos y por lo tanto asíncrona que se ejecuta sobre el intérprete de
  JavaScript creado por Google V8. Lo cierto es que está muy de moda aunque no
  es algo nuevo puesto que existen librerías como Twisted que hacen exactamente
  lo mismo pero si es cierto que es la primera basada en JavaScript y que tiene
  un gran rendimiento.  es un entorno Javascript del lado del servidor, basado
  en eventos. Node ejecuta javascript utilizando el motor V8, desarrollado por
  Google para uso de su navegador Chrome. Aprovechando el motor V8 permite a
  Node proporciona un entorno de ejecución del lado del servidor que compila y
  ejecuta javascript a velocidades increíbles. El aumento de velocidad es
  importante debido a que V8 compila Javascript en código de máquina nativo, en
  lugar de interpretarlo o ejecutarlo como bytecode. Node es de código abierto,
  y se ejecuta en Mac OS X, Windows y Linux.


--------------------------------------------------------------------------------

## Tema 2 | Alta disponibilidad y escalabilidad en servidores web

**Ejercicio 1**:  Calcular la disponibilidad del sistema si tenemos dos réplicar
de cada elemento (en total 3 elementos en cada subsistema).

| Componet     | Dos replicas |  Calculos nuevos                         | Tres replicas |
|--------------| ------------ | ---------------------------------------- | ------------- |
| Web          | 97,75%       | 97,75% + (1 – 97,75%) * 97,75%           | 99,949375%    |
| Application  | 99%          | 99% + (1 – 99%) * 99%                    | 99,99%        |
| Database     | 99,9999%     | 99,9999% + (1 – 99,9999%) * 99,9999%     | 100%          |
| DNS          | 99,96%       | 99,96% + (1 – 99,96%) * 99,96%           | 99,999984%    |
| Firewall     | 97,75%       | 97,75% + (1 – 97,75%) * 97,75%           | 99,949375%    |
| Switch       | 99,99%       | 99,99% + (1 – 99,99%) * 99,99%           | 99,999999%    |
| Data Center  | 99,99%       | 99,99% + (1 – 99,99%) * 99,99%           | 99,999999%    |
| ISP          | 97,75%       | 99,75% + (1 – 99,75%) * 99,75%           | 99,999375%    |


_Total_ = 99,949375% \* 99,99% \* 100% \* 99,999984% \* 99,949375% \* 99,999999% \*
99,999999% \* 99,999375% = 99,8881435%

**Ejercicio 2**: Buscar frameworks y librerías para diferentes lenguajes que
permitan hacer aplicaciones altamente disponibles con relativa facilidad.

  + JavaScript: Tenemos algunos como jQuery, bootstap, Backbone JS, Ember JS, NodeJS
  + Css: Foundation
  + Html: Browserify


**Ejercicio 3**: ¿Cómo analizar el nivel de carga de cada uno de los subsistemas
en el servidor? Buscar herramientas y aprender a usarlas.

He encontrado dos herramientas, una de las cuales usamos en la practica 3 para
comprobar como reacciona nuestra granja web a una gran carga. Las herramientas
son:

  + Apache Benchmark
  + Siege

El hecho de saber usar AB lo hemos visto en practicas, aún así dejo una pequeña
explicación de la orden con algunos argumentos.

``` bash
  ab -n 10000 -c 100 -g grafica.data http://localhost:8888/loadTesting/test
```
  + ab nos permite hacer uso de la herramienta.
  + n especificamos la cantidad de peticiones que deseamos enviar.
  + c especificamos la cantidad de conexiones concurrentes.
  + g podemos generar una gráfica de gnuplot para apreciar mejor los resultados.
  + Finalmente colocamos la dirección que deseamos probar.


**Ejercicio 4**: Buscar ejemplos de balanceadores software y hardware (productos
comerciales), productos comerciales para servidores de aplicaciones y productos
comerciales para servidores de almacenamiento.

  + Balanceadores:
  + Servidores de aplicaciones:
  + Servidores de almacenamiento:

--------------------------------------------------------------------------------

## Tema 3 | La red de una granja web

**Ejercicio 1**: Buscar con qué órdenes de terminal o herramientas gráficas
podemos configurar bajo Windows y bajo Linux el enrutamiento del tráfico de un
servidor para pasar el tráfico desde una subred a otra.

  - Windows: route ADD "IP RED" MASK "MASCARA" “PUERTA DE ENLACE”
  - Linux: route add default gw "PUERTA DE ENLACE"
           route add -net "SEGUNDA SUBRED" netmask "MASCARA" gw "PUERTA DE ENLACE 2º SUBRED"

**Ejercicio 2**: Buscar con qué órdenes de terminal o herramientas gráficas
podemos configurar bajo Windows y bajo Linux el filtrado
y bloqueo de paquetes.

  - Windows: Con herramienta grafica: Para abrir Firewall de Windows, hacer
  click en el botón Inicio > Panel de control. En el cuadro de búsqueda,
  escribir firewall y hacer clic en Firewall de Windows. (no uso windows así
  que no lo he probado)

  - Linux: Con el uso de iptables, en las practicas se explica su uso.

--------------------------------------------------------------------------------

## Tema 4 | Balanceo de carga

**Ejercicio 1**: Buscar información sobre cuánto costaría en la actualidad
un mainframe. Comparar precio y potencia entre esa máquina y una granja web
de unas prestaciones similares.

Normalmente ya no se suelen utilizar mainframes como tal (multiprocesador),
se suelen usar nodos (multicomputadores) pues son mas tolerantes a fallos, más
fáciles de ampliar.

**Ejercicio 2**: Buscar información sobre precio y características de
balanceadores hardware específicos. Compara las prestaciones que ofrecen unos y
otros.

|                   |  Cisco CSS 11501                            | Brocade ServerIron ADX 1000                |
| ----------------- | ----------------------------------------    | ------------------------------------------ |
| Precio            |  £4.019.99                                  |  £6,479.99                                 |
| Peso              |  8,16Kg                                     |  17Kg                                      |
| RAM               |  -                                          |  8GB                                       |
| Conexiones        |  Ethernet, Fast Ethernet                    |  Ethernet, Fast Ethernet, Gigabit Ethernet |
| Metetodo. Autent. |  DES, T.DES, RSA, RC4, TLS 1.0, SSL 3.0/2.0 |  SSL                                       |

Entre otras que se pueden encontrar [aqui](https://www.pcworldbusiness.co.uk/catalogue/item/P103339P#tabss)
y [aqui](https://www.pcworldbusiness.co.uk/catalogue/item/P186722P#tabss)

**Ejercicio 3**: Buscar información sobre los métodos de balanceo que
implementan los dispositivos recogidos en el ejercicio 2.

Los métodos usados para Brocade ServerIron ADX 1000 son:
1. Least Connections Predictor
2. Weighted Round Robin Predictor
3. Round Robin Predictor
4. Weighted Predictor and Enhanced Weighted Predictor
5. Dynamic Weighted Predictor
6. Dynamic-Weighted Direct
7. Dynamic-Weighted Reverse

Más información (aqui)[http://community.brocade.com/t5/Application-Delivery-ADX/Load-Balancing-Distribution-Predictors-with-ServerIron/ta-p/3839]

Por otro lado para Cisco CSS 11501:
1. Round Robin
2. Weighted Round Robin
3. Least Connections/Bytes
4. ArrowPoint Content Aware (ACA)
5. Otros: urlhash, domainhash, url, domain, srcip, and destip

Más información (aqui)[http://www.cisco.com/c/en/us/support/docs/application-networking-services/css-11500-series-content-services-switches/28862-methods-load-bal.html#services]


**Ejercicio 4**: Instala y configura en una máquina virtual el balanceador
ZenLoadBalancer.


**Ejercicio 5**: Probar las diferentes maneras de redirección HTTP. ¿Cuál es
adecuada y cuál no lo es para hacer balanceo de carga global? ¿Por qué?


**Ejercicio 6**: Buscar información sobre los bloques de IP para los distintos
países o continentes. Implementar en JavaScript o PHP la detección de la zona
desde donde se conecta un usuario.

_No conozco ninguno de estos lenguajes._

**Ejercicio 7**: Buscar información sobre métodos y herramientas para
implementar GSLB.


--------------------------------------------------------------------------------

## Tema 5 | Medir prestraciones

**Ejercicio 1**: Buscar información sobre cómo calcular el número de
conexiones por segundo.


**Ejercicio 2**: Revisar los análisis de tráfico que se ofrecen en: http://bit.ly/1g0dkKj
Instalar wireshark y observar cómo fluye el tráfico de red en uno de los
servidores web mientras se le hacen peticiones HTTP.

**Ejercicio 3**: Buscar información sobre características, disponibilidad para
diversos SO, etc de herramientas para monitorizar las prestaciones de un servidor.

--------------------------------------------------------------------------------

## Tema 6 | Asegurar el sistema web

**Ejercicio 1**: Aplicar con iptables una política de denegar todo el tráfico en
una de las máquinas de prácticas. Comprobar el funcionamiento. Aplicar con
iptables una política de permitir todo el tráfico en una de las máquinas de
prácticas. Comprobar el funcionamiento.


**Ejercicio 2**: Comprobar qué puertos tienen abiertos nuestras máquinas,
su estado, y qué programa o demonio lo ocupa.


**Ejercicio 3**: Buscar información acerca de los tipos de ataques más
comunes en servidores web (p.ej. secuestros de sesión). Detallar en qué
consisten, y cómo se pueden evitar.   

--------------------------------------------------------------------------------

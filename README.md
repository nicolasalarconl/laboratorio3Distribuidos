# Sistemas Distribuidos Laboratorio semestral

 Primer semestre 2019: Nicolas Alarcon y Sandra Hernandez

## Enunciado 📖


La búsqueda de información en la Internet ha sido un tema cada vez más recurrente y más difícil de tratar. No soló por el hecho de consultar a
múltiples lugares geograficos, sino tambien sincronizar los nuevos datos que
van agregandose  dıa tras dıa. Por ejemplo, al buscador Google se realizan
40.00 consultas por segundo [1], lo cual debe ser procesado por los servidores,
por lo que se hace difıcil el manejo de las distintas consultas.
Un servidor centralizado se hace inviable para manejar la carga creciente
de consultas que realicen los millones de usuarios, debido a la cantidad de
consultas, la distancia geografica,  capacidad de almacenamiento, rendimiento
y disponibilidad del sistema. Es por esto, que es importante disen˜ar una
arquitectura distribuida que pueda dar solucion a estas problematicas.

En este laboratorio se propone utilizar los conceptos vistos en clases,
correspondientes a almacenamiento, escalabilidad, confiabilidad, disponibilidad, rendimiento, virtualización, geo-distribucion y tolerancia a fallas para
solucionar el problema planteado.



### Análisis y diseño de la arquitectura propuesta 

En primer lugar el problema a desarollar es un buscador de consulta. Nuestro laboratorio intento emular la plataforma IMDb la cual es una base de datos en línea que almacena información relacionada con películas, personal de equipo de producción (directores y productores), actores, series de televisión, programas de televisión, videojuegos, actores de doblaje(Ver imagen 1). Esta plataforma fue elegida por que recibe más de 100 millones de usuarios únicos al mes. En nuestra aplicación se usara la base de datos de esta aplicación. 


<a href="https://ibb.co/hVP39pg"><img src="https://i.ibb.co/TtV92zw/Captura-de-pantalla-2019-07-06-a-la-s-23-21-32.png" alt="Captura-de-pantalla-2019-07-06-a-la-s-23-21-32" border="0"></a> <br>Imagen 1

Nuestra plataforma cuenta con un front-end (ver imagen 2) donde se podran consultar los directores de cada pelicula como muestra a continuación y un backend(ver imagen 3) el cual obtiene de la base de datos de el director especifico de una pelicula.

Imagen 2 <br>
<a href="https://ibb.co/V3QLGX1"><img src="https://i.ibb.co/x3f2bVk/Captura-de-pantalla-2019-07-06-a-la-s-23-21-09.png" alt="Captura-de-pantalla-2019-07-06-a-la-s-23-21-09" border="0"></a> 

<a href="https://ibb.co/vHPH5Lp"><img src="https://i.ibb.co/47T7y4z/Captura-de-pantalla-2019-07-06-a-la-s-23-22-34.png" alt="Captura-de-pantalla-2019-07-06-a-la-s-23-22-34" border="0"></a><br>
Imagen 3


Para nuestra implementación utilizamos ***Kubernet*** el cual es un orquestador de contenedores. Esta elección fue debida a que sus principal caracteristica es ser autoescalado dado que en funcion del uso de la cpu de los servidores permite escalar vertical la aplicacion de manera automatica. ademas permite el balanceo de carga y autoreparación en caso de fallos.

La arquitectura de Kuberent es la siguiente:

<a href="https://ibb.co/jkwpfFv"><img src="https://i.ibb.co/nM1WmSs/65928300-2256486411065298-3922712340037894144-n.png" alt="65928300-2256486411065298-3922712340037894144-n" border="0"></a><br />


Kubernetes sigue una arquitectura maestro-esclavo. Donde el nodo maestro son parte de  un panel de control y los nodos esclavos son los que contienen y  administran los contenedores.

El nodo maestro es el que esta a cargo de mantener a los nodos esclavo y escalar la aplicacion.

El nodo esclavo es una maquina virtual, cada nodo tiene un kubelet que administra el nodo y se comunica con su el nodo maestro, a través de el API de kubernetes. 

etcd es un almacén de datos persistente que almacena de manera confiable los datos de configuración.

Un pod son uno o mas contenedores con almacenamiento y red compartidos. 

Kube-Proxy es un modulo de Kubernet que permite abstraerse de las operaciones de red. Kube-proxy implementa un proxy de red y un balanceador de carga.  Este proxy permite enrutar el trafico hacia el contender segun una metrica.

De esta manera, el funcionamiento de la aplicación inicia al recibir una consulta desde el front-end hacia kube-proxy, este determina cual de los pod seran asignados. una vez asignado un pod, este tendra contendedores que cuenta con el blackend y la base de datos de la aplicación. El nodo Maestro entra en juego al monitorear constantemente los nodos, creando, reconstruyendo o eliminando pods para asegurar disponibilidad.


### Análisis del rendimiento de la arquitectura

Para nuestra implementación se ha utilizado kubernetes de digital ocean, donde se tienen un nodo maestro y 3 nodos esclavos por defecto. Kubernets tiene una plataforma que permite realizar un analisis de rendimiento respecto al kubernetes como sistema completo o a cada nodo individualmente. Las estadisticas entregadas por esta plataforma es CPU usada, Balanceador de carga, Memoria usada, Disco usado, I/O Usado, Ancho de banda utilizado. Como se pueden observar a continuación:

<a href="https://ibb.co/Jd02ms7"><img src="https://i.ibb.co/GJBkn52/Captura-de-pantalla-2019-07-07-a-la-s-04-24-46.png" alt="Captura-de-pantalla-2019-07-07-a-la-s-04-24-46" border="0"></a>

<a href="https://ibb.co/wN14Cb7"><img src="https://i.ibb.co/FbtzJfX/Captura-de-pantalla-2019-07-07-a-la-s-04-27-50.png" alt="Captura-de-pantalla-2019-07-07-a-la-s-04-27-50" border="0"></a>
<a href="https://ibb.co/fQmTRWz"><img src="https://i.ibb.co/JCXSLTw/Captura-de-pantalla-2019-07-07-a-la-s-04-27-35.png" alt="Captura-de-pantalla-2019-07-07-a-la-s-04-27-35" border="0"></a>

Ademas de este analisis de rendimiento es necesario mirarlo del punto de vista distribuido, es decir su escabilidad,  reparto de carga, tolerancia a fallos , disponibilidad, entre otros.


Escabilidad: Si un pod no es capaz de atender el trafico con calidad de servicio, se crean mas replicas de ese mismo pod.  La carga de distribuye entre los diferentes pod. Los nodos se agregan dinamicamente bajo demanda. Todas estas caracteristicas hacen que Kubernetes sea una arquitectura escalable. 

Las caracteristicas de tolerancia de fallas, reparto de carga, disponibilidad seran mencionadas mas tarde.


## Análisis sobre tolerancia a fallas y disponibilidad por parte del sistemas 

Para ser una arquitectura tolerante a fallos esta debe ser rebundante, es decir tener varias copias de elementos que esten desacoplados entre si para que un fallo de una no afecte  a las demas.  Las replicas de los pods que se generan de manera automatica (monitoreada por el nodo maestro) y se ejecutan en diferentes nodos ofrecen la rebundancia. Ademas si un nodo falla en tiempo de ejecución los pod que se estan ejecutando en ese nodo, se crean en otro disponible y el nodo fallido se sustituye por uno nuevo.

Existe una herramienta para determinar  pruebas de fallos para kubernets llamado Gremlin, no fueron instaladas en este laboratorio, pero permiten realizar un analisis mas profundo como saturación de la CPU y eliminar nodos cada ciertos cantidad de tiempo para ver como responde tu sistema. 

Para ser una arquitectura de alta disponibilida  se debe asegurar el nivel de rendimiento operacional por un periodo superior a lo normal. En la arquitectura de Kubernetes la disponibilidad viene por defecto por ejemplo cuando un nodo esclavo desaparece el maestro envia las peticios a otro nodo esclavo(replica del anterior) para continuar aguantando el trafico. Otro ejemplo es que el trafico se reparte entre todo los nodos. Esta disponibilidad es posible gracias al monitoreo de uso de CPU y Ram de los contenedores por parte del nodo Maestro. Sin embargo no se puede asegurar el cien por cierto de disponibilidad. 


### Selección enrutamiento de la consulta realizada por el cliente en base a una métrica 

 El proceso de enrutamiento se puede ver en la siguiente imagen:

<a href="https://ibb.co/NxJv8TH"><img src="https://i.ibb.co/G786KHb/services.png" alt="services" border="0"></a><br /> <br />


 Kube-Proxy proporciona balanceo de carga y se utiliza para llegar a los servidores. Kubernetes busca abstraerse de las operaciones de red por lo cual por defecto incluye metricas de analisis para elegir el mejor pod para ejecutar la consulta. Las metricas dependen de un ranking(Valor normalizado) que incluye Memory Usage (memoria utilizada por parte del pod), Memory Limit ( memoria maxima asignada al pod), CPU asignado al pod, réplicas disponibles de los pods, entre otras.  Sin embargo es posible ajustar estas metricas en un archivo de configuraciones.


### Paralelización de la consulta 

La implementación de este laboratorio no fue de manera paralela en la consulta, sin embargo existen formas de realizarlo a traves de redis.


## Análisis de la distribución de la base de datos 📦

Kubernet permite utilizar base de datos distribuida, debido a que en cada pod se puede contener una base de datos y asi estar en diferentes espacios logicos y/o geograficos. Ademas que la conexiones de red entre nodos permite la comunicacion entre estas de manera interna y rapida. Kubernetes proporciona disponibilidad de estas a traves de las replicas. 

En esta implementacion se crearon 3 nodos por defectos en el cual cada nodo esclavo contaba con un pod y cada pod tenia la misma base de datos de lectura.
por lo cual para esta aplicacion no existen problemas de escritura, debido a que solo existe lectura de datos. 

## Instalar Aplicación

* para esto es necesario la base de datos pedir el esquema por email por que es muy pesada*


1- Una vez descargado el repositorio escribir en la terminal el siguiente comando: 

```
docker-compose up
```


2- Una vez levantada la aplicacion  es necesario configurar e instalar las imagenes de docker a kubernetesal para eso se debe contar con una cuenta en digital ocean y crear una configuracion para que esta comienzas a ser monitoreadas 





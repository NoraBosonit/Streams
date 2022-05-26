# Procesamiento de Streams
## Introducción a Kafka
### Qué es?
Es una plataforma de streaming distribuida capaz de manejar trillones de eventos al día. 
Una plataforma de streaming tiene tres capacidades clave:
1. Procesa flujos de registroa a medida que se van produciendo
2. Almacena estos flujos de forma duradera y estolerante a fallos
3. Puede publicar ys sucribirse a los flujos de datos 
### Para qué se utiliza
- Para construir fujos de datos en tiempo real 
- Crear aplicaciones de treaming en tiempo real
### Conceptos fundamentales
Kafka se ejecuta como un cluster en uno o más servidores que pueden abarcar varios centros de datos. Almacena los flujos de registros en categorías llamadas **topics** en donde cada registro consta de una clave, un valor y una marca de tiempo. Supongo que los topics presentarán los mismos valores.

1. **Topics**: Es un flujo de datos (registros) particular. Es decir, una serie de registros. 
    - Cada topic se identifica por su nombre
    - Los topics se dividen en particiones, las cuales son secuencias ordenadas e inmutables de registros. A los registros de las particiones se les asigna un número de identificación secuencial llamado **offset** el cual identifica de manera única a cada registro dentro de la partición
    - Una vez un dato ha sido escrito en una partición, no se puede modificar
    - Los topics deben tener un fator de replicación superior a uno. 
    - El orden de las particiones se respeta dentro de cada registro. El offset 3 del registro 1 será diferente del offset 3 del registro 2
2. **Brokers**: Son los servidores que componen un cluster kafka
    - Cada broker se identifica por in ID
    - Contienen un cierto número de particiones de topics cada uno
    - Una vez uno se conecte a un broker (bootstrap broker) se conecta a todo el cluster. Es decir basta con conectarse a un broker para acceder al cluster completo.
3. **Líderes y particiones**
    - En todo momento una partición dada solo puede tener un broker lider
    - Solo el lider puede recibir y escribir data para una parición
    - El resto de brokers se limitan a sincronizar la información (la replicación)
4. **Producer**: Se encargan de escribir datos en los topics (divididos en particiones)
    - Conocen automáticamenta a qué broker y partición escribir. Es decir, la partición en la que tiene que escribir y en qué broker está el dato que a tiempo real acaba de llegar
    - Si falla un broker, el producer se recupera automáticamente
    - Pueden escoger enviar una clave para un ordenamiento de datos específico; si la clave es null los datos con enviados en round robin (bloker 101, luego el 102...) si no es null, todas las claves con el mismo valor irán a la misma partición. 
5. **Consumer**: Son los que leen los datos desde un topic (Para pasar los datos a una aplicación por ejemplo)
    - Saben desde cuál broker leer los datos
    - Si falla un broker, los consumer saben cómo recuperarse
    - Los datos son leídos en orden dentro de una partición
    -  Leen los datos en grupo; cada consumer lee unas particiones exclusivas
    -  Semántica de entrega para los consumidores:
        - At most one: Offsets son entregados tan pronto como los mensajes son recibidos. Si el proceso va mal el dato se perderá. Cuando se necesita que los datos lleguen muy rápido y no importa que se pierda alguno (una videollamada, por ejemplo)
        - Al least one: Los offsets son entregados después de que el mensaje sea procesado. Si el procesado va mal, el mensaje será leído de nuevo (Si es una página web, no puede faltar trozos, aunque tarde más en procesar. Por ejemplo, un mapa del resultado de las elecciones por comunidad). Como se leen varias veces pueden hav¡ber duplicados
        - Exactly one: Cuando tenemos falta de memoria
6. **Zookeper**: Almacena metadatos sobre el cluster y gestiona los brokers
    -  Ayuda a escoger el líder para las particiones
    -  Envía notificaciones al cluster en caso de cambios

## Introducción al procesamiento de streams
### Qué es?
El procesamiento de streams es el acto de incorporar continuamente datos para calcular un resultado. Los datos de entrada son ilimitados y no tiene un inicio y final determinados.

### Procesamiento en straming vs procesamiento en batch
Los datos del procesamiento en batch con fijos mientras en streaming son ilimitados. Por otro lado, en el primer caso se hacen múltiples operaciones a medida que pasa el tiempo mientras que en el segundo caso, basta con hacer una única operáción (por ejemplo si se trabaja con un historial de datos).

### Streaming y Batch
A menudo suelen trabajar de forma conjunta ya que para calcular resultados se suele hacer con datos que ya hay (batch, datos limitados) con los datos que van llegando. Para saber qué comunidad autónoma va ganando las elecciones, se necesitan los datos del anterior mapa y lo que acaba de llegar para calcular un resultado. 

### Qué es un sistema a tiempo real
Leen datos a tiempo real y se calsifican en **hard**, **soft** y **near** (casi). Los hard son fáciles de identificar ya que tienen unos requisitos de tiempo muy estrictos y si no se cumplen, puede haber un fallo en el sistema. Los otros dos son más difíciles de identificar ya que ambos dos son casos en los que un retraso en el procesamiento no es de vida o muerte. Alguien retwitea tu tweet o estás haciendo un seguimiento de unas acciones son algunos ejemplos. 
![hard-soft-near](https://user-images.githubusercontent.com/102373797/169527438-c9fef92f-dd72-4869-b829-6f0d636b56f7.png)

### Diferencia entre tiempo real y sistema de treaming
Muchas veces el sistema opera en tiempo real, pero la aplicación no por lo que el cliente no accede a los datos a tiemo real, sino que los toman cuando lo necesitan. Esto es un sistema en straming, un sistema No-Hard real time que hace que los datos estén disponibles cuando se soliciten. No es ni soft ni near, sino streaming.
**Ejemplos**
Generación de informes en tiempo real
Envío de notificaciones (en el correo, estos llegan cuando se refresca la página de forma automática)


## Introducción a Kafka parte II
### Transmisión de datos en Kafka
Kafka transporta bytes por lo que los registros se deben convertir a bytes. Para ello se utilizan serializadores, concertamente, Kafka hace uso de tres:
- IntegerSerializer: Únicamente datos de tipo entero
- StringSerializer: El serializador por defecto y se utiliza para cadenas de caracteres
- ByteStreamSerializer: Cualquier cosa que vaya en array de bytes

Existen serializadores para serialización personalizada
- Thrift: Biblioteca de codificación binaria que requiere de un esquema para todo dato codificado
- Protobuf: Igual que Thrift, pero un proyecto más pequeño
- Avro: Se creó porque Thrift no era una buena opción para los casos de uso de Hadoop

### Algoritmos de compresión
- GZIP
- Snappy
- LZ4

### Herramientas de consulta SQL para apache Kafka
- KSQL: Motor SQL de streaming para Apache Kafka
- Presto: Motor de consultas SQL distribuidas que puede set utilizado con tamaños de datos desde gigabytes hasta petabytes.

Por la facilidad de Presto para coenctarse con distintas bases de datos, es la herramienta que utiliza Kafka para las consultas.

## PrestoDB
Es un sistema distribuido que se ejecuta en un grupo de máquinas que incluye un coordinador y uno o varios trabajadores, y fue diseñada para consultar de manera eficiente grandes cantidades de datos. 

### Conceptos
Hay dos tipos de servidores de Presto: coordinadores y workers.
 **Coordinador:** Analiza las declaraciones, planifica consultas y administra los nodos de trabajo. Es el cerebro de una instalación Presto.
El coordinador realiza un seguimiento de la actividad de cada trabajador y coordina la ejecución de una consulta. 

**Worker**: Es un servidor es responsable de ejecutar tareas y procesar datos. Los workers obtienen los datosde los conectores e imtercambian datos intermedios entre ellos. Luego, es el coordinador el que obtiene los resultados de los workers y devuelve los resultados finales al cliente. 

### Fuentes de datos
**Conector:** Es el que adapta Presto a fuentes de datos com Hive. Cada catálogo está asociado a un conector específico aunque es posible que un catágolo utilice el mismo conector para dos instancias diferentes de una base de datos. 

**Catalogo:** Contiene esquemas y hace referencia a una fuente de datos a través de un conector. Por ejemplo un catálogo Hive para para conectarse a una fuente de datos de Hive y cada vez que se ejecuta una consulta SQL, la ejecución se hace en uno o más catálogos. 

**Esquema:** Formas de organizar las tablas. Un catálogo junto a un esquema definen un conjunto de tablas que se pueden consultar.

**Tabla:** La asignación de datos de origen a tablas está definida por el conector

### Modelo de ejecución de consultas
Presto ejecuta las sentencias de SQL y las convierte en consultas que se ejecutan en un grupo distribuido de coordinadores y trabajadores.

**Sentencias:** Presto de refiere a sentencia como cláusulas, expresiones y predicados. 

**Consultas:** La sentencia es el texto SQL y la consulta es cuando la sentencia se lleva a cabo.

**Etapa:** La consulta se ejecuta dividiendola en jerarquía de etapas. 

**Tarea:** Las etapas en sí mismas no se ejecutan en los workers sino su respectiva división de tareas.

**División:** Supongo que será que las tareas se dividen para hacer uso de varios workers (de forma distribuida)

**Operador:** Lo operadores cogen datos, los transforman y producen nuevos datos. Como el filtrado.

**Intercambiador:** Transfieren datos entre los nodos de Presto. 

## Spark Streaming
Extensión de Spark que permite el procesamiento de flujos de datos a tiempo real. Spark Streaming recibe flujos de datos de entrada en streaming y divide los datos en lotes para ser procesados y generar el resultado final. 
Spark incluye dos APIs para el procesamiento de streams:
- DStream: Secuencia continua de RDDs que representa un flujo continuo de datos
- Structured Streaming: Motor de procesamiento de streams

### Transformaciones de un DStream
Pueden modificar los datos de la entrada DStream. Algunas de las transformaciones más comunes son:

| Transformation | Meaning |
|----------|-------------|
| map(func) | Return a new DStream by passing each element of the source DStream through a function *func* |
| flatMap(func) |Similar to map, but each input item can be mapped to 0 or more output items.|
| filter(func)| Return a new DStream by selecting only the records of the source DStream on which func returns true.|
| repartition(numPartitions) |Changes the level of parallelism in this DStream by creating more or fewer partitions.|
| union(otherStream) |Return a new DStream that contains the union of the elements in the source DStream and otherDStream.|
| count() |Return a new DStream of single-element RDDs by counting the number of elements in each RDD of the source DStream.|
| reduce(func) |Return a new DStream of single-element RDDs by aggregating the elements in each RDD of the source DStream using a function func (which takes two arguments and returns one). The function should be associative and commutative so that it can be computed in parallel.| 
| countByValue() |When called on a DStream of elements of type K, return a new DStream of (K, Long) pairs where the value of each key is its frequency in each RDD of the source DStream.|
| reduceByKey(func,[numTasks])| When called on a DStream of (K, V) pairs, return a new DStream of (K, V) pairs where the values for each key are aggregated using the given reduce function **Note**: By default, this uses Spark's default number of parallel tasks (2 for local mode, and in cluster mode the number is determined by the config property spark.default.parallelism) to do the grouping. You can pass an optional numTasks argument to set a different number of tasks.|
| join(otherStream, [numTasks]) | When called on two DStreams of (K, V) and (K, W) pairs, return a new DStream of (K, (V, W)) pairs with all pairs of elements for each key. |
| cogroup(otherStream,[numTasks]) | When called on a DStream of (K, V) and (K, W) pairs, return a new DStream of (K, Seq[V], Seq[W]) tuples. |
| transform(func) | Return a new DStream by applying a RDD-to-RDD function to every RDD of the source DStream. This can be used to do arbitrary RDD operations on the DStream.|
| updateStateByKey(func) | Return a new "state" DStream where the state for each key is updated by applying the given function on the previous state of the key and the new values for the key. This can be used to maintain arbitrary state data for each key |

### Window Operations
Spark también proporciona cálculos de ventana con los Dstreams. Algunas son:
- window
- countByWindow
- reduceByWindow
- reduceByKeyAndWindow
- reduceByKeyAndWindow
- countByValueAndWindow

### Operaciones de salida
- print
- saveAsTextFiles
- saveAsObjectFiles
- saveAsHaddop
- foreachRDD

## Structured Streaming
Es un motor de porcesamiento de flujo de datos construido en el motor Spark SQL el cual se encarga de ejecutar el Structured Straming de forma incremental e ir actualizando el resultado a medida que van llegando nuevos datos. Las consultas se ejecutan con latencias de 100 milisegundos con garantía de exactamente uno, aunque se ha introducido un nuevo procesamiento denominado Procesamiento continuo que alcanza latencias de 1 milisegundo con garantías de al menos uno.

Structured Streaming en un Dataframe, pero a tiempo real. 
Las **funetes** de entrada son Kafka, ficheros como HDFS o S3 y Socket. Mientras que los sumiders especifican el destino del conjunto de resultados de ese flujo. Estos son los sumideros permitidos:
- Apache Kafka 0.10
- Casi cualquier formato de archivo.
- Un sumidero foreach para ejecutar el cálculo arbitrario en los registros de salida.
- Un sumidero de consola para probar.
- Un sumidero de memoria para depuración.

Definir un **sumidero (sink)** también va acompañado de cómo queremos que Spark escriba los datos en ese sumidero; si queremos que añada toda la información en cada flujo o si sol queremos que añada la nueva información que va llegando. Los modos de salida son:
- Append (solo agregar nuevos registros al receptor de salida)
- Update (actualizar los registros modificados en su lugar)
- Complete (reescribir la salida completa)

Hay veces que para determiandos sinks hay algunos modos de salida que no tienen sentido. Si por cada tweet que se escriba, se tiene que crear una base de datos nueva, sería muy costoso, no tiene sentido. Por el contrario, en un banco el complete y update tiene sentido, pero el append no porque las claves se cambian cada cierto tiempo. 

Los modos de salida definen cómo se emiten los datos, los **disparadores (triggers)** definen cuándo se miten los mismos, es decir cuándo se verifican los nuevos datos de entrada y se actualiza el resultado. Cada vez que acaba un proceso, el Structured Straming buscará nuevos registros de entrada, aunque Spark también admite triggers según el tiempo de procesamiento, es decir, cada vez que acabe un proceso buscar nuevos datos de los siguientes 3 milisegundos. Para esto hay dos ideas clave:
- Datos de tiempo de evento: tiempo de evento significa campos de tiempo incrustados en los datos que indican fechas o tiempos de alta o modificación, por ejemplo. Por lo tanto, datos de tiempo de evento tiene su explicación en que los datos se procesan en función de este tiempo y no en función del orden de llegada (ya que puede haber retrasos)
- Marcas de agua: permiten especificar qué tan tarde espera que llegue un dato. En cuánto se estima al demora máxima para volver a pedir el dato o darlo por perdido. 

### Operaciones de Ventana en tiempo de evento
Las agregaciones de una ventana en tiempo de evento son muy similares a las agregaciones por agrupación. Es decir group By col1 y sumo los valores de col2. En el caso de las agregaciones basadas en ventana, se agrupa por los valores del tiempo. Si tenemos una ventana deslizante de 5 segundos y queremos contar las palabras que llegan durante 10 segundos, tendremos: el número de palabras de 12:00 a 12:10 y como la ventana se desliza cada 5 segundos, también tendremos las palabras que han llegado de 12:05 a 12:15. 

### Manejo de datos tardíos y marcas de agua
La transmisión estructurada puede matener el estado intermedio de agregados durante un largo periodo de tiempo, por lo que los datos tardíos pueden actualizar los agregados de ventanas. Para saber durante cuánto tiempo es necesario mantener un estado intermedio y saber cuán se puede eliminar de memoria se utilizan marcas de agua. Una vez llegue un dato que supera la marca de agua, este se eliminará. 

### Stream-static Joins
Structured Streaming soporta joins entre streams y Datsets/Dataframes estáticos. 

### Stream-Stream Joins
Además, se pueden unir datasets/dataframes de streaming. Hay que tener en cuenta que alguna fila de un dataset se tenga que juntar con otra fila que aún no ha llegado. Por lo que se almacenan las entradas como estados de streams para poder hacer joins de estradas presentes con pasadas. 

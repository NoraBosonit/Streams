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

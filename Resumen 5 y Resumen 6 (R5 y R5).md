# Resumen 5 y 6 | _Spanner: Becoming a SQL System_

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**

## Spanner

Spanner es un sistema de gestión de datos distribuido globalmente que respalda cientos de servicios de misión crítica en Google. Spanner se basa en ideas de las comunidades de sistemas y bases de datos. Google Spanner comenzó como un almacenamiento _llave-valor_ que ofrecía transacciones de varias filas, consistencia externa y tolerancia a errores transparente entre centros de datos. Sin embargo, con el pasar de los años este evolucionó en un sistema de base de datos relaciones, agregando un sistema de esquemas fuertemente tipificados y un procesador de consultas SQL. En la actualidad, Spanner se utiliza ampliamente como sistema de gestión de bases de datos OLTP para datos estructurados en Google, y está disponible públicamente en versión beta como Cloud Spanner en la plataforma en la nube de Google (GCP).

El procesador de consultas Spanner implementa un dialecto de SQL, denominado SQL estándar, que comparten varios subsistemas de consulta dentro de Google (por ejemplo, los sistemas OLAP Dremel/BigQuery). El SQL estándar se basa en el SQL ANSI estándar, y utiliza completamente características estándar como ARRAY y el tipo de fila (llamado STRUCT) para soportar datos anidados como un sistema de consultas de primera clase. El procesador de consultas de Spanner está diseñado para servir a una combinación de cargas de trabajo transaccionales y analíticas, y para soportar tanto la baja latencia como la larga duración de las consultas. de trabajo transaccional y analítico, y para soportar tanto las consultas de baja latencia como las de larga duración. 

## Background

Spanner es un sistema de base de datos relacional fragmentado y georreplicado. La base de datos está fragmentada horizontalmente en filas. Dentro de un centro de datos, los _shards_ se distribuyen en varios servidores. Los _shards_ se replican en múltiples centros de datos separados geográficamente.

Las transacciones de Spanner utilizan un _write-ahead redo log_ replicado y el algoritmo de consenso Paxos se utiliza para que las réplicas se pongan de acuerdo sobre el contenido de cada entrada del registro. En particular, cada _shard_ de la base de datos base de datos se asigna exactamente a un grupo Paxos. Un grupo determinado puede tener asignados varios _shards_. Todas las transacciones que involucran datos en un grupo particular se escriben en un _Paxos write-ahead log_ lo que significa que cada entrada del registro se compromete mediante la replicación exitosa a un quórum de réplicas. Spanner utiliza una forma de Multi-Paxos, en la que se elige un único líder de larga duración y puede hacer commit en múltiples entradas de registro en paralelo para lograr un alto rendimiento. Además el control de concurrencia de Spanner utiliza una combinación de bloqueo pesimista y marcas de tiempo. Para  _blind write_ y de lectura-modificación-escritura de transacciones, el bloqueo estricto de dos fases asegura la serialización dentro de un grupo Paxos dado, mientras que los commits de dos fases (donde diferentes grupos Paxos participan) garantizan la serialización en toda la base de datos. 

A cada _committed transaction_ se le asigna una marca de tiempo, y en cualquier marca de tiempo **T** hay una único _snapshot_ correcto de la base de datos. Las lecturas pueden realizarse en _lock-free snapshot transactions_, y todos los datos devueltos de todas las lecturas en la misma _snapshot transaction_ provienen de un _snapshot_ consistente de la base de datos en una marca de tiempo especificada. Las _Stale reads_ escogen una marca de tiempo en el pasado para aumentar la
probabilidad de que la réplica cercana esté lo suficientemente al día para servir a la lectura. Las réplicas conservan varias versiones de datos anteriores para poder servir lecturas antiguas. Por otro lado, las _Strong reads_ puede que tengan que esperar a que la réplica cercana se ponga al día, o puede reintentar en una réplica más lejana que puede estar más actualizada.

Spanner también provee un _special RPC framework_, el cual es llamado _framework coprocesador_. Este se encarga es esconder gran parte de la complejidad al localizar datos.El framework coprocesador determina qué grupo (o grupos) de Paxos es el propietario de los datos a los que se dirige y encuentra la réplica más cercana de ese grupo que esté suficientemente actualizada para el modo de concurrencia especificado. Los _data shards_ pueden pasar de un grupo Paxos a otro o dividirse en _shards_ más pequeños en nuevos grupos (normalmente por razones de equilibrio de carga). Además, el _framework coprocesador_ oculta una serie de otros fallos transitorios (fallo del servidor, fallos de la red, etc.) reintentando automáticamente. Este mecanismo de reintento resulta complicar considerablemente el procesamiento de las consultas. Una réplica determinada almacena los datos en un sistema de archivos distribuido de sólo apéndice llamado _Colossus_. El almacenamiento se basa en _log-structured merge trees_, que soportan un alto rendimiento de escritura y encajan bien con la naturaleza del sistema de archivos. 

El compilador de consultas Spanner transforma primero una consulta de entrada en un árbol algebraico relacional. A continuación, el compilador optimizador utiliza las propiedades del esquema y de los datos para reescribir el árbol algebraico inicial en un plan de ejecución eficiente mediante reglas de transformación. Internamente, el compilador de consultas utiliza ampliamente los operadores de unión correlacionados, tanto para las transformaciones algebraicas como para los operadores físicos. Los operadores lógicos son de dos tipos: CrossApply y OuterApply. Lógicamente, CrossApply(input, map) evalúa "map" para cada tupla de "input" y concatena los resultados de las evaluaciones de "map". OuterAp ply(input, map) emite una fila de salida incluso si "map" produjo una relación vacía para una fila particular de "input". La compilación de consultas da lugar a planes de ejecución con operadores de distribución que son responsables del envío de subplanes a servidores remotos y de la recopilación de resultados para manejar las ejecuciones particionadas. Tanto el compilador como el tiempo de ejecución intentan realizar agresivamente la poda de particiones para minimizar la cantidad de trabajo realizado por las consultas restringiendo los datos sobre los que operan sin cambiar los resultados.

## Query distribution

El procesador de consulta distribuido de Spanner, que ejecuta el código en paralelo en múltiples máquinas que alojan los datos para servir tanto a las consultas en línea como a las de larga duración. 

### Compilación de consultas distribuidas

El compilador de consultas SQL de Spanner utiliza el enfoque tradicional de construir un árbol de operadores de álgebra relacional y optimizarlo mediante reescrituras equivalentes. La distribución de las consultas se representa mediante operadores en el árbol de álgebra de consultas. Uno de estos operadores de distribución es la _Unión Distribuida_. Se utiliza para enviar una subconsulta a cada _shard_ de los datos persistentes o temporales subyacentes, y para concatenar los resultados. La unión distribuida es una operación fundamental en Spanner, especialmente porque la fragmentación de una tabla puede cambiar durante la ejecución de la consulta, y después de un reinicio de la misma.

### Ejecución distribuida

En tiempo de ejecución, la unión distribuida minimiza la latencia utilizando el marco del coprocesador Spanner para dirigir una petición de subconsulta dirigida a un fragmento a una de las réplicas más cercanas que puedan servir la petición. La poda de _shards_ se utiliza para evitar la consulta de _shards_ irrelevantes. La poda de _shards_ aprovecha las claves de rango de los _shards_ y depende de la imposición de condiciones sobre las claves de _shards_ de las tablas a los escaneos subyacentes. de las tablas a los escaneos que no son necesarios. ADemás la Unión Distribuida reduce la latencia enviando las subconsultas a cada _shard_ o grupo en paralelo. 

### Uniones distribuidas

El _apply join_ es de suma importancia para el funcionamiento de Spanner. Su caso de uso principal es unir un índice secundario y su tabla base distribuida de forma independiente; también se utiliza para ejecutar Inner/Left/Semi-joins con predicados de las claves de la tabla remota. Las uniones de tipo Apply que están correlacionadas con las claves de la tabla funcionan muy bien en un entorno de procesamiento de consultas de una sola máquina en el que la capa de almacenamiento soporta búsquedas. 

Spanner implementa un operador Distributed Apply extendiendo la unión distribuida e implementando el estilo de join Apply de manera batched - como dos joins, un join distribuido que aplica lotes de filas de la entrada a la subconsulta remota, y otro que aplica filas de cada lote a la subconsulta del join original localmente en un shard. La _Distributed apply_ permite a Spanner minimizar el número de llamadas entre máquinas para las uniones basadas en claves y paralelizar la ejecución. 

### APIs de distribución de consultas

Spanner expone dos tipos de API para emitir consultas y consumir resultados. La API de consumidor único se utiliza cuando un solo proceso cliente consume los resultados de una consulta. La API de consumo paralelo se utiliza para consumir los resultados de la consulta en paralelo desde varios procesos que suelen ejecutarse en varias máquinas. Esta API está diseñada para las cadenas de procesamiento de datos y los sistemas de tipo map-reduce que utilizan múltiples máquinas para unir datos de Spanner con datos de otros sistemas o para realizar transformaciones de datos fuera de Spanner.

## Query Range Extraction (Extracción de rangos de consulta)

### Planteamiento del problema

La extracción de rangos de consulta se refiere al proceso de analizar una consulta y determinar qué partes de las tablas son referenciadas por la consulta. Los rangos de las filas referenciadas se expresan como intervalos de valores de la clave primaria. Spanner emplea varios tipos de extracción de rangos:

- **Extracción del rango de distribución:** Saber a qué _table shards_  hace referencia es esencial para dirigir la consulta a los servidores que alojan esos fragmentos. los servidores que alojan esos fragmentos.
- **Extracción del rango de búsqueda:** Una vez que la consulta llega a un servidor Spanner se calcula qué fragmentos del fragmento en cuestión se deben leer de la pila de almacenamiento subyacente.
- **Extracción de rangos de bloqueo:** En este caso, los rangos de claves extraídos determinan qué _table shards_ deben bloquearse (para la concurrencia de transacciones pesimistas), o comprobar las posibles modificaciones pendientes (para las transacciones instantáneas). La extracción de rangos de bloqueo mejora la concurrencia al permitir sustituir un único bloqueo de un rango grande por uno o varios bloqueos _fine-grained_ que cubren un subconjunto del rango grande. La contrapartida aquí es mantener demasiados bloqueos

### Compile-time rewriting (Reescritura en tiempo de compilación)

La implementación de la extracción de rangos en Spanner se basa en dos técnicas principales: En tiempo de compilación, se normaliza y reescribe una expresión de exploración filtrada en un árbol de _correlated self-joins_ que extraen los rangos para las columnas clave sucesivas. En tiempo de ejecución, se utiliza una estructura de datos especial llamada _filter tree_ para calcular los rangos mediante aritmética de intervalos ascendente y para la evaluación eficiente de las condiciones de filtrado posteriores.

### Filter tree (árbol de filtrado)

El árbol de filtrado es una estructura de datos en tiempo de ejecución que se utiliza simultáneamente para extraer los rangos de claves a través de la intersección/unión ascendente de intervalos, y para el post-filtrado de las filas emitidas por las auto-uniones correlacionadas. El árbol de filtrado se comparte entre todos los escaneos correlacionados producidos por la reescritura en tiempo de compilación. El árbol de filtrado memoriza los resultados de los predicados cuyos valores no han cambiado, y depura el cálculo del intervalo.

## Query restarts (Restauración de las consultas)

Spanner compensa automáticamente los fallos, la redistribución y los _binary rollouts_, afectando mínimamente a las latencias de las peticiones. La biblioteca del cliente reanuda de forma transparente la ejecución de las consultas instantáneas si se producen errores transitorios u otros eventos del sistema, incluso cuando los resultados parciales han sido consumidos por el código del usuario. 

### Escenarios de uso y beneficios

**Ocultación de fallos transitorios.** Spanner oculta completamente los fallos transitorios durante la ejecución de la consulta.

**Modelo de programación más sencillo: no hay bucles de reintento.** Los bucles de reintento en el código del cliente de la base de datos son una fuente de errores difíciles de solucionar, ya que escribir un bucle de reintento con un backoff adecuado no es trivial. 

**Paginación en flujo a través de los resultados de la consulta.** Tradicionalmente, las aplicaciones interactivas utilizan la paginación para recuperar los resultados de una sola consulta en porciones que caben en la memoria y constituyen un trozo razonable de trabajo en el lado del cliente o en un nivel intermedio. A menudo, los trabajos de procesamiento por lotes también utilizan la paginación para definir porciones de datos que se procesan en de datos para procesar en una sola toma - para que todos los datos quepan en la memoria o para tener en cuenta los fallos en sistemas no-Spanner de los que utilizan los datos.

**Mejora de la latencia de cola para las solicitudes en línea.** La capacidad de Spanner de ocultar los fallos transitorios y de rehacer una cantidad mínima de trabajo cuando se reinicia después de un fallo ayuda a disminuir la latencia de cola para las solicitudes en línea.

**Avance en las consultas de larga duración.** Para las consultas de larga duración en las que el tiempo total de ejecución de la consulta es comparable al tiempo medio hasta el fallo, es importante tener un entorno de ejecución que garantice el avance en caso de fallos transitorios. 

**Manejo de errores internos de Spanner más sencillo.** Como Spanner utiliza RPCs reiniciables no sólo para las llamadas cliente-servidor, sino también para las llamadas internas servidor-servidor, se simplificó la arquitectura en cuanto al manejo de errores. 

**Reajuste dinámico.** Spanner utiliza reinicios de consultas para continuar la ejecución cuando un servidor pierde la propiedad de un _shard_ o los límites de este.

## Blockwise-Columnar Storage (Almacenamiento de columnas en bloque)

Spanner utilizó originalmente partes significativas del código base de Bigtable, en particular el formato de datos en disco y la caché de bloques. Esto permitió que el desarrollo de Spanner se enfocara sobre todo en distribución, replicación y escalado. El formato de datos SSTable (sorted string table) heredado de Bigtable está optimizado para datos NoSQL sin esquema que consisten principalmente en cadenas grandes. Debido a que Spanner escala tan bien horizontalmente, las SSTables han demostrado ser notablemente robustas incluso cuando se utilizan para datos esquematizados que consisten en gran medida en valores pequeños, a menudo atravesados por columnas. Pero en última instancia, no encajan bien y dejan mucho rendimiento sobre sobre la mesa.

Ressi es el nuevo formato de almacenamiento de bajo nivel para Spanner. Está diseñado desde el principio para manejar consultas SQL en bases de datos distribuidas a gran escala que comprenden tanto cargas de trabajo OLTP como OLAP. Ressi almacena una base de datos como un árbol LSM, cuyas capas se compactan periódicamente. Dentro de cada capa, Ressi organiza los datos en bloques en orden mayor de fila, pero dispone los datos dentro de un bloque en orden mayor de columna (esencialmente, la disposición PAX). Internamente, la estructura de datos fundamental de Ressi es el vector, que es una secuencia indexada ordinalmente de valores de tipo homogéneo.




[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [dill]: <https://github.com/joemccann/dillinger>
   [git-repo-url]: <https://github.com/joemccann/dillinger.git>
   [john gruber]: <http://daringfireball.net>
   [df1]: <http://daringfireball.net/projects/markdown/>
   [markdown-it]: <https://github.com/markdown-it/markdown-it>
   [Ace Editor]: <http://ace.ajax.org>
   [node.js]: <http://nodejs.org>
   [Twitter Bootstrap]: <http://twitter.github.com/bootstrap/>
   [jQuery]: <http://jquery.com>
   [@tjholowaychuk]: <http://twitter.com/tjholowaychuk>
   [express]: <http://expressjs.com>
   [AngularJS]: <http://angularjs.org>
   [Gulp]: <http://gulpjs.com>

   [PlDb]: <https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md>
   [PlGh]: <https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md>
   [PlGd]: <https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md>
   [PlOd]: <https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md>
   [PlMe]: <https://github.com/joemccann/dillinger/tree/master/plugins/medium/README.md>
   [PlGa]: <https://github.com/RahulHP/dillinger/blob/master/plugins/googleanalytics/README.md>

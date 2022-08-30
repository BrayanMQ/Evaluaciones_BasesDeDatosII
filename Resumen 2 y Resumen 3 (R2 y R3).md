# Resumen 2 y 3 (R2 y R3) | _Bigtable: A Distributed Storage System for Structured Data_

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**

## 1 Concepto de Bigtable 
Bigtable es un sistema de almacenamiento distribuido para la gestión de datos estructurados que está diseñado para escalar de forma fiable a petabytes de datos y miles de máquinas. Este sistema cuenta con varios logros como lo son una amplia aplicabilidad, escalabilidad, alto rendimiento y alta disponibilidad. Este no soporta un modelo de datos relacional completo; en lugar de ello, este provee a los clientes un modelo simple que soporta control dinámico sobre la disposición y el formato de los datos. 

## 2 Modelo de datos
Una **Bigtable** es un mapa ordenado multidimensional disperso, distribuido y persistente. El mapa está indexado por una "row key" y una "column key" y una marca de tiempo o "timestamp"; cada valor del mapa es un arreglo de bytes no interpretado.

### Filas
Las "row keys" de una tabla son cadenas arbitrarias. Cada lectura o escritura de datos bajo una "row key" es atómica, una decisión de diseño que facilita a los clientes el razonamiento sobre el comportamiento del sistema en presencia de actualizaciones concurrentes de la misma fila. 

Los **Bigtable** mantienen los datos en orden lexicográfico por "row key". El rango de filas o "row range" de una tabla se particiona dinámicamente. Cada rango de filas se denomina "tablet", que es la unidad de distribución y equilibrio de carga.

### Columnas
Las "Column keys" se agrupan en conjuntos denominados familias de columnas, que constituyen la unidad básica del control de acceso. Todos los datos almacenados en una familia de columnas suelen ser del mismo tipo. A familia de columnas debe ser creada antes de que los datos puedan ser almacenados bajo cualquier clave de columna en esa familia.

Una "column key" se nombra utilizando la siguiente sintaxis **_familia:calificador_**. Los nombres de las familias de columnas deben ser imprimibles, pero los calificadores pueden ser cadenas arbitrarias. Además, el control de acceso y la contabilidad de disco y memoria se realizan a nivel de familia de columnas.

### Marcas de tiempo o timestamps

Cada celda de una Bigtable puede contener varias versiones de los mismos datos; estas versiones se indexan por marcas de tiempo. Las marcas de tiempo de Bigtable son enteros de 64 bits. Pueden ser asignados por el Bigtable, en cuyo caso representan el "tiempo real" en microsegundos, o ser asignados explícitamente por la aplicación cliente.

## 3 API

La API de Bigtable proporciona funciones para crear y eliminar tablas y familias de columnas. También proporciona funciones para cambiar los metadatos de clústeres, tablas y familias de columnas, como los derechos de control de acceso.

## 4 Bloques de construcción

El formato de archivo SSTable de Google se utiliza internamente para almacenar datos de Bigtable. SSTable proporciona un mapa persistente ordenado e inmutable de claves a valores, donde tanto las claves y valores son cadenas de bytes arbitrarias. Internamente, cada SSTable contiene una secuencia de bloques. Un "block index" (almacenado al final de la SSTable) se utiliza para localizar los bloques; el índice se carga en la memoria cuando se abre la SSTable. 

### Chubby 

Bigtable se basa en un servicio de bloqueo distribuido, altamente disponible y persistente llamado Chubby. Un servicio Chubby consiste en cinco réplicas activas, una de las cuales es elegido para ser el maestro y servir activamente las solicitudes. El servicio servicio está activo cuando la mayoría de las réplicas están y pueden comunicarse entre sí. Chubby utiliza el algoritmo Paxos para mantener la coherencia de sus réplicas frente a los fallos.

Chubby proporciona un "namespace" que consiste en directorios y pequeños archivos. Cada directorio o archivo puede ser utilizado como un bloqueo, y las lecturas y escrituras en un archivo son atómicas

#### Usos de Chubby 

Bigtable utiliza Chubby para una variedad de tareas: 

- Para asegurar que hay como máximo un maestro activo en cualquier momento.
- Para almacenar la ubicación de arranque de los datos de Bigtable.
- Para descubrir "tablet servers" y finalizarlos.
- Para lamacenar el esquema de información del Bigtable.
- Para guardar las listas de control de acceso.


## 5 Implementación

La implementación de Bigtable tiene tres componentes principales: una biblioteca que se enlaza en cada cliente, un servidor maestro y muchos "tablet servers". Los "Tablet Servers" pueden añadirse (o eliminarse) dinámicamente de un clúster para adaptarse a los cambios en las cargas de trabajo. 

El maestro es responsable de asignar las Tablets a los "Tablet Servers", detectar la adición y la expiración de los Tablet equilibrar la carga de los servidores de tabletas, y la recolección de basura de los archivos en GFS. 
Cada "Tablet Server" gestiona un conjunto de "Tablets" (normalmente tenemos entre diez y mil tabletas por servidor de tabletas). El "Tablet Server" gestiona las solicitudes de lectura y escritura en las tabletas que ha cargado, y también divide las tabletas que han crecido demasiado. 

Un cluster de Bigtable almacena un número de tablas. Cada tabla consiste en un conjunto de tablets, y cada tablet contiene todos los datos asociados a un rango de filas. Inicialmente, cada tabla consiste en una sola tableta. A medida que una tabla crece, se divide automáticamente en múltiples tabletas, cada una de aproximadamente 100-200 MB por defecto.

### 5.1 Ubicación de "tablet"

El primer nivel es un archivo almacenado en Chubby que contiene la ubicación de la "root tablet". La "root tablet" contiene la ubicación de todas las "tablets" en una tabla especial de METADATA.
Cada tabla METADATA contiene la ubicación de un conjunto de "user tablets". La "root tablet" es sólo la primera tableta de la tabla METADATA, pero recibe un tratamiento especial (nunca se divide) para garantizar que la jerarquía de localización de las "tablets" no tenga más de tres niveles.

### 5.2 Asignación de tablet

Cada "tablet" se asigna a un "tablet server" a la vez. El maestro mantiene un registro del conjunto de "tablet servers" activos, y la asignación actual de "tablets" a los "tablet servers", incluyendo las "tablets" que están sin asignar. Cuando una "tablet" está sin asignar, y hay un "tablet server" para espacio suficiente para la "tablet", el maestro la asigna enviando una solicitud de carga de "tablets" al "tablet server".

### 5.3 Tablet serving

El estado persistente de una "tablet" se almacena en GFS. Las actualizaciones se guardan en un "commit log" que almacena los "redo records". De estas actualizaciones, las más recientes se almacenan en memoria en un buffer ordenado llamado _memtable_; las actualizaciones más antiguas se almacenan en unasecuencia de SSTables. Para recuperar una "tablet", un "tablet server" lee sus metadatos de la tabla METADATA. Estos metadatos contienen la lista de SSTables que componen una "tablet" y un conjunto de "redo points", que son punteros a cualquier "commit logs" que puedan contener datos para la "tablet". El servidorr lee los índices de las SSTables en memoria y reconstruye la _memtable_ aplicando todas las actualizaciones que se han confirmado desde los "redo points".

### 5.4 Compactaciones

A medida que se ejecutan las operaciones de escritura, el tamaño de la _memtable_ aumenta. Cuando el tamaño de la _memtable_ alcanza un tope, la _memtable_ se congela, se crea una nueva _memtable_ y la _memtable_ congelada se convierte en una SSTable y se escribe en GFS. Este proceso de compactación menor tiene dos objetivos: reducir el uso de memoria del "tablet" y reducir la cantidad de datos que hay que leer del "commit log" durante la recuperación si este servidor muere. Las operaciones de lectura y escritura entrantes pueden continuar mientras se produce la compactación.

## 6 Refinamientos o mejoras

La implementación descrita en la sección anterior requirió una serie de refinamientos para lograr el alto rendimiento, la disponibilidad y la fiabilidad requerida por los usuarios. A continuación se listan en más detalle.

- **Locality groups:** Los clientes pueden agrupar varias familias de columnas en un _locality group_. Se genera una SSTable separada para cada _locality group_ en cada tableta. Segregar las familias de columnas a las que no se suele acceder conjuntamente en grupos de localidad separados permite realizar lecturas más eficientes.
- **Compresión:**  Los clientes pueden controlar si las SSTables de un _locality group_ están comprimidas, y si es así, qué formato de compresión se utiliza. El formato de compresión especificado por el usuario se aplica a cada bloque SSTable (cuyo tamaño es controlable mediante un parámetro de ajuste específico del grupo de localidad). Aunque se pierde algo de espacio al comprimir cada bloque por separado, nos beneficiamos en que se pueden leer pequeñas porciones de un SSTable sin descomprimir todo el archivo. Se suele utilizar un esquema de compresión personalizado que da una doble pasada. En la primera se usa el esquema de Bentkley y Mcllroy, el cual comprime largas cadenas comunes a través de una gran ventana.La segunda pasada utiliza un algoritmo de compresión rápida que busca repeticiones en una pequeña ventana de 16 KB de los datos.
- **Caching for read performance:** Para mejorar el rendimiento de lectura, los "tablet servers" utilizan dos niveles de caché. La caché de exploración es una caché de nivel superior que almacena en caché los pares clave-valor devueltos por la interfaz SSTable al código del servidor de la "tablet". La caché de bloques es una caché de nivel inferior que almacena en caché los bloques de SSTables que se leídos desde GFS.
- **Bloom filters:** Reducimos el número de accesos permitiendo a los clientes especificar que los filtros Bloom. Un filtro Bloom nos permite preguntar si una SSTable puede contener algún dato para un par fila/columna especificado. Estos filtros permiten que en ciertas aplicaciones se reduzca drásticamente el número de búsquedas de disco necesarias para las operaciones de lectura. El uso de los filtros Bloom también implica que la mayoría de las búsquedas de filas o columnas inexistentes columnas no necesitan tocar el disco.
- **Commit-log implementation:** Añadimos las mutaciones a un "single commit log" por servidor de "tablets", mezclando las mutaciones de las diferentes "tablets" en el mismo archivo físico del "log file". El uso de un log proporciona importantes ventajas de rendimiento durante el funcionamiento normal, pero complica la recuperación.
- **Speeding up tablet recovery:** Si el maestro mueve una "tablet" de un servidor de "tablets" a otro, el " source tablet server" realiza primero una compactación menor en esa "tablet". Esta compactación reduce el tiempo de recuperación al reducir la cantidad de estado no compactado en el "commit log" del "tablet server". Una vez terminada esta compactación, el "tablet server" deja de servirla. Antes de descargar la "tablet", el "tablet server" realiza otra compactación menor (normalmente muy rápida) para eliminar cualquier estado restante no compactado en el " tablet server’s log" que haya llegado mientras se realizaba la primera compactación menor. Después de esta segunda compactación menor se completa, la "tablet" puede ser cargada en otro "tablet server "sin necesidad de recuperar las entradas del registro.
- **Exploiting immutability:** Dado que las SSTables son inmutables, el problema de eliminar permanentemente los datos borrados se transforma en la recogida de basura de las SSTables obsoletas. Las SSTables de cada "tablet" se registran en la tabla METADATA. El maestro elimina las SSTables obsoletas como un "mark-and-sweep garbage collection" sobre el conjunto de SSTables, donde la tabla METADATA contiene el conjunto de "roots". Por último, la inmutabilidad de SSTables nos permite dividir las "tablets" rápidamente. En lugar de generar un nuevo conjunto de SSTables para cada "tablet" hija, dejamos que las "tablets" hijas compartan las SSTables de la "tablets" madre.


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

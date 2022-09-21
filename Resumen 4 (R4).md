# Resumen 4 | _Schema-Agnostic Indexing with Azure DocumentDB_

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**

## 1 Azure DocumentDB
Azure DocumentDB es el servicio de base de datos distribuida de tendencia múltiple de Microsoft para gestionar documentos JSON a escala Internet 

### 1.1 Resumen de las capacidades
DocumentDB se basa en el modelo de datos JSON y el lenguaje JavaScript directamente en su motor de base de datos.

- El lenguaje de consulta admite una gran cantidad de consultas relacionales y jerárquicas. Está arraigado en el sistema de tipos de JavaScript, la evaluación de expresiones y el modelo de invocación de funciones.
- El motor de la base de datos está optimizado para servir consultas consistentes ante un volumen elevado y sostenido de escrituras de documentos. Por defecto, el motor de la base de datos indexa automáticamente todos los documentos sin necesidad de esquemas o índices secundarios de los desarrolladores.
- Ejecución transaccional de la lógica de la aplicación proporcionada a través de procedimientos almacenados y triggers, creados completamente en JavaScript y ejecutada directamente dentro del motor de base de datos de DocumentDB.
- Como sistema de base de datos geo-distribuido, DocumentDB ofrece niveles de consistencia bien definidos y ajustables para que los desarrolladores puedan elegir (fuerte, acotado-estado, sesión y eventual) y las correspondientes garantías de rendimiento.
- Como servicio de base de datos en la nube totalmente gestionado y con tenencia múltiple, toda la gestión de máquinas y recursos se abstrae de los usuarios. 

### 1.2 Modelo de recursos
Un inquilino de DocumentDB comienza por aprovisionar una cuenta de base de datos (utilizando una suscripción a Azure). Una cuenta de base de datos gestiona una o varias bases de datos DocumentDB. Una base de datos DocumentDB gestiona a su vez un conjunto de entidades: usuarios, permisos y colecciones. Una colección DocumentDB es un contenedor agnóstico del esquema de documentos generados por el usuario. Además de los documentos, una colección colección DocumentDB también gestiona procedimientos almacenados, triggers funciones definidas por el usuario (UDF) y archivos adjuntos. Las entidades que se encuentren bajo una cuenta de un inquilino (usuarios, colecciones, documentos, etc.) son referidos como _recursos_. Cada recurso está identificado de forma única por una URI estable y lógica y se representa como un documento JSON. Los desarrolladores pueden interactuar con los recursos a través de HTTP (y a través de un protocolo TCP sin estado) utilizando los verbos HTTP estándar estándar para CRUD (create, read, update y delete), consultas y procedimientos almacenados.

### 1.3 Topología del sistema
Desplegamos y gestionamos el servicio DocumentDB en clusters de máquinas, cada una de ellas con discos SSD locales dedicados. Una vez desplegado, el servicio DocumentDB se manifiesta como una red superpuesta de máquinas, denominada federación, que abarca uno o más clusters. Cada máquina alberga réplicas correspondientes a varias particiones de recursos dentro de un conjunto fijo de procesos. Las réplicas correspondientes a las particiones de recursos se colocan y equilibran la carga entre las máquinas de la federación. Cada réplica aloja una instancia del motor de base de datos de DocumentDB que gestiona los recursos (por ejemplo, los documentos), así como el índice asociado

### 1.4 Objetivos de diseño para la indexación
- Indexación automática
- Compensaciones configurables de almacenamiento/rendimiento
- Consultas jerárquicas y relacionales eficaces y ricas
- Consultas coherentes ante un volumen sostenido de escrituras de documentos 
- Tenencia múltiple


## 2 Indexación agnóstica de esquemas

### 2.1 No esquema, no problema
Con el objetivo de eliminar el desajuste de impedancia entre la base de datos y los modelos de programación de aplicaciones, DocumentDB explota la simplicidad de JSON y su falta de especificación de esquema. No hace suposiciones sobre los documentos y permite que los documentos dentro de una colección de DocumentDB varíen en el esquema, además de los valores específicos de la instancia. A diferencia de otras bases de datos de documentos, el motor de base de datos de DocumentDB opera directamente en el nivel de la gramática JSON, permaneciendo agnóstica el concepto de esquema de documentos y difuminando la frontera entre la estructura y los valores de instancia de los documentos. Esto, a su vez, le permite indexar automáticamente los documentos sin requerir esquema o índices secundarios. 

### 2.2 Documentos como árboles

La técnica que ayuda a difuminar la frontera entre el esquema de los documentos JSON y sus valores de instancia es la representación de los documentos como árboles. La representación de documentos JSON como árboles normaliza tanto la estructura como los valores de instancia de los documentos en un concepto unificador de una estructura de ruta codificada dinámicamente codificada dinámicamente

### 2.3 Índice como documento

Con la indexación automática, cada ruta en un árbol de documentos es indexado. Además, cada actualización de un documento a una colección de DocumentDB conlleva la actualización de la estructura del índice. Existen dos posibles mapeos del documento y las rutas: (a) _mapeo de índice hacia adelante (forward index mapping)_, que mantiene un mapa de tuplas (id de documento, ruta) y (b) _mapeo de índice invertido (inverted index mapping)_, que mantiene un mapa de tuplas (ruta, id de documento). Dado que el lenguaje de consulta de DocumentDB opera sobre rutas de los árboles de documentos, el índice invertido es una representación muy eficiente.  

### 2.4 Consultas DocumentDB

Un aspecto único de las consultas de DocumentDB es que operan directamente con la representación de árbol (en lugar de filas y columnas en una tabla relacional). ESto permite referirse a las propiedades de los documentos JSON a cualquier profundidad arbitraria, incluidas las rutas comodín.

## 3 Organización del índice lógico

El índice es la unión de todos los documentos y también se representa como un árbol. Cada nodo del árbol de índices contiene una lista de identificadores de los documentos correspondientes que contienen la etiqueta dada dada. La representación en árbol de los documentos y el índice permite un motor de base de datos agnóstico al esquema. Para que la persistencia en disco sea rentable, el árbol de índices debe convertirse en una representación eficiente de almacenamiento. La representación lógica del índice puede verse como un conjunto ordenado de tuplas clave-valor, cada una de las cuales se denomina _index entry_. La _key_ consiste en un término que representa la información codificada de la ruta del nodo en el árbol de índices, y un PES (posting entry selector) que ayuda a dividir las contabilizaciones horizontalmente. El valor consiste en lista de publicaciones que representan colectivamente los documentos codificados (o fragmentos de documentos) codificados.

![index](https://www.dropbox.com/s/39r67gfeje5n28o/resumen4.png?dl=1)

## 4 Organización del índice físico

Se concluyó que realizar una extensión del BwTree permitiría cumplir los requisitos establecidos. El BwTree utiliza actualizaciones en memoria libres de latch y almacenamiento estructurado de logs para la persistencia. Este aprovecha dos tendencias del hardware moderno: (i) los procesadores multinúcleo con una jerarquía de memoria/caché de varios niveles, y (ii) las unidades SSD basadas en memoria flash con lecturas aleatorias rápidas. 

La propiedad _latch-free_ garantiza que los hilos no se bloqueen y que los lectores no entren en conflicto con los escritores no se bloqueen y que los lectores no entren en conflicto con los escritores alto grado de concurrencia. La organización del _log-structured_ de Bwtree está diseñada para evitar el rendimiento ineficiente de la escritura aleatoria en flash y es adecuada también para los discos duros. Esta técnica es similar a la propuesta para los sistemas de archivos, pero con una diferencia crucial: a diferencia de los sistemas de archivos estructurados en logs, el BwTree desacopla completamente las páginas lógicas de sus contrapartes físicas. 

Esto permite numerosas optimizaciones, incluida la reducción de la amplificación de la escritura. Para proporcionar escrituras rápidas sostenidas para DocumentDB, el árbol Bw se amplió para soportar _blind incremental updates_, lo que permite que el motor de la base de datos de DocumentDB utilice todo el ancho de banda de escritura del almacenamiento para las actualizaciones del índice. 


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

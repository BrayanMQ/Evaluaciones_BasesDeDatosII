# Prueba Corta # 9

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**

1. Suponiendo que un sistema de bases de datos relacional que presenta un read-heavy workload y los queries son muy diferentes, explique detalladamente ¿porque el uso de caches puede afectar el rendimiento del sistema de forma negativa? (30 pts)

    El uso de caches puede afectar el rendimiento del sistema de forma negativa debido a que un cache agrega latencia, es decir, un salto más en el camino. Cuando existe un cache entre la app y la base de datos, la primera consulta se realiza en la cache, en caso de no encontrar los datos allí, se realiza la petición a la base de datos. El resultado de esto es una latencia adicional a un camino que ya era lento deporsí. 
    
    Se puede afirmar que cuando todo el conjunto de datos cabe en la cache, la latencia adicional no entra en juego, pero la mayoría de las veces hay más de una carga de trabajo/patrón que llega a la base de datos y parte de ella llevará el coste del salto adicional. En este caso, al existir varios queries distintos y un workload read-heavy la latencia entrará en juego, pues no todos los datos cabrán siempre en la cache.

2. El particionamiento de tablas en bases de datos relacionales es un concepto muy parecido al de shards en bases de datos NoSQL, explique detalladamente ¿Cómo afecta el particionamiento y el sharding en el rendimiento de bases de datos SQL y
NoSQL? (30 pts)

    El particionamiento y el sharding son opciones muy útiles para mejorar el rendimiento en bases de datos. En el caso del particionamiento en bases de datos SQL, este permite que las consultas sean más eficientes y rápidas al permitir la opción de aplicar la consulta solo a la partición más relevante de los datos de la tabla. En el caso de sharding en NoSQL es muy parecido, pues este permite poder distribuir la información entre varias filas y columna en instancias separadas, dando así paso a poder distribuir el _trafic load_ .

3. En un sistema de bases de datos con Strong Consistency cuyo workload es de read-heavy y write-heavy, ¿Cómo afectan los exclusive locks el rendimiento de las bases de datos NoSQL? (20 pts)

    Si se tiene una base de datos NoSQL con Strong Consistency, es decir, en la que se obliga a que el usuario únicamente pueda realizar lecturas cuando se tiene la última versión de los datos, además, cuyo workload de read-heavy y write-heavy, lo cual implicaría que esta tiene una cantidad grande de lecturas y escrituras, un _exlusive lock_ haría que el el rendimiento caiga, pues este realiza un bloqueo de los datos con el fin de prevenir que estos puedan ser modificados por otras transacciones que estén ocurriendo. Es decir, se estarían realizando locks de manera constante y debido al workload, el rendimiento se vería sumamente impactado. 

4. Explique detalladamente, ¿Cómo afecta la selección de discos físicos el rendimiento de una base de datos SQL y NoSQL? (20 pts)

    Con respecto a la selección de discos físicos en bases de datos SQL y NoSQL, se deben tomar en cuenta dos características, la velocidad del disco y la tasa de transferencia. La velocidad del disco nos indicará con qué rapidez se ejecutarán todas las operaciones de lectura y escritura y la tasa de transferencia del disco nos especificará cuál es la cantidad de datos que se pueden escribir en disco durante una unidad de tiempo (normalmente es medida en segundos). Por lo tanto, mientras más rápido sea el disco, mayor será la cantidad de datos que puedan procesarse. Además, más discos son generalmente mejores que un solo disco cuando muchos usuarios están utilizando el sistema de base de datos simultáneamente (en este caso, el acceso a los datos suele repartirse entre muchos discos, lo que mejora el rendimiento general del sistema).

# Referencias

Bentz B. (2021, 14 enero). How Table Partitioning in SQL Server Improves Query Performance | IT Briefcase. IT Briefcase - IT Briefcase is a targeted online publication that attracts qualified business and IT professionals who are actively researching business integration solutions. Some of the topics we cover include BI, BPM, Cloud Computing, Data Storage, Health IT and Open Source. A full list of the topics we cover can be found on the right hand side of our website. https://www.itbriefcase.net/how-table-partitioning-in-sql-server-improves-query-performance-explained-in-legos

Pan, T. J. (2022, 14 marzo). How sharding a database can make it faster. Stack Overflow Blog. https://stackoverflow.blog/2022/03/14/how-sharding-a-database-can-make-it-faster/

Exclusive locks. (s.f.). (C) Copyright 2005. https://docs.oracle.com/javadb/10.8.3.0/devguide/cdevconcepts842279.html

Contributor, S. (2014, 23 octubre). How System Resources Affect SQL Server Performance. SolarWinds. https://logicalread.com/system-resources-affect-sql-server-performance-mc03/

Laor, D. (2022, 28 septiembre). 7 Reasons Not to Put a Cache in Front of Your Database. ScyllaDB. https://www.scylladb.com/2017/07/31/database-caches-not-good/

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

# Prueba Corta # 1

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**

1. Explique en qué consisten los siguientes conceptos: 
    -  **Data Warehouse:** Es un repositorio central de información procedente de una o varias fuentes de datos.
    -  **Data Lake:** Un data lake es una modalidad nueva y cada vez más popular de almacenar y analizar datos porque permite a las empresas administrar múltiples tipos de datos de una amplia variedad de fuentes, y almacenar estos datos, estructurados y no estructurados, en un repositorio centralizado.
    -  **Data Mart:** Un data mart es una forma sencilla de almacén de datos centrada en un área funcional o materia específica.
    
2. ¿De qué forma se benefician las aplicaciones del uso de Columnar Storage? 

    Las aplicaciones que utilizan el "Columnnar Storage" se benefician a través de un rápido rendimiento de consulta (Querys) y de E/S (Entrada y salida) para prácticamente cualquier tamaño de datos. Esto se logra debido a que el "Columnnar Storage" permite la organización de cada columna en su propio conjunto de bloques físicos en lugar de empaquetar todas las filas en un bloque. Esta funcionalidad les permite ser más eficientes en la entrada/salida (E/S) para las consultas de sólo lectura, ya que sólo tienen que leer del disco (o de la memoria) las columnas a las que accede una consulta.

3. ¿En qué consiste streaming y batch processing?
    
    El streaming y el batch processing consisten en flujos de trabajo para procesar los datos en un bruto de un almacén de datos. El streaming se destaca por procesar los datos de forma secuencial e incremental, registro a registro o en ventanas de tiempo deslizante. Usar esos datos procesados para una amplia variedad de análisis, correlaciones, agregaciones, filtrado y muestreo es lo que se llama "Streaming processing" o "real-time processing".

    Por otro lado, el batch processing tiene varias formas de procesar los datos:
    - **Extract Transform Load (ETL):** Consiste en el proceso de extraer datos de múltiples fuentes para cargarlos en sistemas de almacenamiento de datos.
    - **Extract Load Transform (ELT):** Es una variante del anterior, donde los datos extraídos datos se cargan primero en el sistema de destino.
    - **Online Analytical Processing (OLAP):** Los sistemas OLAP almacenan datos históricos agregados en esquemas multidimensionales. Los sistemas OLAP, que se utilizan ampliamente para realizar consultas, informes y análisis, permiten extraer datos y detectar tendencias en múltiples dimensiones

4. ¿En qué consiste datos estructurados, semi estructurados y no estructurados? 

    - **Datos estructurados:** Los datos estructurados son información que ha sido formateada y transformada en un modelo de datos bien definido.
    
    - **Datos semiestructurados:** Los datos semiestructurados son un tipo de datos que tienen algunas características consistentes y definidas, además que no se limitan a una estructura rígida como la necesaria para las bases de datos relacionales.
    
    - **Datos no estructurados:** Se definen como datos presentes en forma absoluta sin procesar, es decir, datos en crudo. Son difíciles de procesar debido a su compleja organización y formato

# Bibliografía

Amazon Web Services, Inc. (2022, enero). Data Warehousing on AWS. awsstatic. Recuperado 26 de agosto de 2022, de https://d1.awsstatic.com/whitepapers/enterprise-data-warehousing-on-aws.pdf

Amazon Web Services, Inc. (s. f.). Lago de datos | Implementaciones | Soluciones de AWS. Recuperado 26 de agosto de 2022, de https://aws.amazon.com/es/solutions/implementations/data-lake-solution/

Naeem, T. (2022, 29 marzo). Comprensión de datos estructurados, no estructurados y semiestructurados. Astera. Recuperado 26 de agosto de 2022, de https://www.astera.com/es/type/blog/structured-semi-structured-and-unstructured-data/


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

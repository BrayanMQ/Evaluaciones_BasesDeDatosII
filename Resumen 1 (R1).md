# Resumen 1 (R1) | _Data Warehousing on AWS_

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**

## Introduction

Los datos son el activo más valioso de una empresa. Una empresa para alimentar la innovación busca lo siguiente: 

- Almacenar todos los datos relevantes de su negocio
- Dar acceso a los datos a todo aquel que los necesite
- Tener la capacidad de analizar los datos de diferentes maneras
- Destilar los datos para obtener información.

La mayoría de las grandes empresas utilizan "Data warehouses" (almaneces de datos)  sobre todo para elaborar informes y análisis. Para ello se utilizan diversas fuentes como sus propios sistemas de procesamiento de transacciones y otras bases de datos. Las arquitecturas de los "Data warehouses" y sus instalaciones plantean grandes retos como los siguientes: 

- Son difíciles de escalar y tienen largos plazos para la adquisición de hardware
y actualizaciones de hardware.
- Tienen elevados costes generales de administración.
- No pueden separar los datos fríos (de uso infrecuente) de los calientes (de uso frecuente), lo que provoca un aumento de los costes y un desaprovechamiento de la capacidad.
- Limitan el número de usuarios y la cantidad de datos accesibles, lo que conduce a la antidemocratización de los datos.


## Introducing Amazon Redshift

En el pasado, cuando las empresas se encontraban con problemas respecto al tamaño de los volúmenes de datos o cuando querían poner a disposición de más usuarios los análisis y los informes tenían que elegir entre aceptar un rendimiento lento de las consultas o invertir tiempo y esfuerzo en un costoso proceso de actualización. Es entonces donde aparecen los "Cloud data warehouses", como "Amazon Redshift", los cuales cambiaron la forma de pensar de las empresas con respecto al almacenamiento de los datos, pues se redujo drásticamente el costo y el esfuerzo de la implementación de "Data warehouses" sin comprometer las características, el escalado y el desempeño. 

**"Amazon Redshift"** se define como una solución de "warehousing" (almacenamiento de datos) rápida, totalmente administrada y a escala de petabytes, que simplifica y rentabiliza el análisis de grandes volúmenes de datos mediante las herramientas de inteligencia empresarias existentes (BI - Business Intelligence). Con Amazon Redshift, se puede obtener el desempeño de los motores de almacenamiento de datos en columnas que realizan un procesamiento paralelo masivo (MPP) a una décima parte del costo. Desde su lanzamiento en 2013, **"Amazon Redshift"** ha sido uno de los servicios AWS que más ha crecido, con clientes en muchos sectores y empresas como NTT DOCOMO, FINRA, Johnson & Johnson, McDonalds, entre otros.

<div style="page-break-after: always;"></div>

## Modern Analytics and Data Warehousing Architecture

 Los usuarios, incluidos los científicos de datos, los analistas empresariales y los responsables de la toma de decisiones, acceden a los datos de un "Data warehouse" que llegan desde sistemas transaccionales y bases de datos relaciones a través de herramientas de BI, clientes SQL y otras herramientas una vez estos son procesados y transformados. Entonces ¿por qué no ejecutar las consultas directamente en las OLTP (online transaction processing database) donde se registran las transacciones? Para responder las preguntas se deben ver las diferencias entre los "Data warehouses" y las bases de datos OLTP:
 
- Los almacenes de datos están optimizados para las operaciones de escritura por lotes y la lectura de grandes volúmenes de datos.
- Las bases de datos OLTP están optimizadas para operaciones de escritura continuas y altos volúmenes de pequeñas operaciones de lectura.
- Los almacenes de datos suelen emplear esquemas desnormalizados, como el esquema Star y el esquema Snowflake, debido a los requisitos de alto rendimiento de los datos, mientras que las bases de datos OLTP emplean esquemas altamente normalizados, que son más adecuados para los requisitos de alto rendimiento de las transacciones.

Para sacar provecho de utilizar un "Data warehouse" gestionado como un almacén de datos independiente junto a una base de datos OLTP u otro sistema de origen, se recomienda crear un canalización de datos eficiente.

## Data Warehouse Technology Options

Opciones disponibles para construir un "Data Warehouse": bases de datos orientadas a filas, bases de datos orientadas a columnas y arquitecturas de procesamiento paralelo masivo.

### Bases de datos orientadas a filas

Alamacena filas enteras en un bloque físico. Se consigue un alto rendimiento en las operaciones de lectura por los índices secundarios. Algunos ejemplos son "Oracle Database Server", "Microsoft SQL Server", "MySQL" y "PostgreSQL". Estos son más adecuados para el procesamiento transaccional (OLTP) que para el análisis. Se encuentran limitados por los recursos disponibles de una sola máquina.

### Bases de datos orientadas a columnas

Las bases de datos orientadas a columnas organizan cada columna en su propio conjunto de bloques físicos en lugar de empaquetar todas las filas en un bloque. Esta funcionalidad les permite ser más eficientes en la entrada/salida (E/S) para las consultas de sólo lectura, ya que sólo tienen que leer del disco (o de la memoria) las columnas a las que accede una consulta. Este enfoque hace que las bases de datos orientadas a columnas sean una mejor opción que las orientadas a filas para el "data warehousing". Algunas bases de datos orientadas a columnas que se utilizan para el "Data warehousing" son Amazon Redshift, Vertica, Greenplum, Teradata Aster, Netezza y Druid.

<div style="page-break-after: always;"></div>

### Arquitecturas de procesamiento paralelo masivo (MPP)

Una arquitectura MPP permite utilizar todos los recursos disponibles en el clúster para procesar los datos, lo que aumenta drásticamente el rendimiento de los "Data warehouses" a escala de petabytes. Los almacenes de datos MPP permiten mejorar el rendimiento simplemente añadiendo más nodos al clúster. Amazon Redshift, Druid, Vertica, Greenplum y Teradata
Aster son algunos de los almacenes de datos construidos sobre una arquitectura MPP.

## Amazon Redshift Deep Dive

Como tecnología MPP columnar, Amazon Redshift ofrece beneficios clave para el almacenamiento de datos rentable y de alto rendimiento, como la compresión eficiente, la reducción de E/S y los menores requisitos de almacenamiento. Se basa en ANSI SQL. Amazon Redshift ofrece un rápido desempeño de consulta y E/S para prácticamente cualquier tamaño de datos mediante almacenamiento en columnas y mediante la paralelización y la distribución de las consultas en varios nodos.

## Operations 
### Ideal Usage Patterns

Amazon Redshift es ideal para OLAP utilizando sus herramientas de inteligencia empresarial existentes. Las empresas utilizan Amazon Redshift para hacer lo siguiente: 

- Ejecución de informes y BI empresarial
- Analizar los datos de ventas globales de múltiples productos
- Almacenar datos históricos de comercio de acciones
- Analizar las impresiones y los clics de los anuncios
- Agregar datos de juegos de azar
- Analizar las tendencias sociales
- Medir la calidad clínica, la eficiencia operativa y el rendimiento financiero en la atención sanitaria 

### Anti-Patterns

Amazon Redshift no es ideal para los siguientes patrones de uso:

- **OLTP** – Amazon Redshift está diseñado para cargas de trabajo de almacenamiento de datos que ofrecen capacidades analíticas extremadamente rápidas y económicas, no para ser un rápido sistema transaccional. 
- **Unstructured data** – Data in Amazon Redshift must be structured by a defined schema.
- **Datos BLOB** – Si planea almacenar archivos de objetos grandes binarios (BLOB) como vídeo digital, imágenes o música, es posible que desee almacenar los datos en S3 (Amazon simple Storage Service) y hacer referencia a su ubicación en Amazon Redshift.

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

# Apuntes - Semana 3 - Martes 9 de agosto

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**

## Ciclo de Fetch

Consiste en el periodo de tiempo en el que el CPU ejecuta una instrucción en lenguaje máquina.

### Algunos conceptos
- **ALU:** Unidad aritmética lógica. Se encarga de ejecutar las operaciones ariméticas y lógicas entre valores. 
- **PC:** Program Counter. Registro del CPU que apunta a la siguiente instrucción a ejecutar. 
- **Control Unit:** Uno de los 3 bloques principales en los que se divide el CPU.
- **Diagrama:**

![Diagrama de funcionamiento](https://www.dropbox.com/s/jmai9f1o5mktwll/funcionamiento.png?dl=1)

>**Nota:** Las instrucciones se guardan en una **"palabra"**. Una **palabra** es una cadena de bits.

<div style="page-break-after: always;"></div>

## Direct Memory Access

Circuito especializado que permite a ciertos componentes de la computadora acceder a la memoria del sistema independientemente del CPU. 

![Diagrama de DMA](https://www.dropbox.com/s/k5oict550vdmna4/DMA.png?dl=1)

## Acceso secuencial

Supongamos que dos usuarios realizan una solicitud hacia el CPU. Uno busca leer y el otro escribir. 

![Diagrama de acceso secuencial](https://www.dropbox.com/s/1vxupgrjs5g06ou/acceso.png?dl=1)

<div style="page-break-after: always;"></div>

El acceso secuencial se muestra en el siguiente diagrama: 

![Diagrama de acceso secuencial 2](https://www.dropbox.com/s/js51w6of6k3dua8/acceso2.png?dl=1)

>**Nota:** El tener varias computadoras no nos permite asegurar que cada tick se dé al mismo tiempo. 

Con el fin de solventar esta situación, se crea un Controller. El **Controller** se encarga se recibir las solicitudes de los **Worker nodes** utilizando el método FIFO  y los serializa.

Ejemplo:

![Diagrama de controller](https://www.dropbox.com/s/o6fvl0a2w1zelxf/controller.png?dl=1)

>**Nota:** Si el controller falla, todo el sistema falla. Para solucionar esto se agregan más controllers.

![Diagrama de controller 2](https://www.dropbox.com/s/s7fey0mrgcfl98h/controller2.png?dl=1)

- Cada controller conoce las direcciones de los otros controllers.
- El worker pregunta a un controller cuál es el máster. 
- Si el máster se cae, el worker vuelve a preguntar.


## Conceptos sobre bases de datos

- **Instrucción atómica:** Es indivisible  y se ejecuta en un solo ciclo del reloj. 
- **Transacciones:** Instrucciones que se ejecutan como una sola. Permite bloquear la BD para brindar consistencia a los datos.
- **Índices:** Son un identificador único que para cada dato que mejora la velocidad de las operaciones.
- **Abstracción:** Agarrar un problema de la vida real y obtener las características más importantes para resolver el problema.
- **SQL:** Lenguaje de definición y manipulación de datos.
- **Autenticación:** Validación en el sistema.
- **Autorización:** Permisos que se tienen en el sistema.
- **Diccionario de datos:** Los metadatos de los datos que se guardan en la BD.

<div style="page-break-after: always;"></div>

### Representación de una tabla:
![Diagrama de tabla](https://www.dropbox.com/s/4vf5uqfftemb1im/tabla1.png?dl=1)

Podríamos definir una **base de datos** como una colección de tablas.

### Integridad referencial

![Diagrama de tabla 2](https://www.dropbox.com/s/98ky4e8bdc1jam5/tabla2.png?dl=1)

No se puede hacer un delete de la tabla de la derecha sin haber borrado los registros de la izquierda.

## Conceptos de la lectura

- **Data point:** Información que generan los sistemas de información.
- **Data access:** Verifica la autenticación y la autorización.
- **Document level security:** Permisos a un usuario para que pueda leer y/o escribir en ciertas rows y columns.
- **Insights:** Conclusiones a partir de datos.
- **Reporting/analytics:** Los Analytics son el análisis para llegar a los insights y el reporting es la presentación de los insights.
- **Info batch:** Información que se encuentra alojada en disco.
- **Info streaming:** Tener un flujo constante de datos.
- **Dump:** Escribir información en crudo al disco.

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

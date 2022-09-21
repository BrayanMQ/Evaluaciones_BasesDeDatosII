# Prueba Corta # 2

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**

1. Explique el concepto de:
    -  **Shard:** Es la unidad en la que ElasticSearch distribuye los datos en el cluster.
    -  **Replica:** Las replicas son básicamente sistemas de seguridad, donde alojan copias de la información. Estos permiten acceso a la información en caso de que un nodo se caiga. Las replicas también definen cuántos pods corren al mismo tiempo.
    -  **Partition:** Consiste en la división de grandes datos de una BD en slices mucho más pequeños y manejables. 

2. Explique la diferencia entre Strong Consistency Eventual Consistency

    El concepto de **Strong consistency** se caracteriza por obligar a que el usuario únicamente pueda leer datos cuando este tiene la última versión. Por otro lado, el **eventual consistency** permite que el usuario tenga acceso a la información con alta disponibilidad a cambio de que este pueda no ver la última versión debido al retraso existente que puede existir.  
3. ¿En qué consiste warm replicas y hot replicas?
    Los warm replicas trabajan con strong consistency, mientras que las hoy replicas trabajan con eventual consistency. 

4. ¿En qué consiste consiste switch over y fail over?
El **switch over** consiste en la intervención manual en caso de la caida de un server. El **fail over** consiste en un respaldo, donde apenas un error es detectado en un componente, el tránsito de datos se limita y de inmediado otro sistema de respaldo asume la responsabilidad. 

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

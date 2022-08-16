# Apuntes - Semana 2 - Viernes 5 de agosto

>**Brayan Marín Quirós - 2019180348**

>**Bases de datos II**


## Componentes de una computadora

- **Disco:** Elemento de almacenamiento de datos. Es la parte más lenta de la computadora comparada con la memoria principal, la caché o el CPU. 
- **Memoria principal:** Elemento de la computadora que almacena datos de forma temporal, ya sea porque se están ejecutando o porque se ejecutarán después. Se comunica con el disco a través de buses. Es volátil.
- **Bus:** Se encarga de transferir datos entre elementos de la computadora. Por ejemplo, entre la memoria principal y el disco. Son de 32 o 64 bits.
- **CPU:** Componente más rápido de la computadora. Se encarga de ejecutar las apps. 
- **Program counter:** Registro de un CPU que contiene la dirección (ubicación) de la instrucción que se está ejecutando en ese momento. 
- **Caché:** Memoria que comunica la memoria principal y el CPU. Se encarga de almacenar datos de alta velocidad, normalmente de manera transitoria. 

## Diagrama de funcionamiento

![Diagrama de funcionamiento](https://lh3.googleusercontent.com/pw/AL9nZEW_mgnO5ffpvWJE1sgO1kk_Jf1vrXxwIkJb0AW1tQkSBhy2CjD1LTBq2MRgPxdQbeh62wK1oBTiHWCUB8k-zif3DWorJ2L6zs78nGZx-dxscIXh4YzdpG6v0z9I61r2uc44gGzfAlHkNE7MyWuUAUik=w1218-h727-no?authuser=1)

## Conceptos importantes 

- **Round-Robin:** Dar un recurso en un momento determinado de manera equitativa y ordenada. 
- **Context switch:** Obtiene toida la info del CPu y la devuelve a memoria. Es una opción muy cara.
- **Scale up:** Mejorar o agradar la computadora.
- **Scale out:** Agregar más computadoras. 
- **File system:** Elemento que controla cómo se almacenan y se recuperan los datos.

    ![Descripción de la imagen](https://lh3.googleusercontent.com/pw/AL9nZEVsaUEBESevcF9IWLlR9NjilQbzIzr5_sqjSmqpDH3VQIFMItzgNLrwvk02MBZByT8ULSoGWIWVbWXYjTBm4jUg5dnqIgLS0wVqY_UR98POAznh5Yq245zWZN1jBLg5C54ls_lqe0kiWp-xrGaHlTU8=w674-h265-no?authuser=1)

    | Cantidad de memoria principal | Cantidad de memoria en disco | Storage |
    | ------ | ------ | ------ |
    | 1Gb | 30Gb| Hot | 
    | 1Gb | 60Gb| Warm | 
    | 1Gb | 120Gb| Cold | 
    
- **File descriptors:** Cantidad de archivos que puedo tener abiertos al mismo tiempo.
- **Calidad de servicio:** Cantidad de usaurios a los que se les puede servir al mismo tiempo. 

- **RAID:** Redundant Array of independent Disks. Su propósito es proteger datos en caso de que un disco falle. 
    - **Replicación en RAID:**
![Replicacion](https://lh3.googleusercontent.com/pw/AL9nZEUSzM7aOIZXflMBGvE4X0ekbz5S-ScdtIdjk8Dh4wskUzxa2ZVC5-aBu2gl6kFBZjfpMFkoIIC_8zJvakH4cSZanD_qERWl_dDtVn-10d5mcTshTCfQN-eYlmyRqPG6PbDGj6i28Qgv9b_ghZ4owyLt=w486-h313-no?authuser=1)

    - **Paridad en RAID:** 
    
        | Disco 1 | Disco 2 | Disco 3 | Cantidad de unos | Paridad | 
        | ------ | ------ | ------ | ------ | ------ |
        | 0 | 1 | 1 | 2 | 0 | 
        | 1 | 0 | 0 | 1 | 1 | 
        | 1 | 1 | 0 | 2 | 0 | 

        Si se elimina un disco duro, se puede volver a reproducir la información a partir del conocimiento de la paridad.

- **Memory management:** Se refiere a la gestión de memoria de las apps. El SO no permite que se manipule la información de otras apps a partir de una app. 

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

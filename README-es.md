# elasticsearch-machine-learning-twitter

[English](./README.md) | Español

## Introducción

El proyecto sirve para ver un ejemplo de cómo utilizar el módulo de machine learning de elasticsearch. El entorno se despliega de forma muy sencilla mediante docker-compose y tanto la ingesta de datos (filebeat) como la generación de índices de elastic y configuraciones se lleva a cabo automáticamente en el despliegue, de forma que el entorno está **totalmente preparado** para la prueba una vez deplegado. El dataset es un conjunto de tweets (en formato *csv*) recopilados en 2017 haciendo un seguimiento de ciertos hashtags asociados con elasticsearch (podría cambiarse por cualquier otro siempre y cuando tenga un formato similar).

Nota: El uso del módulo de machine learning requiere licencia *platinum*, pero para nuestro ejemplo no será necesaria la suscripción ya que Elastic Search permite una prueba gratis de 30 días que se activa inmediatamente desde Kibana, que además puede ser extendida.

## Requerimientos

Docker - Se usa docker-compose para desplegar el entorno de trabajo.

## Despliegue

Windows: 

Arrancar: ```bin/start-up.bat```

Parar: ```bin/stop-remove.bat```

Linux: 

Arrancar: ```bin/start-up.sh```

Parar: ```bin/stop-remove.sh```

No es necesario utilizar los scripts para arrancar y parar. Para desplegar el entorno basta con hacer ```docker-compose up``` y para parar y eliminar todos los contenedores y la red asociada ```docker-compose down```.

**Nota**: Si se quiere guardar toda la información generada en elastic y kibana (jobs de machine learning, etc.) para acceder en el futuro (es decir, después de eliminar los contenedores), basta con descomentar la línea de 'volumes' en el fichero docker-compose.yml. En ese caso, no haría falta arrancar todos los servicios de nuevo, bastaría con arrancar elastic y kibana, ya que filebeat se encarga únicamente de ingestar los datos del dataset (```docker-compose up elasticsearch kibana```).

## Software del entorno

Elasticsearch: 6.7.0

Kibana: 6.7.0

Filebeat: 6.7.0

## Tutorial

Una vez desplegado el entorno, filebeat se encarga de forma automática de crear el índice con los datos almacenados en */volumes/filebeat/config/dataset/tweets.csv*. Para ello, hace uso del fichero de configuración propio de filebeat */volumes/filebeat/config/filebeat.yml*, donde se define el path del dataset, el template que hay que usar a la hora de crear el índice, etc.

Podemos comprobar desde kibana (http://localhost:5600) que se ha generado todo correctamente, por ejemplo, en *Management --> Index Management* vemos que se ha creado el índice y podemos ver el número de documentos, tamaño, mapping...

![Index Management](/assets/indexManagement.png)

Para ver los datos indexados y almacenados en kibana, podemos hacer uso de la pestaña *Dev Tools*, que nos presenta una interfaz desde la cual podemos hacer queries al índice creado.

![Consulta al índice twitter-ml](/assets/devTools.gif)


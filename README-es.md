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

Antes de empezar a crear jobs de Machine Learning, será necesario crear un index-pattern. Un index-pattern es un patrón de índices, que permite, tanto para visualizaciones y dashboards como para Machine Learning, visualizar/analizar documentos de los índices que cumplan dicho patrón. Por ejemplo, es importante a la hora de crear trabajos de roll-over, en los que los shards de nuestros índices necesiten rotar con una política específica (horario, diario, por tamaño...). Desde la pestaña de *Management*, vamos a Kibana --> Index Patterns

![Crear index-pattern](/assets/indexPattern.gif)

Vamos a crear ahora los jobs de Machine Learning, que es el objetivo fundamental del tutorial.

En la pestaña *Machine Learning* necesitamos activar la licencia. Es tan simple, que solo hace falta hacer clic en el botón *Start trial*, lo que llevará automáticamente a la interfaz de management y se podrá activar sin necesidad de introducir ninguna información. La licencia nos dará acceso durante 30 días al módulo de Machine Learning y Watchers entre otros...

Ya podemos navegar de nuevo a la pestaña ***Machine Learning***. Ahora tenemos desbloqueado el uso de todas las opciones.

Dentro de dicha interfaz de kibana, vemos que a su vez varias pestañas:

- Job Management: Interfaz para ver los jobs creados, detalles de los mismos, memory status, job state, etc.
- Anomaly Explorer: Heapmap que permite analizar las anomalías por el valor de un campo específico frente al día del mes
- Single Metric Viewer: Histograma con tabla de información de las anomalías.
- Data Visualizer: Para explorar los datos en caso de ser necesario. Permite ver las distribuciones y los "top values" para cada dato del índice, con un número de documentos, por supuesto, limitado.
- Settings: permite generar litas de filtros y calendario de acciones.

A grosso modo, hay dos tipos de jobs de Machine Learning que se pueden generar (todos los que veremos son para detección de anomalías en series temporales):

1. Single metric: Genera un único modelo y solo para una única métrica
2. Multi metric: Permite generar varios modelos asociados a los posibles valores de un campo particular, y permite monitorizar varias métricas

En este ejemplo, vamos a crear un job para analizar el número de tweets por topic (aunque solo tenemos un topic general que es *apm*). El job nos va a permitir investigar acerca de las anomalías que se han producido en la serie temporal, tomando como métrica el número de documentos (tweets) para un periodo de tiempo específico.

Como idea, lo ideal sería tener una monitorización continua de twitter para dichos hashtags y así poder monitorizar mediante machine learning el comportamiento del número de tweets que se publican para un tema específico, pudiendo además hacer uso de los Watchers de elastic para lanzar webhooks a una aplicación específica de gestión de alarmas por ejemplo cuando detectasemos ciertas anomalías.

El job que vamos a configurar es de tipo *Single metric* (aunque lo haremos desde la pestaña de **Advanced**), y tiene la siguiente configuración:

- Name: twitter_trends
- Detectors: high_count (twitter_trends)
- Influencers: "fields.topic", "entities.hashtags.text", "entities.user_mentions.name", "retweeted_status.entities.user_mentions.name", "user.name", "user.location", "entities.urls.display_url", "retweeted_status.entities.hashtags.text", "retweeted_status.entities.urls.display_url"

![Crear job](/assets/createJob.gif)

A continuación, podemos validar el job para pasar un chequeo antes de crear el job. En nuestro caso (sacado de un ejemplo propio de elastic) nos dice que tenemos demasiados influencers y que consideremos crear varios jobs, no hará falta para este ejemplo de prueba, pero en la documentación de elasticsearch dicen que lo apropiado es tener alrededor de 3 (https://www.elastic.co/guide/en/kibana/6.7/job-tips.html#influencers).

Guardado el job, lo último es ejecutarlo. Cuando guardas dicho job, sale la opción de iniciar el datafeed. Personalmente, recomiendo guardar primero el job sin lanzarlo y desde la interfaz de *Management*, lanzarlo (para el periodo temporal que uno quiera, existiendo, por supuesto, la opción de continuar en *Real-Time*).

El siguiente gif muestra el resultado final una vez iniciado el job.

![Start job](/assets/startJob.gif)

Hasta aquí un ejemplo sencillo de cómo utilizar el módulo de Machine Learning del stack de elastic.

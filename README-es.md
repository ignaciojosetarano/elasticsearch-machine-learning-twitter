# elasticsearch-machine-learning-twitter

[English](./README.md) | Español

## Requerimientos

Se usa docker-compose para desplegar el entorno de trabajo.

## Despliegue

Windows: 

Arrancar: ```bin/start-up.bat```

Parar: ```bin/stop-remove.bat```

Linux: 

Arrancar: ```bin/start-up.sh```

Parar: ```bin/stop-remove.sh```

No es necesario utilizar los scripts para arrancar y parar. Para desplegar el entorno basta con hacer ```docker-compose up``` y ```docker-compose down``` para parar y eliminar todos los contenedores y la red.

Si se quiere guardar toda la información generada en elastic y kibana (Jobs de machine learning, dashboards...) para acceder en el futuro, basta con desconectar la línea de 'volumes' en el fichero docker-compose.yml. En este caso, no haría falta arrancar todos los servicios, bastaría con arrancar elastic y kibana, ya que filebeat se encarga únicamente de ingestar los datos del dataset (```docker-compose up elasticsearch kibana```).

## Software del entorno

Elasticsearch: 6.7.0

Kibana: 6.7.0

Filebeat: 6.7.0

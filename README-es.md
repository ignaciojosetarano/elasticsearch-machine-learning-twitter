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

No es necesario utilizar los scripts para arrancar y parar. Para desplegar el entorno basta con hacer ```docker-compose up``` y para parar y eliminar todos los contenedores y la red  ```docker-compose down```.

Si se quiere guardar toda la información generada en elastic y kibana (jobs de machine learning, etc.) para acceder en el futuro, basta con descomentar la línea de 'volumes' en el fichero docker-compose.yml. En ese caso, no haría falta arrancar todos los servicios de nuevo, bastaría con arrancar elastic y kibana, ya que filebeat se encarga únicamente de ingestar los datos del dataset (```docker-compose up elasticsearch kibana```).

## Software del entorno

Elasticsearch: 6.7.0

Kibana: 6.7.0

Filebeat: 6.7.0

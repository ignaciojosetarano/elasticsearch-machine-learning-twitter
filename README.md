# elasticsearch-machine-learning-twitter

English | [Español](./README.md)

  Pendiente: Índices de elastic y jobs de machine learning. Traducir a español.

## Requeriments

Docker or Docker for Windows. It´s use docker-compose to environment deploy.

## Deploy

For windows: bin/start-up.bat
For linux: bin/start-up.sh

You can easily deploy without using bin scripts doing ```docker-compose up```, 'up' command create the containers, the network and start all the services. To stop and remove all the containers and network, you can do ```docker-compose down```. If you only want to stop the containers and use them in the future without loosing information, you can stop services doing 'docker-compose stop SERVICE1 SERVICE2' (to start them 'docker-compose start SERVICE1). Also you can uncomment the docker-compose volumes for elasticsearch and persist the information you generate in kibana (for example, ml jobs).

## Software

Elasticsearch: 6.7.0
Kibana: 6.7.0
Filebeat: 6.7.0

# elasticsearch-machine-learning-twitter

English | [Español](./README-es.md)

## Requeriments

Docker or Docker for Windows, docker-compose is used to environment deploy.

## Deploy

Windows: 

Start: ```bin/start-up.bat```

Stop: ```bin/stop-remove.bat```

Linux: 

Start: ```bin/start-up.sh```

Stop: ```bin/stop-remove.sh```

You can easily deploy without using bin scripts doing ```docker-compose up```, 'up' command create containers, network and start all the services. To stop and remove all containers and network, you can do ```docker-compose down```. 

Also you can uncomment the docker-compose lines 'volumes' for elasticsearch and persist the information you generate in kibana (for example, Twitter data and machine learnin jobs).

## Software environment

Elasticsearch: 6.7.0

Kibana: 6.7.0

Filebeat: 6.7.0

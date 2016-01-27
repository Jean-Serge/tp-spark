# SID - Utilisation de SPARK 

## Prérequis

Il faut tout d'abord avoir installé [*docker*](https://docs.docker.com/engine/installation/).

Il est également préférable de savoir programmer en *python* ou en *scala* pour la programmation distribuée avec Spark.

## Installation

### Lancement du *master* 

	docker run -d -p 7077:7077 -p 8080:8080 --name master sequenceiq/spark:1.6.0 '/usr/local/spark/sbin/start-master.sh ; tail -f /usr/local/spark/logs/*'
	
Le port 8080 donne accès à l'interface web de **SPARK**. Celle-ci permet de consulter l'état du *master* (nombre de *slaves*, leur état, ...).

Le port 7077 permet aux *slaves* de communiquer avec le *master*.

Il est possible de rajouter certaines options au lancement, par exemple pour créer un volume partagé entre l'hôte et le conteneur : 

	-v [path_on_host]:[path_on_container]

Ou configurer automatiquement le proxy dans le conteneur :

	-e http_proxy=proxy.univ-lille1.fr:3128 -e https_proxy=proxy.univ-lille1.fr:3128

### Lancement des *slaves*

	docker run -d -e SPARK_MASTER_IP=192.168.12.111 -e SPARK_WORKER_CORES=1 -e SPARK_WORKER_MEMORY=1g -e SPARK_WORKER_INSTANCES=3 sequenceiq/spark:1.6.0 '/usr/local/spark/sbin/start-slave.sh spark://${SPARK_MASTER_IP}:7077 ; tail -f /usr/local/spark/logs/*' 

Il est possible de modifier les différentes variables d'environnement :
+ L'IP du *master* *(préférer une adresse IP directement plutôt qu'un localhost)*
+ Le nombre de *slave* 
+ La RAM et le nombre de coeur alloués aux *slaves* 

*Remarque : Il faut faire attention aux ressources attribuées aux slaves ainsi que le nombre de slave démarrés. Le but étant d'éviter de saturer la machine hôte et de garder la main dessus.*

## Utilisation 

### Fonctionnement du conteneur

Pour prendre la main sur le *master* 

	docker exec -ti master bash

L'image utilisée est basée sur CentOS, il faut donc utiliser *yum* comme gestionnaire de paquets.

Dans le conteneur *master* vous pouvez accéder à un interpréteur Python (**pyspark**) ou Scala (**spark-shell**)

### Lancer un programme distribué

pour lancer un programme distribué sur le master du cluster, il faut précéder la commande par la définition d'une variable d'environnement spécifiant le *master* du cluster. Par exemple :

	MASTER=spark://192.168.12.111:7077 pyspark


## Liens externes

### Documentation Spark

+ [Site officiel de spark](http://spark.apache.org/)
+ [Tutoriel d'utilisation de Spark en cluster indépendant](http://mbonaci.github.io/mbo-spark/)
+ [Documentation pour l'utilisation de Spark dans un cluster indépendant](http://spark.apache.org/docs/latest/spark-standalone.htm)

### Documentation Docker

+ [Site officiel de Docker](https://www.docker.com)
+ [Github de l'image spark utilisé](https://github.com/sequenceiq/docker-spark)
+ [Docker Hub de l'image spark utilisée](https://hub.docker.com/r/sequenceiq/spark/)
+ [Problème avec Docker : "fork: Resource temporarily unavailable"](http://unix.stackexchange.com/questions/253903/creating-threads-fails-with-resource-temporarily-unavailable-with-4-3-kernel)

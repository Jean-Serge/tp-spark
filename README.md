# SID - Utilisation de SPARK 

## Lancer spark dans un docker

Lancement du maître : 

	docker run -t-d -v $HOME/Documents/sid/tp-spark:/root -p 8080:8080 --name master -h master sequenceiq/spark:1.6.0 '/usr/local/spark/sbin/start-master.sh ; tail -f /usr/local/spark/logs/*'
	

Lancement des esclaves :

	docker run -d -v $HOME/Documents/sid/tp-spark:/root -e SPARK_MASTER_IP=master -e SPARK_WORKER_CORES=1 -e SPARK_WORKER_MEMORY=1g -e SPARK_WORKER_INSTANCES=4 --link master:master sequenceiq/spark:1.6.0 '/usr/local/spark/sbin/start-slave.sh spark://master:7077 ; tail -f /usr/local/spark/logs/*' 

Le port 8080 du maître donne accès à l'interface web de *SPARK* qui permet de consulter l'état du maître (nombre d'esclaves, état des esclaves, programmes en cours).

## Dans le conteneur 

Pour démarrer un cluster :

	cd /usr/local/spark
	bash ./sbin/start-master.sh

Y connecter des utilisateurs :

	cd /usr/local/spark
	./sbin/start-slave.sh spark://localhost:7077

## Sources

+ http://mbonaci.github.io/mbo-spark/
+ http://spark.apache.org/docs/latest/spark-standalone.html
+ [fork: Resource temporarily unavailable](http://unix.stackexchange.com/questions/253903/creating-threads-fails-with-resource-temporarily-unavailable-with-4-3-kernel)

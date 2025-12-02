# kafka-docker-composer
Script Python qui permet de générer un fichier docker-compose.yaml à partir d'un modèle Jinja2 et des paramètres fournis par défaut ou dans la ligne de commande

**Usage :**

```
usage: kafka_docker_composer.py [-h] [-r RELEASE] [-b BROKERS] [-z ZOOKEEPERS] [-c CONTROLLERS] [-s SCHEMA_REGISTRIES] [--control-center] [--uuid UUID] [-p] [--kafka-container KAFKA_CONTAINER] [--racks RACKS]
                                [--zookeeper-groups ZOOKEEPER_GROUPS] [--docker-compose-file DOCKER_COMPOSE_FILE] [--config CONFIG]

Kafka docker-compose Generator

options:
  -h, --help            show this help message and exit
  -r RELEASE, --release RELEASE
                        Docker images release [7.4.0]
  -b BROKERS, --brokers BROKERS
                        Nombre de Brokers [1]
  -z ZOOKEEPERS, --zookeepers ZOOKEEPERS
                        Nombre de ZooKeepers [0] - mutuellement exclusif avec l'option -c CONTROLLERS
  -c CONTROLLERS, --controllers CONTROLLERS
                        Nombre de Contrôleurs Kafka [0] - mutuellement exclusif avec l'option -z ZOOKEEPERS
  -C CONNECTS, --connect CONNECTS
                        Nombre d'instances Kafka Connect [0]
  -s SCHEMA_REGISTRIES, --schema-registries SCHEMA_REGISTRIES
                        Nombre d'instances de Schema Registry [0]
  --control-center      déploie aussi Confluent Control Center [False]
  --uuid UUID           UUID du cluster [Nk018hRAQFytWskYqtQduw]
  -p, --prometheus      déploie Prometheus [False]
  --kafka-container KAFKA_CONTAINER
                        Container utilisé pour Kafka, default [cp-server]
  --racks RACKS         Nombre de racks parmi lesquels les brokers seront répartis uniformément répartis [1]
  --zookeeper-groups ZOOKEEPER_GROUPS
                        Nombre de groupes zookeeper dans la hiérarchie [1]
  --docker-compose-file DOCKER_COMPOSE_FILE
                        Nom du fichier produit pour docker-compose, defaut [docker-compose.yaml]
  --config CONFIG       Fichier de paramètre config file, les valeurs seront remplacées par les valeurs des paramètres de la ligne de commande
```

**Installation :**
```
cd ~
rm -Rf kafka-docker-composer
git clone https://github.com/crystalloide/kafka-docker-composer
cd kafka-docker-composer
```

**A savoir :**
```
Image Docker pour Confluent Platform 8.0 :
A partir de Confluent Platform 8.0, l'image Docker à utiliser est confluentinc/cp-enterprise-control-center-next-gen,
et non plus l'ancienne image cp-enterprise-control-center qui reste limitée aux versions 7.x et antérieures.​

Versions disponibles pour Control Center "Next-Gen" :
Les versions du nouveau Control Center compatibles avec Confluent Platform 8.0 sont:​

| Version Control Center | Date de sortie | Compatibilité Confluent Platform |
| ---------------------- | -------------- | -------------------------------- |
| 2.3.0                  | 22/10/2025     | 8.1                              |
| 2.2.2                  | Récent         | 8.0.x                            |
| 2.2.1                  | 30/09/2025     | 8.0.x                            |
| 2.2.0                  | 11/06/2025     | 8.0                              |
| 2.1.0                  | 23/05/2025     | 7.5-7.9, 8.0                     |
| 2.0.0                  | 15/05/2025     | 7.5-7.9                          |

Pour utiliser le script ici, il faut donc prendre une release <8.0.0

| Confluent Platform	| Apache Kafka®	| Release Date        | Confluent Community software End of Support	| Confluent Enterprise Standard End of Support | Confluent Enterprise Platinum End of Support
| --------------------- | ------------- | ------------------- | ------------------------------------------- | ------------------------------------------ | ----------------------------------------- |
| 8.0.x			       	| 4.0.x			| June 11, 2025	  	  | June 11, 2026			                   	| June 11, 2027						      	  		| June 11, 2028                  	  | 
| 7.9.x			       	| 3.9.x			| February 19, 2025	  | February 19, 2027		                    | February 19, 2027						      		| February 19, 2028	                | 
| 7.8.x		   	    	| 3.8.x			| December 2, 2024	  | December 2, 2026	                        | December 2, 2026						        		| December 2, 2027                	| 
| 7.7.x		       		| 3.7.x			| July 26, 2024	  	  | July 26, 2026		                   	    | July 26, 2026						        			| July 26, 2027    	                | 
| 7.6.x		       		| 3.6.x			| February 9, 2024    | February 9, 2026                            | February 9, 2026				    	  	  		| February 9, 2027	                | 
| 7.5.x		       		| 3.5.x			| August 25, 2023     | August 25, 2025                             | August 25, 2025					      		    | August 25, 2026                  	| 
| 7.4.x		       		| 3.4.x			| May 3, 2023         | May 3, 2025                                 | May 3, 2025							  	         	| May 3, 2026	                      | 
| 7.3.x		        	| 3.3.x			| November 4, 2022	  | November 4, 2024                         	  | November 4, 2024					      	  		| November 4, 2025	                | 
| 7.2.x		        	| 3.2.x			| July 6, 2022	  	  | July 6, 2024			                        | July 6, 2024					      	    			| July 6, 2025                    	| 
| --------------------- | ------------- | ------------------- | ------------------------------------------- | ----------------------------------------- | ----------------------------------------- |

```

**Exemples :**

```
# Cluster  3 controleurs 3 brockers 1 schema-registry 1 control-center
docker search confluentinc
python3 kafka_docker_composer.py --controllers 3 --brokers 3 --schema-registries 1 --control-center -r 7.9.5
cat docker-compose.yml 
docker compose up -d
```

```
python3 kafka_docker_composer.py -b 4 -z 3 -r 7.3.1
docker compose up -d
```

```
python3 kafka_docker_composer.py --controllers 3 --brokers 3 --schema-registries 2 --control-center  
docker compose up -d
```

**Connecteurs :**

Quelques plugins de connecteurs préconfigurés sont disponibles dans le répertoire volumes/connector-plugin-jars et seront
automatiquement associés aux instances kafka-connect. Ajoutez ici les connecteurs nécessaires (décompressés du fichier zip)

si vous en avez besoin de davantage.

Un petit fichier postgres.yaml est également joint : il peut être déployé en complément du cluster Kafka Confluent à l'aide de l'option -f :

```shell
> docker-compose -f docker-compose.yaml -f postgres.yaml up -d
```

Vous pouvez étendre ce principe à toute autre source ou destination de données. 
Démarrés simultanément, tous les conteneurs sont démarrés sur le même réseau pour faciliter les tests.

** Reste à faire :**
* Ajouter la gestion des aspects "sécurité"
* Corriger les tableaux de bord pour Grafana


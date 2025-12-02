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

**Exemples :**

```
# Cluster  3 controleurs 3 brockers 1 schema-registry 1 control-center 
python3 kafka_docker_composer.py --controllers 3 --brokers 3 --schema-registries 1 --control-center -r 8.0.0
docker-compose up -d
```

```
python3 kafka_docker_composer.py -b 4 -z 3 -r 7.3.1
docker-compose up -d
```

```
python3 kafka_docker_composer.py --controllers 3 --brokers 3 --schema-registries 2 --control-center  
docker-compose up -d
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


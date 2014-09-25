# Historique

Au début ça s'apellait 10gen
MongoDB car huMONGOus
10gen car binaire donc 2ème génération

Le siège est à NY, Dublin en europe.

Plusieurs éditions
* Community
* Enterprise

Un autre produit MMS en cloud ou local.

## Community
University
MongoDB User Groups (dont un à Bordeaux), voir sur Meetup
MongoDB Days

# Intérêt
* Éviter d'avoir un énorme schéma de données peu fléxible, et optimiser la vitesse de lecture/écriture vs l'espace de stockage.
* Facilite l'évolution du schéma via l'ajout de colonne sur certains enregistrements uniquement et pas les autres.
* Pas de jointure ni de transactions complexes => facilite la scalabilité horizontale.

# Modèle de déploiement
* Serveur unique
* Replica set
* Cluster shardé
* Cluster shardé AVEC replica set

# Modèle de données
* Clé/valeur : Cassandra, Dynamo. Pas d'update ni consistance.
* Tabular : BigTable
* Graphe : Neo4j
* Orienté document : MongoDB. Le meilleur des 2 mondes (!)
* Relationnel : SQLS, Oracle. Richesse fonctionnelle

# Terminologie
| RDBMS | Mongo                               |
|-------|-------------------------------------|
| DB    | DB                                  |
| Table | Collection                          |
| Row   | Document                            |
| Index | Index                               |
| Join  | Document embarqué                   |
| FK    | Référence (qui n'est pas garantie)  |

# Divers
Pour palier à des problématiques de stockage, réseau, etc il a été créé la norme BSON qui permet de gérer de nouveaux types de données par rapport à JSON
```JSON
debut:isoDate("20140612")
```
Le BSON impose une limitation de 16Mb, le stockage d'objets plus lourd étant géré par GridFS qui va permettre le découpage de l'objet en documents de 16Mb.

MongoDB.com est le site commercial, avec les webcast des conférences, des architectures de référence (ecommerce, social network, …), des webinars
MongoDB.org est le site technique avec les docs techniques, les binaires, …

# Installation
```bash
$ mongod --fork --logpath /data/mxebia.log
$ mongorestore -d digg /home/vagrant/Desktop/mXebia/sampledata/dump/digg
2014-09-24T08:50:08.267+0000 /home/vagrant/Desktop/mXebia/sampledata/dump/digg/stories.bson
2014-09-24T08:50:08.268+0000 	going into namespace [digg.stories]
10000 objects found
2014-09-24T08:50:08.744+0000 	Creating index: { key: { _id: 1 }, ns: "digg.stories", name: "_id_" }
2014-09-24T08:50:08.819+0000 	Creating index: { key: { diggs: 1 }, ns: "digg.stories", name: "diggs_1" }

$ mongorestore -d training -c scores /home/vagrant/Desktop/mXebia/sampledata/dump/training/scores.bson
2014-09-24T08:50:58.895+0000 /home/vagrant/Desktop/mXebia/sampledata/dump/training/scores.bson
2014-09-24T08:50:58.895+0000 	going into namespace [training.scores]
3000 objects found
2014-09-24T08:50:58.936+0000 	Creating index: { key: { _id: 1 }, ns: "training.scores", name: "_id_" }

$ mongoimport -d twitter -c tweets /home/vagrant/Desktop/mXebia/sampledata/twitter.json
2014-09-24T08:51:33.011+0000 		Progress: 39035916/92307954	42%
2014-09-24T08:51:33.012+0000 			21700	7233/second
2014-09-24T08:51:36.019+0000 		Progress: 88864108/92307954	96%
2014-09-24T08:51:36.020+0000 			49500	8250/second
2014-09-24T08:51:36.221+0000 check 9 51428
2014-09-24T08:51:36.244+0000 imported 51428 objects
```

# Stockage

Il est possible de paralléliser les stockages en affectant les bases à différents répertoires à monter sur des diques différents.
Il est possible de stocker les journaux, logs et data à des endroits différents également.

À la création d'une base on crée le fichier base.0 qui alloue l'espace maximal (64MB) puis un base.1 juste avant le remplissage avec 128MB d'allocation et ainsi de suite jusqu'à ce que chaque nouveau fichier alloue 2GB. (Voir extent allocation)

----

Note : Sur un système avec beaucoup de delete il faut lancer une commande de compactage sur les collections, mais attention c'est bloquant.

----

## Memory map

* Pas de code complexe dans MongoDB car géré par l'OS (et ses bugs)
* La RAM est affecté par la fragmentation disque et lecture en avance

La doc officielle propose des best practice d'installation

L'ensemble des données des fichiers data remonte ses infos, soient la data mais aussi les index qui peuvent donc ne pas ête en mémoire sans pour autant forcer un comportement différent.

## Cout du journaling

* Aucun cout sur un système orienté lecture
* Performance en écriture réduite de 5 à 30%
* 3% sur un disque différent

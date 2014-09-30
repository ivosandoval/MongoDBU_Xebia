* Il est nécessaire de définir une clé de sharding afin de partitionner les données.
* La taille limite d'un chunk est de 64MB.
* Lors du split puis de la migration des chunks un seul d'entre eux peut être en train de migrer entre 2 mêmes serveurs en même temps tout en restant disponible sur le serveur source tant que l'opération est en cours.
* Il est possible de sharder les chunks avec des tags (6 serveurs US, 2 serveurs FR et les clients taggués suivant la localité)

Les shards sont gérés par des `mongos`.  
Lors d'une requête utilisant la clé de sharding les demandes sont envoyés uniquement au bon shard.  
Lors d'une requête n'utilisant pas la clé de sharding les demandes sont envoyés à tous les serveurs qui remontent les résultat au shard par défaut de la base utilisée.

Il faut exactement 3 serveurs de config dans un shard à cause d'un algo "2 place commit" à fournir à l'identique à chaque mongos.  
Lors d'uin crash du serveur de config il est nécessaire de reconfigurer à la main car rien n'est automatisé.  

Dans certains cas il faut stopper le balancer dans la nuit afin que les migrations ne rentrent pas en compétition avec les insertions dans le cas de gros travail d'IO à ce moment là.

Une clé de sharding ne peut être modifié à moins de migrer la collection, de même pour ses valeurs.

## Mise en route
1. Démarrer les réplica (si il y en a)
2. Démarrer les serveurs de config
3. Démarrer `mongos`

## Clé de sharding (email)
* Sur `_id` = excellent cardinalité mais croissant donc forte chance de rester sur le même shard
* Sur `hash(_id)` = moins bonne cardinalité mais très couvrant donc répartition excellent : super pour maximiser l'écriture
* Sur `user` = mauvaise cardinalité mais on écrit sur tous les shard, on isole correctement les requetes
* Sur `user, time` = comme précédente mais avec des chunks différents pour un même utilisateur dans le temps d'où meilleure cardinalité


|             | cardinalité | write scaling | query isolation | reliability | index locality |
|-|-|-|-|-|-|
| id          | - | - |- |- |- |
| hash(id)    | - | - |- |- |- |
| user        | - | - |- |- |- |
| user, time  | - | - |- |- |- |

## Use cases

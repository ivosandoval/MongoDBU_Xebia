* Les requêtes sur les ranges risquent de se diviser sur plusieurs branches du B-Tree.
* Utiliser des disques séparés pour les journaux => complexifie les backups qui * doivent être fais au même moment
* Utiliser des disques séparés pour les bases
* Interdire les requetes sans index via `--notablescan`
* Recompacter les index => opération bloquante à faire sur chaque secondaire dans un premier temps avant de faire sur le primaire qu'il faut basculer. Visible en comparant `storageSize` et `dataSize` dans `db.stats()` OU `size` et `storageSize` dans `db.collection.stats()`
* Préférer ext4 et xfs sur Linux et desactiver NUMA
* Préférer la RAM puis les disques et enfin les CPU en 64bits
* Scaler les reads avec la réplication en perdant la consistence forte
* Configurer différement les machines du replica suivant les configs et les use cases
* Sharder pour dispatcher les write ou si trop de data pour monter en mémoire sur un nœud (limites physiques du système)

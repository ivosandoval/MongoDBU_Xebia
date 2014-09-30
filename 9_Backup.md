Recovery Point Objective : Combien on peut perdre  
Recovery Time Objective : Combien de temps offline  
Disaster Recovery  
High Availability

Tester régulièrement les backups et leur restauration

## Techniques
### Niveau document
* Logique (import/export) : lent, garde pas les index, …
* `mongodump` : bson, rebuild les index, local/remote, `--oplog` nécessaire si base online + `--oplogReplay` pdt le restore, pas incrémental

### Niveau système de fichier
* Snapshot volume (si même volume pour data et journal) Copie db+journal en même temps  
  `db.fsyncLock` pour flush la RAM > Copy snapshot > `db.fsyncUnlock`
* File System Backup : le plus rapide à backup/restore mais très gros

`mongorestore`

# Shard
## Backup
1. Stoper le balancer (et attendre) ou periode sans balancing
2. Stopper un serveur de config pour empecher les splits
3. Backup data (shard + config)
4. Redemarrer le serveur de config
5. Relancer le balancer

## Restore
* Pas possible en ayant changé la shard key
* Difficile avec un nombre de shards ayant évolué
* Restores selectifs
* …

# MMS
1. Stop le balancer toutes les 6h
2. Injection d'un token dans chaque shard/mongos/config
3. …

Restore n'importe quand dans les dernières 48h puis dépend de la config de rétention

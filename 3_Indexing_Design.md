# Binaires
`mongorestore` reprend la base telle quelle.  
`mongoimport` n'importe que les data sans les index.

# Structure
Les index sont des B-Trees  
Il est possible d'optimiser l'insertion en gérant la modélisation de l'index (plusieurs milliers d'insertions à la seconde)

## Exemples

Index simple (ASC), l'ordre n'étant pas vraiment important
```
db.recipes.ensureIndex({main_ingredient: 1})
```

Index composé (ASC, DESC)
```
db.recipes.ensureIndex({main_ingredient: 1, calories: -1})
```

Index multi clés, sur les tableaux
```
db.recipes.ensureIndex({ingredient: 1})
```

Index sur sous élément
```
db.recipes.ensureIndex({"contributor.id": 1})
```

Gérer les indexs ou leur clés
```
db.recipes.getIndexes()
db.recipes.getIndexKeys()
db.recipes.dropIndex({ingredient: 1})
```

La création d'un index est une opération bloquante par défaut sauf si elle est déclenchée en tache de fond ou sur un replica (appliquer l'index sur le secondaire puis le basculer en primaire).
```
db.recipes.ensureIndex({ingredient: 1},{background: true})
```

Création d'index unique (dropDups dégage les entrées ultérieures sans générer d'erreur)
```
db.recipes.ensureIndex({name: 1},{unique: true, dropDups: true})
```

Les sparses index permettent de n'indexer QUE les documents contenant les champs indiqués au contraire d'un index classique qui les indexera également avec la valeur NULL
```
db.recipes.ensureIndex({calories: 1},{sparse: true})
```

Il est possible de forcer l'utilisation d'un index en précisant un `.hint()`


D'autres types d'index sont disponibles :
* Géospatiaux
```
db.recipes.ensureIndex({loc: "2dsphere"})
db.recipes.ensureIndex({fig: "2d"})
```
* Texte (type full text)
```
db.recipes.ensureIndex({y: "text"})
```
* TTL (le document se supprimant automatiquement après un certain temps ou à une date fixe)
```
db.recipes.ensureIndex({ttl: 1},{ expireAfterSeconds: 3600 })
db.recipes.ensureIndex({date_purge: 1},{ expireAfterSeconds: 0 })
```
* Hashes

# Limitations
* 64 index max par collection
* Clé de 1024 bytes
* Nom de 125 char
* 1 seul index utilisable, voir 2 si une intersection à moins qu'on précise des `$or`
* 31 champs max dans un index composé

# Explain
```
db.scores.find({students:0}).explain()
```
* `cursor` : type d'opération
* `yield` : nombre de relachements de la requete avant d'aboutir
* `indexOnly` : utilisation uniquement des champs de l'index

# Attention
* Si deux index sont créés sur des champs différents un seul des deux sera utilisé pour remonter les résultats au lieux de faire une intersection.
* Peu d'interet sur un champ peu dense (genre booléen)
* Possibilité d'utiliser des regexp (seul le premier exemple utiliserait l'index)
```
db.users.find({username : /^joe smith/})
db.users.find({username : /smith/})
db.users.find({username : /joe smith/i})
```
* Non utilisé sur les négations

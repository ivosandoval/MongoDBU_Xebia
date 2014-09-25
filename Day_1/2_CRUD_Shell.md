# Commandes de base

```
> use twitter
> db.scores.find()
```

Scores de l'étudiant 99
```
> db.scores.find({"student" : 99})
```

Scores supérieurs à 60 de type exam
```
> db.scores.find({"kind" : 'exam', score : {$gt : 60}}).count()
```
Scores supérieurs à 60 de type exam ou quizz

```
> db.scores.find({"kind" : {$in : ['exam', 'quizz']}, score : {$gt : 60}}).count()
> db.scores.find({$or : [{kind : 'exam'},{kind : 'quizz'}], score : {$gt : 60}}).count()
```

```
> db.scores.insert({"name" : "machin", "age" : 27})
> db.scores.remove({"age" : 27})
```
L'update se fait avec un set afin de ne pas perdre les données déjà existantes

```
> db.scores.update({"age" : 38}, {$set : {"city" : "Paris" }})
```

L'update s'arrete au premier résultat qui marche à moins de préciser l'opotion multi
```
> db.scores.update({"age" : 38}, {$set : {"city" : "Paris" }}, {multi : true})
```

Pour gérer en upsert il faut ajouter l'option
```
> db.scores.update({"age" : 38}, {$set : {"city" : "Paris" }}, {multi : true, upsert : true})
```

Pour créer des champs uniquement à l'insert il faut ajouter l'option $setOnInsert
```
> db.scores.update({"age" : 38}, {$set : {"city" : "Paris" }}, $setOnInsert { "new" : true}, {multi : true, upsert : true})
```

Pour récupérer uniquement le champ name
```
db.scores.find({}, {name : 1})
db.scores.find({}, {name : 1, _id : 0})
```

Pour trier un champ en DESC
```
db.scores.find({}, {name : 1, _id : 0}).sort({age : -1})
```

On peut limiter le set en paginant
```
db.scores.find({}, {name : 1, _id : 0}).limit(5).skip(20)
```

Les résultats peuvent être prettifiés
```
db.scores.find({}, {name : 1, _id : 0}).limit(5).skip(20).pretty()
```

Des éléments peuvent être ajoutés ou enlevés à un tableau
```
db.scores.update({"name" : "bob"}, {$push : {"notes" : 10}})
db.scores.update({"name" : "bob"}, {$addToSet : {"notes" : 10}})
db.scores.update({"name" : "bob"}, {$pull : {"notes" : 10}})
```

# Les locks

Les locks sont posés au niveau d'une db (process en 2.2), et passera au niveau d'un document en 2.8.
Une écriture doit attendre que les locks en lecture soient tombés.
Un lock en écriture est suspendu par moment afin de laisser passer d'autres lectures ou écritures intermédiaires -> On peut lire des données pas totalement mises à jour (un update sur 500 documents qui n'est passé que sur les 300 premiers).

# Agrégations
## MapReduce
* Versatile et puissant
* Lent car single-thread

## Aggregation Framework
* Déclaré en JSON
* Execution en C++
* Flexible et simple
* Non compatible avec le sharding

To return all states with a population greater than 10 million, use the following aggregation operation :
```
> db.zipcodes.aggregate(
  { $group :
    { _id : "$state", totalPop : { $sum : "$pop" } }
  },
  { $match : {totalPop : { $gte : 10*1000*1000 } } }
)
```

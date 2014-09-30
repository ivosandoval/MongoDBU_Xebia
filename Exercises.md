# Create (inserts) (p7)
```
> db.messcores.insert({_id : 10, "name" : "Ludivine"})
WriteResult({
	"nInserted" : 0,
	"writeError" : {
		"code" : 11000,
		"errmsg" : "insertDocument :: caused by :: 11000 E11000 duplicate key error index: training.messcores.$_id_  dup key: { : 10.0 }"
	}
})
```

```
> db.messcores.find()
{ "_id" : ObjectId("5422bd7192265512de198a63"), "name" : "lenaic", "note" : 20 }
> db.messcores.insert({_id : new ObjectId(), "name" : "Ludivine"})
{ "_id" : ObjectId("5422bd7192265512de198a63"), "name" : "lenaic", "note" : 20 }
{ "_id" : ObjectId("5422be29769b438e72bf18b4"), "name" : "Ludivine" }
> db.messcores.insert({_id : new ObjectId().valueOf(), "name" : "Marianne"})
{ "_id" : ObjectId("5422bd7192265512de198a63"), "name" : "lenaic", "note" : 20 }
{ "_id" : ObjectId("5422be29769b438e72bf18b4"), "name" : "Ludivine" }
{ "_id" : "5422be9b482d402acf735165", "name" : "Marianne" }

```

# Querying (p10-12)
```
> db.scores.find().count()
3000
> db.scores.find({"score" : {$lt : 65}}).count()
832
> db.scores.find({"kind" : {$in : ['exam','quiz']}}).count()
2000
```

```
> db.scores.find().sort({ score : 1 }).limit(3)
{ "_id" : ObjectId("4c90f2543d937c033f424701"), "kind" : "quiz", "score" : 50, "student" : 0 }
{ "_id" : ObjectId("4c90f2543d937c033f424711"), "kind" : "essay", "score" : 50, "student" : 5 }
{ "_id" : ObjectId("4c90f2543d937c033f424712"), "kind" : "exam", "score" : 50, "student" : 5 }
> db.scores.find().sort({ score : -1 }).limit(3)
{ "_id" : ObjectId("4c90f2543d937c033f42471c"), "kind" : "quiz", "score" : 99, "student" : 9 }
{ "_id" : ObjectId("4c90f2543d937c033f424804"), "kind" : "essay", "score" : 99, "student" : 86 }
{ "_id" : ObjectId("4c90f2543d937c033f424811"), "kind" : "exam", "score" : 99, "student" : 90 }
```

```
> db.stories.find().count()
10000
> db.stories.find({"shorturl.view_count" : { $gt : 1000}}).count()
8331  
> db.stories.find({$or : [{"topic.name" : "Television"},{"media" : "videos"}]}).limit(10).skip(5)
> db.stories.find({media : {$in : ["news","images"]}, "topic.name" : "Comedy" }).count()
308
```

# Updating (p13)
```
> db.scores.find({"score" : {$gt : 90}}).count()
562
> db.scores.update({"score" : {$gt : 90}},{$set : {"grade":"A"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.scores.update({"score" : {$gt : 90}},{$set : {"grade":"A"}},{multi:true})
WriteResult({ "nMatched" : 562, "nUpserted" : 0, "nModified" : 561 })
> db.scores.find({"score" : {$gt : 80}}).count()
1148
> db.scores.find({"score" : {$gt : 80}, "score" : {$lte : 90}}).count()
2438
> db.scores.find({$and : [{"score" : {$gt : 80}},{"score" : {$lte : 90}}]}).count()
586
> db.scores.update({$and : [{"score" : {$gt : 80}},{"score" : {$lte : 90}}]},{$set : {"grade":"B"}},{multi:true})
WriteResult({ "nMatched" : 586, "nUpserted" : 0, "nModified" : 586 })
> db.scores.find({"score" : {$lt : 60}}).count()
571
> db.scores.update({"score" : {$lt : 60}},{$inc : {"score":10}},{multi:true})
WriteResult({ "nMatched" : 571, "nUpserted" : 0, "nModified" : 571 })
> db.scores.find({"score" : {$lt : 60}}).count()
0
> db.scores.find({"score" : {$lt : 70}}).count()
1143
```

# Removing data (p15)
Removing documents keep the collection (and its files) alive, droping the collection totally remove it from disk

```
> db.scores.find({"score" : {$lt : 80}}).count()
1785
> db.scores.remove({"score": {$lt : 80}})
WriteResult({ "nRemoved" : 1785 })
```

# System commands (p16)
```
> db.runCommand({fooCommand  : 1})
{
	"ok" : 0,
	"errmsg" : "no such cmd: fooCommand",
	"code" : 59,
	"bad cmd" : {
		"fooCommand" : 1
	}
}
```

# Aggregation (p49)
Affiche les champs `_id`, `media`, `comments` et `diggs` des entrées ayant `People` en `topic name`
```
db.stories.aggregate([{"$match": {"topic.name" : "People"}}, {"$project" : {"media":1, "comments":1, "diggs": 1}}])
```
Affiche `lotz` ou `fewz` suivant si on a plus ou moins de 100 commentaires sur une story
```
db.stories.aggregate([{"$project" : { "x" : { "$cond" : [{ "$gt" : ["$comments" , 100]}, "lotz", "fewz"]}}}])
```

# Replica set (p25-30)
Minimum number of servers in replica : 3

Primary pour être consistent  
Secondary read pour du logging  
Primary read pour du temps reel

Sur 3 replica, writeConcern=2 est raisonnable

# Sharding
Configure a shard with 2 replica sets : [see script](Appendix/Shard.sh)

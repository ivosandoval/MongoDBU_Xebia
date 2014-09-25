Connection directement sur le primaire
```
$ mongo --host rs0/bdxmong003:27017,bdxmong004:27017
```

Il est possible de retarder les réplications avec `slaveDelay : 3600`

Il est possible de préciser le lieu de connexion des commandes de type find avec `.reafPref({mode: 'nearest'})`.
* Primary
* PrimaryPrefered (au cas où l'appli n'ai plus accès au primaire, déconnexion du DC)
* Secondary
* SecondaryPrefered
* Nearest (en fonction du ping)

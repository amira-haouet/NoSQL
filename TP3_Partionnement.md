
1. Qu’est-ce que le sharding dans MongoDB et pourquoi est-il utilisé ?

Le sharding est un mécanisme de partitionnement horizontal des données : une collection est découpée en fragments appelés chunks répartis sur plusieurs serveurs (shards).
Il est utilisé pour :

- gérer des volumes de données très importants

- améliorer la scalabilité horizontale

- répartir la charge en lecture/écriture.

2. Différence entre sharding et réplication

Réplication : duplication des mêmes données sur plusieurs nœuds → tolérance aux pannes et lectures distribuées.

Sharding : répartition des données différentes sur plusieurs nœuds → passage à l’échelle.

3. Composants d’une architecture shardée

- Shards : stockent les données (chacun est un replica set)

- Config Servers : stockent les métadonnées du sharding

- mongos : routeur des requêtes clientes

![image](img_tp3_tp4/sharded-cluster-mixed.bakedsvg.svg "Titre de l'image")



4. Rôle des config servers (CSRS)

Les config servers :

stockent la carte du cluster

maintiennent la correspondance chunk → shard

coordonnent le balancing.

5. Rôle du mongos router

Point d’entrée des clients

Route les requêtes vers le(s) shard(s) concerné(s)

Agrège les résultats

Est stateless (pas de données stockées)

6. Comment MongoDB choisit le shard pour un document ?

À partir de la valeur de la clé de sharding MongoDB :

détermine le chunk correspondant

identifie le shard qui possède ce chunk.

7. Qu’est-ce qu’une clé de sharding ?

C’est l’attribut (ou ensemble d’attributs) utilisé pour :

- partitionner les données

- router les requêtes

- organiser les chunks.


8. Critères d’une bonne clé de sharding

Forte cardinalité

Bonne distribution

Immuable

Fréquemment utilisée dans les requêtes

Évite les accès séquentiels

9. Qu’est-ce qu’un chunk ?

Un chunk est une plage de valeurs de la clé de sharding.
Taille par défaut : 128 Mo.

10. Comment fonctionne le splitting des chunks ?

Quand un chunk dépasse la taille limite :

MongoDB le divise en deux

met à jour les métadonnées

déclenche éventuellement une migration.

11. Rôle du balancer

Le balancer :

- surveille la distribution des chunks

- déplace les chunks pour équilibrer la charge

- fonctionne en arrière-plan.

12. Quand et comment le balancer agit-il ?

- Lorsqu’un déséquilibre est détecté

- Par migration de chunks entre shards

- De préférence en période de faible charge

13. Qu’est-ce qu’un hot shard ?

Un shard surchargé (écritures ou lectures excessives).
Cause principale : mauvais clé de sharding.

14. Problèmes d’une clé monotone

Insertion toujours sur le même shard

- Déséquilibre

- Saturation des ressources

Exemples : date croissante compteur auto-incrémenté.

 Création des répertoires de données



15. Activer le sharding (DB et collection)
```
sh.enableSharding("mabasefilms")
sh.shardCollection("mabasefilms.films", { titre: 1 })
```

16. Ajouter un nouveau shard
`sh.addShard("replicashard3/localhost:20006")`


Le balancer redistribue automatiquement les chunks.
17. Vérifier l’état du cluster

Commandes utiles :

sh.status()
db.stats()
db.films.stats()

18. Quand utiliser une clé hashée ?

Forte charge d’écriture

Accès uniformes

Pas de requêtes par intervalle

{ champ: "hashed" }

19. Quand privilégier une clé par intervalles ?

Requêtes de type range

Agrégations temporelles

Analyse par plages

20. Qu’est-ce que le zone sharding ?

Mécanisme permettant d’assigner certaines plages de données à des shards spécifiques.
Utile pour :

contraintes géographiques,

conformité réglementaire,

isolation logique.

21. Gestion des requêtes multi-shards

mongos envoie la requête à tous les shards concernés

agrégation et fusion des résultats
 Plus coûteux qu’une requête ciblée.

22. Optimiser les performances

Choisir une bonne clé

Utiliser des index compatibles

Éviter les requêtes multi-shards

Utiliser des clés hashées pour l’écriture massive

23. Indisponibilité d’un shard

Si replica set : failover automatique

Sinon : requêtes partielles ou échec

Le cluster reste actif si majorité atteinte

24. Migrer une collection existante

Activer le sharding sur la DB

Créer les index nécessaires

Appliquer sh.shardCollection

Le balancer redistribue les données

25. Outils et métriques de diagnostic

sh.status()

mongostat, mongotop

logs MongoDB

métriques : chunks, migrations, latence, hotspots
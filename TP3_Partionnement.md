
1. Qu’est-ce que le sharding dans MongoDB et pourquoi est-il utilisé ?

Le sharding est un mécanisme de **partitionnement horizontal** des données : Une collection est découpée en fragments appelés **chunks**, répartis sur plusieurs serveurs appelés **shards**.

Il est utilisé pour :
- gérer de **très grands volumes de données**,
- assurer la **scalabilité horizontale**,
- répartir la **charge en lecture et en écriture** entre plusieurs machines.

---

## 2. Différence entre sharding et réplication

- **Réplication** : duplication des mêmes données sur plusieurs nœuds afin d’assurer la **tolérance aux pannes** et d’améliorer les performances en lecture.
- **Sharding** : répartition de données différentes sur plusieurs nœuds afin de permettre le **passage à l’échelle**.

Dans MongoDB, chaque shard est généralement un **replica set**.

---

## 3. Composants d’une architecture shardée

Une architecture shardée MongoDB est composée de :
- **Shards** : stockent les données (chaque shard est un replica set),
- **Config servers** : stockent les métadonnées du cluster,
- **mongos** : routeur qui distribue les requêtes vers les shards.

---

## 4. Rôle des config servers (CSRS)

Les config servers :
- stockent les **métadonnées de sharding**,
- maintiennent la correspondance **chunk → shard**,
- conservent la configuration du cluster,
- coordonnent le **balancing**.

Sans config servers, le cluster devient inutilisable.

---

## 5. Rôle du routeur mongos

Le mongos :
- est le **point d’entrée des clients**,
- route les requêtes vers les shards concernés,
- agrège les résultats,
- ne stocke aucune donnée (stateless).

---

## 6. Comment MongoDB choisit-il le shard pour un document ?

MongoDB utilise la **clé de sharding** :
1. il identifie le **chunk** correspondant à la valeur de la clé,
2. il détermine le shard qui possède ce chunk,
3. le mongos envoie la requête au shard concerné.

---

## 7. Qu’est-ce qu’une clé de sharding ?

La clé de sharding est l’attribut (ou ensemble d’attributs) utilisé pour :
- partitionner les données,
- distribuer les chunks,
- router efficacement les requêtes.

Elle est essentielle au bon fonctionnement du sharding.

---

## 8. Critères d’une bonne clé de sharding

Une bonne clé de sharding doit :
- avoir une **forte cardinalité**,
- assurer une **distribution uniforme**,
- être **immuable** ou rarement modifiée,
- être utilisée dans les requêtes,
- éviter les valeurs monotones.

---

## 9. Qu’est-ce qu’un chunk ?

Un chunk est une **plage de valeurs de la clé de sharding**.  
Il constitue l’unité de base de distribution des données.

Taille par défaut : **128 Mo**.

---

## 10. Comment fonctionne le splitting des chunks ?

Lorsqu’un chunk dépasse la taille maximale :
- MongoDB le **divise en deux**,
- met à jour les métadonnées,
- peut déclencher une migration vers un autre shard.

---

## 11. Rôle du balancer

Le balancer :
- surveille la répartition des chunks,
- déplace les chunks entre shards,
- assure l’**équilibrage de la charge**,
- fonctionne en arrière-plan.

---

## 12. Quand et comment le balancer agit-il ?

Le balancer agit :
- lorsqu’un déséquilibre est détecté,
- en migrant des chunks entre shards,
- de préférence pendant les périodes de faible charge.

---

## 13. Qu’est-ce qu’un hot shard ?

Un hot shard est un shard surchargé en lectures ou écritures.  
Il est généralement causé par une **mauvaise clé de sharding**.

Conséquence : baisse des performances globales.

---

## 14. Problèmes d’une clé de sharding monotone

Une clé monotone (date croissante, compteur) entraîne :
- des insertions sur un seul shard,
- un déséquilibre important,
- une saturation des ressources.

---

## 15. Comment activer le sharding sur une base et une collection ?

```
sh.enableSharding("mabasefilms")
sh.shardCollection("mabasefilms.films", { titre: 1 })

```

## 16. Comment ajouter un nouveau shard ?

Pour augmenter la capacité du cluster ou répartir la charge, on peut ajouter un nouveau shard à l’architecture MongoDB.

L’ajout se fait via le routeur mongos avec la commande suivante :


`sh.addShard("replicashard3/localhost:20006")`



## 17. Comment vérifier l’état du cluster shardé ?

MongoDB fournit plusieurs commandes pour vérifier l’état du cluster shardé :


```
sh.status() :Affiche l’état global du cluster, la liste des shards et la distribution des chunks.  

db.printShardingStatus() : Affiche une vue détaillée de la configuration du sharding (selon la version de MongoDB).  

db.stats() : Fournit des statistiques générales sur la base de données.

```



---

## 18. Quand utiliser une clé de sharding hashée ?

Une clé de sharding **hashée** est adaptée lorsque :
- la charge d’écriture est élevée,
- les accès sont uniformément répartis,
- les requêtes par intervalle ne sont pas nécessaires.

Le hachage permet une **distribution homogène des données**, mais empêche les requêtes de type range.

---

## 19. Quand privilégier une clé de sharding par intervalles ?

Une clé de sharding par intervalles est préférable pour :
- les requêtes de type **range**,
- les analyses temporelles,
- les agrégations par plage de valeurs.

Ce type de clé conserve l’ordre des valeurs, mais peut provoquer des **hot shards** si les valeurs sont monotones.

---

## 20. Qu’est-ce que le zone sharding ?

Le zone sharding permet d’associer certaines **plages de valeurs de la clé de sharding** à des shards spécifiques.

Il est utile pour :
- les contraintes géographiques,
- la conformité réglementaire,
- l’isolation logique des données.

---

## 21. Comment MongoDB gère-t-il les requêtes multi-shards ?

Lorsque la requête ne contient pas la clé de sharding, le mongos :
- envoie la requête à tous les shards concernés,
- collecte les résultats,
- les agrège avant de les retourner au client.

Ces requêtes sont plus coûteuses que les requêtes ciblées.

---

## 22. Comment optimiser les performances en environnement shardé ?

Pour optimiser les performances :
- choisir une bonne clé de sharding,
- utiliser des index adaptés,
- éviter les requêtes multi-shards,
- utiliser des clés hashées pour les écritures massives.

---

## 23. Que se passe-t-il lorsqu’un shard devient indisponible ?

Si le shard est un **replica set** :
- un **failover automatique** est déclenché.

Sinon :
- certaines requêtes échouent,
- les données hébergées sur ce shard deviennent temporairement inaccessibles.

---

## 24. Comment migrer une collection existante vers un schéma shardé ?

Pour migrer une collection existante :
- activer le sharding sur la base,
- créer les index nécessaires,
- shard(er) la collection,
- laisser le balancer redistribuer les données progressivement.

Cette migration peut se faire sans arrêter le cluster.

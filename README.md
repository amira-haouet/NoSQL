# Projet NoSQL – MongoDB, Redis et MapReduce

## Présentation générale

Ce dépôt regroupe l’ensemble des **travaux pratiques réalisés dans le cadre du module Bases de données NoSQL**.  
Il couvre les concepts fondamentaux liés aux bases de données distribuées et NoSQL, notamment :

- les bases de MongoDB,
- la réplication et la tolérance aux pannes,
- le partitionnement (sharding),
- le traitement de données avec MapReduce,
- une introduction aux bases clé–valeur avec Redis.

L’objectif est de comprendre **comment les systèmes NoSQL permettent de gérer de grands volumes de données**, tout en assurant performance, disponibilité et passage à l’échelle.

Les différents travaux sont organisés sous forme de fichiers Markdown, chacun correspondant à un TP spécifique.

---

## Travaux pratiques

### 1. Introduction à MongoDB

Ce TP présente les bases de MongoDB et le modèle orienté documents :
- documents JSON / BSON,
- bases et collections,
- opérations CRUD,
- premières requêtes.

 Lien :  
[TP_intro_MongoDB.md](./TP_intro_MongoDB.md)

---

### 2. Manipulation de MongoDB

Ce TP approfondit l’utilisation de MongoDB :
- insertion et lecture de documents,
- requêtes avec filtres et projections,
- manipulation avancée des données.

 Lien :  
[TP_MongoDB.md](./TP_MongoDB.md)

---

### 3. Réplication MongoDB

Ce TP est consacré à la **réplication et à la tolérance aux pannes** :
- replica sets,
- rôles PRIMARY / SECONDARY,
- mécanismes de failover,
- impact sur la cohérence et la disponibilité.

 Lien :  
[TP2_replication.md](./TP2_replication.md)

---

### 4. Partitionnement (Sharding)

Ce TP traite du **partitionnement horizontal des données** dans MongoDB :
- architecture shardée,
- shards, config servers et routeur mongos,
- clé de sharding, chunks et balancer,
- bonnes pratiques et problèmes courants (hot shards).

 Lien :  
[TP3_Partitionnement.md](./TP3_Partitionnement.md)

---

### 5. MapReduce avec MongoDB

Ce TP introduit le modèle de calcul **MapReduce** :
- principe des fonctions Map et Reduce,
- agrégation distribuée,
- application du modèle avec MongoDB.

 Lien principal :  
[TP_4_MapReduce.md](./TP_4_MapReduce.md)

 Ressources complémentaires :  
[MapReduce_MongoDB/](./MapReduce_MongoDB)

---

### 6. Redis – Base de données clé–valeur

Ce TP présente Redis et les bases de données clé–valeur :
- principes fondamentaux,
- cas d’usage,
- différences avec MongoDB.

 Lien :  
[TP_Redis.md](./TP_Redis.md)

---

## Compétences acquises

- compréhension des architectures distribuées,
- manipulation de bases NoSQL orientées documents et clé–valeur,
- mise en œuvre de la réplication et du sharding,
- analyse des compromis CAP / BASE,
- traitement distribué de données avec MapReduce.

---

## Ce README constitue le **point d’entrée général** du projet.

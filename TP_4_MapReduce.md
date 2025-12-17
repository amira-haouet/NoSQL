# TP n°4 – MapReduce avec CouchDB
## Partie 1 : Introduction simple au MapReduc

## CouchDB

CouchDB est un système de gestion de base de données NoSQL orienté documents. Contrairement aux bases relationnelles classiques qui utilisent des tables, CouchDB stocke les données sous forme de documents JSON. Chaque document est indépendant et peut avoir une structure différente, ce qui offre une grande souplesse dans la modélisation des données.

CouchDB est souvent choisi pour sa simplicité de mise en œuvre et son orientation vers le web. Il est particulièrement adapté aux applications distribuées et aux environnements où les données évoluent fréquemment. Son fonctionnement repose directement sur HTTP, ce qui facilite son intégration avec des applications web et des API REST.

## Fonctionnement

CouchDB fonctionne à l’aide de requêtes HTTP standards telles que GET, PUT, POST et DELETE. Chaque base de données et chaque document sont accessibles via une URL. Une interface graphique intégrée permet également de consulter et manipuler les données sans écrire de code.

Pour réaliser des traitements analytiques et des agrégations sur les données, CouchDB utilise le modèle MapReduce. Ce mécanisme permet de parcourir l’ensemble des documents et de produire des résultats agrégés de manière efficace.

## Utilité

CouchDB est principalement utilisé pour gérer de grandes quantités de données semi-structurées ou non structurées. Il est particulièrement pertinent dans des contextes distribués, où la tolérance aux pannes, la disponibilité des données et la simplicité d’accès sont essentielles. Il convient bien aux applications web, mobiles ou collaboratives nécessitant une synchronisation et une forte flexibilité des données.



1. Qu’est-ce que MapReduce ?

MapReduce est un modèle de calcul utilisé pour traiter de grandes quantités de données.

Il se déroule en deux étapes :

Map : on transforme chaque document indépendamment

Reduce : on regroupe et on additionne les résultats
L’avantage principal est que les calculs peuvent être parallélisés.

2. CouchDB et MapReduce

CouchDB est une base de données orientée documents (JSON).

Elle intègre MapReduce via des vues :

- la fonction Map lit chaque document,

- la fonction Reduce agrège les résultats,

- les résultats sont stockés et réutilisables.




## Question 1
Modélisation en documents 

Chaque document représente une page web et ses liens sortants.

{
  "_id": "page_1",
  "page": 1,
  "links": [
    { "to": 2, "weight": 0.5 },
    { "to": 3, "weight": 0.2 }
  ]
}


 L’ensemble des documents forme la collection C.

## Question 2
Calcul de la norme d’une ligne 
	​
```
Map
function (doc) {
  doc.links.forEach(function (l) {
    emit(doc.page, l.weight * W[l.to]);
  });
}

Reduce
function (keys, values) {
  var sum = 0;
  values.forEach(v => sum += v);
  return sum;
}
```
## Calcul de la norme des vecteurs

![image](img_tp3_tp4/norm.png "Titre de l'image")


# Partie 2

Cette partie s’appuie sur la collection de films utilisée lors du TP n°1. L’objectif est de se familiariser avec l’écriture de fonctions MapReduce dans MongoDB. Les résultats obtenus doivent être déposés dans le dépôt Git.

1. Nombre total de films

```
map = function () {
  emit("total", 1);
};

reduce = function (key, values) {
  return Array.sum(values);
};

db.movies.mapReduce(map, reduce, { out: "total_films" });

2. Nombre de films par genre
map = function () {
  if (this.genres) {
    this.genres.forEach(g => emit(g, 1));
  }
};
```
```

reduce = function (key, values) {
  return Array.sum(values);
};

db.movies.mapReduce(map, reduce, { out: "films_par_genre" });

```

3. Nombre de films par réalisateur

```

map = function () {
  if (this.directors) {
    this.directors.forEach(d => emit(d, 1));
  }
};

reduce = function (key, values) {
  return Array.sum(values);
};

db.movies.mapReduce(map, reduce, { out: "films_par_realisateur" });

```

4. Nombre d’acteurs uniques

```

map = function () {
  if (this.cast) {
    this.cast.forEach(a => emit(a, 1));
  }
};

reduce = function (key, values) {
  return 1;
};

db.movies.mapReduce(map, reduce, { out: "acteurs_uniques" });

db.acteurs_uniques.count()
```

5. Nombre de films par année

```

map = function () {
  emit(this.year, 1);
};

reduce = function (key, values) {
  return Array.sum(values);
};

db.movies.mapReduce(map, reduce, { out: "films_par_annee" });

```

6. Note moyenne par film

```

map = function () {
  if (this.grades) {
    this.grades.forEach(g => emit(this.title, g.score));
  }
};

reduce = function (key, values) {
  return Array.sum(values) / values.length;
};

db.movies.mapReduce(map, reduce, { out: "note_moyenne_par_film" });

```

7. Note moyenne par genre

```

map = function () {
  if (this.genres && this.grades) {
    let avg = this.grades.reduce((s,g)=>s+g.score,0) / this.grades.length;
    this.genres.forEach(g => emit(g, avg));
  }
};

reduce = function (key, values) {
  return Array.sum(values) / values.length;
};

db.movies.mapReduce(map, reduce, { out: "note_moyenne_par_genre" });

```

8. Note moyenne par réalisateur

```

map = function () {
  if (this.directors && this.grades) {
    let avg = this.grades.reduce((s,g)=>s+g.score,0) / this.grades.length;
    this.directors.forEach(d => emit(d, avg));
  }
};

reduce = function (key, values) {
  return Array.sum(values) / values.length;
};

db.movies.mapReduce(map, reduce, { out: "note_moyenne_par_realisateur" });

```

9. Film avec la note maximale

```

map = function () {
  if (this.grades) {
    this.grades.forEach(g => emit(this.title, g.score));
  }
};

reduce = function (key, values) {
  return Math.max.apply(null, values);
};

db.movies.mapReduce(map, reduce, { out: "note_max_film" });

db.note_max_film.find().sort({ value: -1 }).limit(1)
```

10. Nombre de notes supérieures à 70


```

map = function () {
  if (this.grades) {
    this.grades.forEach(g => {
      if (g.score > 70) emit("sup70", 1);
    });
  }
};

reduce = function (key, values) {
  return Array.sum(values);
};

db.movies.mapReduce(map, reduce, { out: "notes_sup_70" });
```

11. Acteurs par genre (sans doublons)

```
map = function () {
  if (this.genres && this.cast) {
    this.genres.forEach(g => {
      this.cast.forEach(a => emit(g, a));
    });
  }
};

reduce = function (key, values) {
  return Array.from(new Set(values));
};

db.movies.mapReduce(map, reduce, { out: "acteurs_par_genre" });
```

12. Acteurs apparaissant dans le plus de films

```
map = function () {
  if (this.cast) {
    this.cast.forEach(a => emit(a, 1));
  }
};

reduce = function (key, values) {
  return Array.sum(values);
};

db.movies.mapReduce(map, reduce, { out: "films_par_acteur" });

db.films_par_acteur.find().sort({ value: -1 }).limit(1)
```

13. Films classés par lettre de grade

```
map = function () {
  if (this.grades) {
    this.grades.forEach(g => emit(g.grade, this.title));
  }
};

reduce = function (key, values) {
  return values;
};

db.movies.mapReduce(map, reduce, { out: "films_par_lettre" });
```

14. Note moyenne par année
map = function () {
  if (this.grades) {
    let avg = this.grades.reduce((s,g)=>s+g.score,0) / this.grades.length;
    emit(this.year, avg);
  }
};

reduce = function (key, values) {
  return Array.sum(values) / values.length;
};

db.movies.mapReduce(map, reduce, { out: "note_moyenne_par_annee" });

16. Réalisateurs avec une note moyenne supérieure à 80

```
map = function () {
  if (this.directors && this.grades) {
    let avg = this.grades.reduce((s,g)=>s+g.score,0) / this.grades.length;
    this.directors.forEach(d => emit(d, avg));
  }
};

reduce = function (key, values) {
  return Array.sum(values) / values.length;
};

db.movies.mapReduce(map, reduce, { out: "realisateurs_notes" });

db.realisateurs_notes.find({ value: { $gt: 80 } })
```
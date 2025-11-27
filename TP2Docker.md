# Partie 1 — Filtrer et projeter les données

---

# Partie 1 — Filtrer et projeter les données

---

## 1. Compter le nombre total de films importés
```mongodb
db.films.count()
```

---

## 2. Examiner la structure d’un document
```mongodb
db.films.findOne()
```

---

## 3. Afficher les films d’action
```mongodb
db.films.find({ genre: "Action" })
```

---

## 4. Nombre de films d’action
```mongodb
db.films.count({ genre: "Action" })
```

---

## 5. Films d’action produits en France
```mongodb
db.films.find({ genre: "Action", pays: "France" })
```

---

## 6. Films d’action produits en France en 1963
```mongodb
db.films.find({
  genre: "Action",
  pays: "France",
  annee: 1963
})
```

---

## 7. Films d’action en France sans afficher les grades
```mongodb
db.films.find(
  { genre: "Action", pays: "France" },
  { grade: 0 }
)
```

---

## 8. Supprimer l’affichage de _id
```mongodb
db.films.find(
  { genre: "Action", pays: "France" },
  { _id: 0, grade: 0 }
)
```

---

## 9. Afficher uniquement le titre et les grades
```mongodb
db.films.find(
  { genre: "Action", pays: "France" },
  { _id: 0, titre: 1, grade: 1 }
)
```

---

## 10. Films d’action français ayant une note > 10
```mongodb
db.films.find(
  { genre: "Action", pays: "France", grade: { $gt: 10 } },
  { _id: 0, titre: 1, grade: 1 }
)
```

---

## 11. Films dont toutes les notes sont > 10
```mongodb
db.films.find({
  grade: { $elemMatch: { $gt: 10 } }
})
```

---

## 12. Afficher tous les genres disponibles
```mongodb
db.films.distinct("genre")
```

---

## 13. Afficher tous les grades disponibles
```mongodb
db.films.distinct("grade")
```

---

## 14. Films contenant au moins un des artistes donnés
```mongodb
db.films.find({
  artistes: { $in: ["artist:4", "artist:18", "artist:11"] }
})
```

---

## 15. Films sans résumé
```mongodb
db.films.find({ resume: { $exists: false } })
```

---

## 16. Films avec Leonardo DiCaprio en 1997
```mongodb
db.films.find({
  acteurs: "Leonardo DiCaprio",
  annee: 1997
})
```

---

## 17. Films avec Leonardo DiCaprio OU films de 1997
```mongodb
db.films.find({
  $or: [
    { acteurs: "Leonardo DiCaprio" },
    { annee: 1997 }
  ]
})
```

---

# Partie 2 — Agrégations MongoDB

---

## 18. Nombre de films par année
```mongodb
db.films.aggregate([
  { $group: { _id: "$annee", total: { $sum: 1 } } },
  { $sort: { _id: 1 } }
])
```

---

## 19. Moyenne des notes par genre
```mongodb
db.films.aggregate([
  { $unwind: "$genre" },
  { $group: { _id: "$genre", moyenne: { $avg: "$grade" } } },
  { $sort: { moyenne: -1 } }
])
```

---

## 20. Nombre de films par pays
```mongodb
db.films.aggregate([
  { $unwind: "$pays" },
  { $group: { _id: "$pays", total: { $sum: 1 } } },
  { $sort: { total: -1 } }
])
```

---

# Partie 3 — Mises à jour

---

## 21. Ajouter un champ etat
```mongodb
db.films.updateOne(
  { titre: "Jaws" },
  { $set: { etat: "culte" } }
)
```

---

## 22. Incrémenter le nombre de votes
```mongodb
db.films.updateOne(
  { titre: "Inception" },
  { $inc: { votes: 100 } }
)
```

---

## 23. Supprimer un champ
```mongodb
db.films.updateMany(
  {},
  { $unset: { poster: "" } }
)
```

---

## 24. Modifier le réalisateur d’un film
```mongodb
db.films.updateOne(
  { titre: "Titanic" },
  { $set: { realisateur: "James Cameron" } }
)
```

---

# Partie 4 — Requêtes complexes

---

## 25. Films les mieux notés par décennie
```mongodb
db.films.aggregate([
  { $match: { grade: { $exists: true } }},
  { $project: {
      titre: 1,
      decade: { $subtract: ["$annee", { $mod: ["$annee", 10] }] },
      grade: 1
  }},
  { $group: { _id: "$decade", maxRating: { $max: "$grade" } }},
  { $sort: { _id: 1 }}
])
```

---

## 26. Films dont le titre commence par "Star"
```mongodb
db.films.find(
  { titre: /^Star/ },
  { titre: 1 }
)
```

---

## 27. Films avec plus de 2 genres
```mongodb
db.films.find(
  { $where: "this.genre.length > 2" },
  { titre: 1, genre: 1 }
)
```

---

## 28. Films de Christopher Nolan
```mongodb
db.films.find(
  { realisateur: "Christopher Nolan" },
  { titre: 1, annee: 1, grade: 1 }
)
```

---

# Partie 5 — Indexation

---

## 29. Créer un index
```mongodb
db.films.createIndex({ annee: 1 })
```

---

## 30. Voir les index existants
```mongodb
db.films.getIndexes()
```

---

## 31. Analyse avec explain()
```mongodb
db.films.find({ annee: 1995 }).explain("executionStats")
```

---

## 32. Supprimer un index
```mongodb
db.films.dropIndex({ annee: 1 })
```

---

## 33. Créer un index composé
```mongodb
db.films.createIndex({ annee: 1, grade: -1 })
```

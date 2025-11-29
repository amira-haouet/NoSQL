
### Commandes utiles

| Commande | Syntax        | Exemple             | Output |
| -------- | ------------- | ------------------- | ------ |
| SET      | SET key value | `SET  ""` | ""   |

Définit la clé pour qu'elle contienne la valeur de la chaîne de caractères. Si la clé contient déjà une valeur, celle-ci est écrasée, quel que soit son type : O(1)

---

| Commande | Syntax  | Exemple     | Output  |
| -------- | ------- | ----------- | ------- |
| GET      | GET key | `GET ` | "" |

Obtenir la valeur de la chaîne de caractères de la clé. Si la clé n'existe pas, la valeur spéciale nil est renvoyée : O(1)

---

| Commande | Syntax             | Exemple          | Output              |
| -------- | ------------------ | ---------------- | ------------------- |
| MGET     | MGET key [key ...] | `MGET  ` | 1) "Hello" 2) (nil) |

Renvoie les valeurs de toutes les clés spécifiées : O(N)

---

| Commande | Syntax        | Exemple          | Output      |
| -------- | ------------- | ---------------- | ----------- |
| INCR     | INCR compteur | `INCR ` | (integer) 1 |

Augmente d'une unité le nombre stocké dans la clé : O(1)

---

| Commande | Syntax       | Exemple    | Output                    |
| -------- | ------------ | ---------- | ------------------------- |
| KEYS     | KEYS pattern | `` | |

Renvoie toutes les clés correspondant au motif : O(N)

---

| Commande | Syntax               | Exemple            | Output      |
| -------- | -------------------- | ------------------ | ----------- |
| EXISTS   | EXISTS key [key ...] | `EXISTS `     | (integer) 1 |

Vérifie si une ou plusieurs clés existent : O(N)

---

| Commande | Syntax             | Exemple            | Output      |
| -------- | ------------------ | ------------------ | ----------- |
| EXPIRE   | EXPIRE key seconds | `EXPIRE  ` | (integer) 1 |

Définit un délai d’expiration sur une clé.

---

| Commande | Syntax  | Exemple     | Output        |
| -------- | ------- | ----------- | ------------- |
| TTL      | TTL key | `TTL myKey` | (integer) 113 |

Renvoie le temps restant avant expiration.

---

| Commande | Syntax      | Exemple         | Output      |
| -------- | ----------- | --------------- | ----------- |
| PERSIST  | PERSIST key | `PERSIST myKey` | (integer) 1 |

Supprime l’expiration d’une clé.

---

| Commande | Syntax           | Exemple     | Output      |
| -------- | ---------------- | ----------- | ----------- |
| DEL      | DEL key [key...] | `DEL myKey` | (integer) 1 |

Supprime une clé.

---

| Commande | Syntax         | Exemple                          | Output |
| -------- | -------------- | -------------------------------- | ------ |
| INFO     | INFO [section] | `INFO server` ou `INFO keyspace` | ...    |

Affiche des informations sur le serveur Redis.

---

| Commande | Syntax                       | Exemple              | Output      |
| -------- | ---------------------------- | -------------------- | ----------- |
| SADD     | SADD key member [member ...] | `SADD mySet "Hello"` | (integer) 1 |

Ajoute un élément dans un set.

---

| Commande | Syntax       | Exemple          | Output     |
| -------- | ------------ | ---------------- | ---------- |
| SMEMBERS | SMEMBERS key | `SMEMBERS mySet` | 1) "Hello" |

Renvoie tous les membres d’un set.


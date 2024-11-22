# Introduction à Redis et aux Bases de Données NoSQL

## Qu'est-ce que le NoSQL ?

Le terme **NoSQL** désigne une catégorie de bases de données qui ne suivent pas le modèle relationnel classique. Contrairement aux bases de données relationnelles (SQL), les bases NoSQL sont conçues pour :
- Une meilleure **flexibilité** dans la gestion des données.
- Une **scalabilité horizontale** (ajout de serveurs pour gérer la montée en charge).
- Le traitement de grandes quantités de données **non structurées** ou **semi-structurées**.

### Types de bases de données NoSQL

1. **Clé-Valeur** :
   - Les données sont stockées sous forme de paires clé-valeur.
   - Idéal pour des cas simples où chaque clé unique fait référence à une valeur spécifique.
   - **Exemples** : Redis, Amazon DynamoDB.

2. **Colonnes** :
   - Les données sont organisées en colonnes plutôt qu’en lignes.
   - Convient pour les analyses rapides de grandes quantités de données.
   - **Exemples** : Apache Cassandra, HBase.

3. **Documents** :
   - Les données sont stockées sous forme de documents structurés (JSON, BSON, XML, etc.).
   - Idéal pour les applications web et mobiles.
   - **Exemples** : MongoDB, Couchbase.

4. **Graphes** :
   - Les données sont représentées sous forme de graphes.
   - Parfait pour les systèmes où les relations complexes entre données sont cruciales.
   - **Exemples** : Neo4j, ArangoDB.

---

## Qu'est-ce que Redis ?

**Redis** (REmote DIctionary Server) est une base de données NoSQL en mémoire rapide et open source. Utilisée principalement comme cache, courtier de messages ou base de données clé-valeur, elle est réputée pour ses performances élevées et sa simplicité d'utilisation. Ce guide vous permettra de maîtriser Redis à travers des exemples pratiques et d'explorer ses structures de données pour l'utiliser efficacement dans vos projets.


## 1. Démarrage de Redis

### Lancer le serveur Redis
**redis-server**
```bash
redis-server
```
### Démarrer un client Redis: redis-cli
```bash
redis-cli
```
📝 Remarque : Redis écoute par défaut sur le port 6379. Il existe également des clients graphiques pour interagir avec Redis.

## 2. Gestion des clés (CRUD)
### Créer une clé : SET clé valeur
```bash
SET demo "Bonjour"
```
Ici, la clé demo est créée avec la valeur "Bonjour".

### Lire une valeur : GET clé
```bash
GET demo #Résultat : "Bonjour"
```

### Supprimer une clé : DEL clé
```bash
DEL demo
```
Retourne :
1 si la clé est supprimée.
0 si la clé n'existe pas.

### Incrémenter une valeur
```bash
SET compteur 0
INCR compteur  # Résultat : 1
```
### Décrémenter une valeur
```bash
DECR compteur # Résultat : 0
```


## 3. Durée de vie d'une clé
### Vérifier la durée de vie d'une clé: TTL clé
```bash
TTL macle
```
Retourne :
- -1 si la clé n’a pas de durée de vie (elle est permanente).
- Une valeur positive correspondant au nombre de secondes restantes.

### Définir une durée de vie: EXPIRE clé secondes
```bash
EXPIRE macle 120
```
La clé macle expire après 120 secondes.

## 4. Structures de données Redis
Redis prend en charge plusieurs structures de données avancées.
### 4.1 Listes
Les listes permettent de gérer des données ordonnées, même redondantes, et sont particulièrement utiles pour des files d'attente ou des journaux d'événements, comme dans un historique de chat ou une file de tâches. 
#### Ajouter des éléments
```bash
RPUSH mesCours "BDA"
LPUSH mesCours "NoSQL"
```
**RPUSH** : Ajoute à droite.
**LPUSH** : Ajoute à gauche.

#### Afficher les éléments
```bash
LRANGE mesCours 0 -1 # Tous les éléments
LRANGE mesCours 0 0   # Premier élément
```
#### Supprimer un élément
```bash
LPOP mesCours  # Supprime le premier élément
RPOP mesCours  # Supprime le dernier élément
```
### 4.2 Ensembles (Sets)
Les ensembles garantissent l'unicité des éléments, sans ordre spécifique, ce qui les rend idéaux pour des cas comme le suivi des utilisateurs uniques connectés ou des tags.

#### Ajouter des éléments
```bash
SADD utilisateurs "Alice"
SADD utilisateurs "Bob"
```
#### Afficher les éléments
```bash
SMEMBERS utilisateurs
```
#### Supprimer un élément
```bash
SREM utilisateurs "Augustin"
```
#### Union de deux ensembles: SUNION ensemble1 ensemble2
```bash
SADD autresUtilisateurs "Malek"
SUNION utilisateurs autresUtilisateurs
```
### 3. Ensembles Ordonnés (Sorted Sets)
Les ensembles ordonnés, en plus d'assurer l'unicité, classent les données par un score numérique, ce qui les rend parfaits pour des classements, par exemple dans les systèmes de recommandation ou les scores de jeu.
#### Ajouter des données : ZADD nom_du_set score valeur
```bash
ZADD scores 10 "Alice"
ZADD scores 15 "Bob"
```
#### Afficher les données : ZRANGE nom_du_set premier_indice dernier_indice
```bash
ZRANGE scores 0 -1      # Ordre croissant: ["Alice", "Bob"]
ZREVRANGE scores 0 -1   # Ordre décroissant: ["Bob", "Alice"]
```
#### Trouver la position d’un élément : ZRANK nom_du_set valeur
```bash
ZRANK scores "Alice"
```
retournera son indice (commençant à 0). Si "Alice" n'existe pas dans le set, la commande retourne nil.

### 4. Hachages (Hashes)
Les hachages organisent des données structurées sous forme de paires clé-valeur dans une clé principale, facilitant la gestion des profils utilisateur ou des configurations.
#### Ajouter des données : HSET hash champ valeur
```bash
HSET utilisateur:1 nom "Alice"
HSET utilisateur:1 age "30"
```
#### Afficher les données : HGETALL hash
```bash
HGETALL utilisateur:1
```
Résultat :
1) "nom"
2) "Alice"
3) "age"
4) "30"

### 5. Pub/Sub (Publication/Souscription)
le système Pub/Sub permet des communications en temps réel entre émetteurs et récepteurs, ce qui est indispensable pour des applications comme les systèmes de messagerie ou les notifications instantanées.

#### Souscrire à un canal : SUBSCRIBE canal
```bash
SUBSCRIBE canal
```
#### Publier un message : PUBLISH canal "Message"
```bash
PUBLISH canal "Message"
```
#### Scénario : 
##### Lancer le serveur Redis et les clients
Dans un terminal, démarrez le serveur Redis et ouvrir deux clients Redis:
Terminal Client 1 : Ce client s'abonne à un canal.
Terminal Client 2 : Ce client publie des messages.
##### 1. Abonnement à un canal
Dans Terminal Client 1, abonnez-vous à un canal nommé mesCours :
```bash
SUBSCRIBE mesCours
```
Ce terminal reste à l'écoute pour recevoir tous les messages publiés sur le canal mesCours.

##### 2. Publier un message sur un canal
Dans Terminal Client 2, publiez un message dans le canal mesCours :
```bash
PUBLISH mesCours "Un nouveau cours sur NoSQL"
```

Résultat dans Terminal Client 1 :
```bash
1) "message"
2) "mesCours"
3) "Un nouveau cours sur NoSQL"
```
##### 3. Envoyer un message spécifique à un utilisateur
Pour envoyer un message spécifique à un utilisateur nommé user:1, utilisez la commande PUBLISH avec le canal correspondant :
```bash
PUBLISH user:1 "Bonjour user 1!"
```
Résultat pour l'abonné : Si user:1 est abonné à ce canal, il recevra instantanément le message :
```bash
1) "message"
2) "user:1"
3) "Bonjour user 1!"
```
##### 4. S'abonner à plusieurs canaux avec des motifs
Redis permet de s'abonner à plusieurs canaux utilisant un motif. Par exemple, pour s'abonner à tous les canaux commençant par mes, utilisez :
```bash
PSUBSCRIBE mes*
```
Résultat : Vous recevrez tous les messages des canaux comme mesCours, mesNotifications, etc.

## Fonctions Utiles de Redis
Redis offre plusieurs commandes pratiques pour explorer et gérer les données.

### 1. Afficher toutes les clés dans la base de données active : KEYS *
Redis permet de répertorier toutes les clés disponibles dans la session actuelle.
Exemple :
```bash
SET nom "Alice"
SET age "30"
KEYS *
```
Résultat :
```bash
1) "nom"
2) "age"
```
### 2. Bases de données Redis
Redis propose 16 bases de données par défaut, numérotées de 0 à 15. La base de données 0 est utilisée par défaut.

#### Changer de base de données: SELECT <numéro>
Exemple :
```bash
SELECT 1
```
Résultat : maintenant connecté à la base de données numéro 1.

#### Vérifier les clés dans une autre base
Chaque base de données Redis est isolée. Ainsi, les clés créées dans une base ne seront pas visibles dans une autre. Par exemple :

```bash
SET cleBase0 "valeur"
KEYS *
```
Résultat : ["cleBase0"]

```bash
SELECT 1
KEYS *
```
Résultat : Aucune clé n’est affichée car cette base est vide.

### 3. Vider une base de données: FLUSHDB
Pour supprimer toutes les clés d'une base spécifique, utilisez la commande FLUSHDB.
Exemple :
```bash
SET test "données"
KEYS *       # Résultat : ["test"]
FLUSHDB
KEYS *       # Résultat : []
```
### 4. Vider toutes les bases de données: FLUSHALL
Pour supprimer toutes les données dans toutes les bases de Redis, utilisez la commande FLUSHALL.

Exemple :
```bash
SET test "données"
SELECT 1
SET exemple "données2"
FLUSHALL
```
Résultat dans toutes les bases : Plus aucune clé.

















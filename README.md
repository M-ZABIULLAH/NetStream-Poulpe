# NetStream-Poulpe

## Pourquoi PostgreSQL pour notre SGBD

Dans le cadre de ce projet d'application pour un futur site de streaming et de recherche de films, nous avons fait le choix de **PostgreSQL** comme système de gestion de base de données relationnelle (SGBDR). Ce choix repose sur plusieurs critères techniques et contextuels, en lien direct avec la structure et les besoins de notre modèle.

- Dans un premier temps, **PostgreSQL nous offre un respect strict du modèle relationnel**, ce qui nous garantit l'intégrité des données grâce à sa gestion des clés primaires, étrangères et des contraintes. Nos schémas MPD, MLD et MPT comprennent plusieurs relations complexes (1,n et n,n), ainsi que des entités, dont l’une avec un suivi d’historique (`archive`), ce qui nécessite une base fiable et cohérente dans le temps.

- **Un système de triggers** nous a été demandé par le client pour l'entité `cinéphile`. PostgreSQL propose un système de triggers puissant et flexible, idéal pour automatiser la traçabilité des modifications, comme exigé par notre client. Grâce à PostgreSQL, nous pouvons également utiliser **des fonctions personnalisées**, comme celle qui nous permet, lors de la création d’un acteur ou d’une actrice, de lui attribuer un rôle directement rattaché à un film, ce qui facilite l’ajout d’informations dans notre base de données.

- Un autre avantage qui a motivé notre choix est **la richesse des fonctions natives**, notamment pour la manipulation des dates. Lors de la conception, nous avons été confrontés à deux possibilités : saisir la date de naissance des acteurs ou stocker directement leur âge. La date de naissance permet une meilleure mise à jour et évite des recalculs manuels. Avec PostgreSQL, le calcul de l’âge à partir de la date de naissance est directement possible grâce à des fonctions comme `AGE()` et `DATE_PART()`, sans nécessiter de logique additionnelle.

**PostgreSQL répond donc pleinement aux exigences fonctionnelles, techniques et évolutives** de notre projet, tout en s’adaptant à l'ajout futur de fonctionnalités telles que le streaming de films.

## Requetes SQL

### Les titres et dates de sortie des films du plus récent au plus ancien

```sql
SELECT title, release_date
FROM movies
ORDER BY release_date DESC;
```

### Les noms, prénoms et âges des acteurs/actrices de plus de 30 ans dans l'ordre alphabétique

```sql
SELECT first_name, last_name, DATE_PART('year', AGE(birth_date)) AS age
FROM actors
WHERE DATE_PART('year', AGE(birth_date)) < 30
ORDER BY last_name, first_name;
```

### La liste des acteurs/actrices principaux pour un film donné

```sql
SELECT a.first_name, a.last_name, c.character_type, m.title
FROM actors
JOIN acting act ON act.id = acting.actor_id
JOIN character c ON c.id = act.character_id
JOIN movie_character mc ON mc.id = c.movie_character_id
JOIN movies m ON m.id = mc.movie_id
WHERE m.title = 'Nom du film';
```

### La liste des films pour un acteur/actrice donné

```sql
SELECT m.title
FROM movies m
JOIN acting act ON act.movie_id = m.id
JOIN actor a ON a.id = act.actor_id
WHERE a.first_name = 'Prénom' AND a.last_name = 'Nom';
```

### Ajouter un film

```sql
INSERT INTO movie (movie_title,movie_lenght, movie_release_date)
VALUES ('Avenger Endgames', '2H15', '2021');
```

### Ajouter un acteur/actrice

```sql
INSERT INTO actor (actor_firstname, actor_lastname,actor_birthdate)
VALUES ('Scarlett','Johansson', '1984/11/22');
```

### Modifier un film

```sql
UPDATE MOVIE
SET movie_title = 'Pirate des Caraibes 5'
WHERE movie_id= 'ac558f45-d121-48c1-9415-8c86bbedfd0c'
```

### Afficher les 3 derniers acteurs/actrices ajouté(e)s

```sql
SELECT * FROM actor ORDER BY DESC created_at LIMIT 3;
```

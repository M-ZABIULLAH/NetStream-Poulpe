# NetStream-Poulpe

## Pourquoi PostegreSQL pour notre SGBD

Dans le cadre de ce projet d'application pour un futur site de streaming et de recherche de films, nous avons fait le choix de PostgreSQL comme système de gestion de base de données relationelle (SGBDR). Ce choix repose sur plusieurs critères techniques et contextuels en lien direct avec la structure et les besoin de notre modèle.

- Dans un premier temps, PostgreSQL nous offre un respect strict du modèle relationnel, ce qui nous garabtira l'intégrité des données grâce a sa gestion des clés primaire, étrangère et des contraintes. Nos schémas MPD MLD et MPT comprendent plusieurs relaxion complexes (1,n et n,n) ainsi que des entités dont l'un avec un suivi d'historique (archive), ce qui néccessite une base fiable et qui restera cohérente dans le temps.

- Comme un système de triggers, nous a été demander par le client pour l'entité cinéphile. PostgreSQL propose un sytème de triggers puissant et flexible, qui sera idéal pour l'automatisation, la tracabilité de modifications comme demandé par notre client. Nous pourrons également grâce à postgreSQL utiliser des fonctions personnalisées comme celle qui nous a permis lors de la création d'un acteur ou une actrice de lui créer un rôle qui sera alors également rattacher à un film, ce qui facilite les ajout dans notre base de données.

-

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

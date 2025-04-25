# NetStream-Poulpe

**PostgreSQL répond donc pleinement aux exigences fonctionnelles, techniques et évolutives** de notre projet, tout en s’adaptant à l'ajout futur de fonctionnalités telles que le streaming de films.

## Requetes SQL

### Les titres et dates de sortie des films du plus récent au plus ancien

```sql
SELECT movie_title, movie_release_date
FROM movie
ORDER BY movie_release_date DESC;
```

### Les noms, prénoms et âges des acteurs/actrices de plus de 30 ans dans l'ordre alphabétique

```sql
SELECT actor_lastname, actor_firstname, DATE_PART('year', AGE(actor_birthdate)) AS age
FROM actor
WHERE DATE_PART('year', AGE(actor_birthdate)) > 30
ORDER BY actor_lastname;
```

### La liste des acteurs/actrices principaux pour un film donné

```sql
SELECT actor_firstname, actor_lastname,c.character_type, m.movie_title FROM actor
JOIN acting act ON act.actor_id = actor.actor_id
JOIN character c ON c.character_id = act.character_id
JOIN movie_characters mv ON mv.character_id = c.character_id
JOIN movie m ON m.movie_id = mv.movie_id
WHERE character_type = 'Personnage principal'
AND m.movie_title = 'King Kong';
```

### La liste des films pour un acteur/actrice donné

```sql
SELECT actor_firstname, actor_lastname, m.movie_title FROM actor
JOIN acting act ON act.actor_id = actor.actor_id
JOIN character c ON c.character_id = act.character_id
JOIN movie_characters mv ON mv.character_id = c.character_id
JOIN movie m ON m.movie_id = mv.movie_id;
```

### Ajouter un film

```sql
INSERT INTO movie (movie_id, movie_title, movie_release_date, movie_length, director_id)
VALUES (gen_random_uuid(),'Sonic', '2015-01-10', '01:00:00', '770561c0-81e7-4140-bf51-7588f9a8ceaa' );
```

### Ajouter un acteur/actrice

```sql
INSERT INTO actor (actor_id,actor_firstname, actor_lastname,actor_birthdate)
VALUES (gen_random_uuid()'Scarlett','Johansson', '1984-11-22');
```

### Modifier un film

```sql
UPDATE movie
SET movie_title = 'Sonic',
    movie_release_date = '2015-01-10',
    movie_length = '01:20:00',
    director_id = '770561c0-81e7-4140-bf51-7588f9a8ceaa'
WHERE movie_id = '702a0dd6-12b5-4ea7-adc1-fab458f7f6b8';
```

## Supprimer un acteur/actrice

```sql
DELETE FROM actor
WHERE actor_id = '8b5b3470-264c-46d5-82f3-3e840b34a6b9';
```

### Afficher les 3 derniers acteurs/actrices ajouté(e)s

```sql
SELECT * FROM actor ORDER BY DESC created_at LIMIT 3;
```

# Brouillons de commandes SQL diverses

## Créer un réalisateur

```SQL
insert into director (director_id, director_firstname, director_lastname)
values (gen_random_uuid(), 'Steven', 'Spielsberg');
```

## Créer un film et le relier a un réalisateur (à améliorer)

```SQL
insert into movie (movie_id, movie_title, movie_release_date, movie_length, director_id)
values (gen_random_uuid(),'Sonic', '2015-01-10', '01:00:00', '770561c0-81e7-4140-bf51-7588f9a8ceaa' );
```

## Procédure pour créer et ajouter un acteur a un film

```SQL
CREATE PROCEDURE add_actor(
IN p_actor_id UUID,
IN p_firstname VARCHAR,
IN p_lastname VARCHAR,
IN p_birthdate DATE,
IN p_character_id UUID,
IN p_character_name VARCHAR,
IN p_character_type VARCHAR,
IN p_movie_id UUID
)
AS $$
BEGIN

INSERT INTO actor (actor_id, actor_firstname, actor_lastname, actor_birthdate)
VALUES (p_actor_id, p_firstname, p_lastname, p_birthdate);

INSERT INTO character (character_id, character_name, character_type)
VALUES (p_character_id, p_character_name, p_character_type);

INSERT INTO acting (actor_id,character_id)
VALUES (p_actor_id, p_character_id);

INSERT INTO movie_characters (movie_id, character_id)
VALUES (p_movie_id, p_character_id);
END;
$$ LANGUAGE plpgsql;
```

## Appele de la fonction pour créer un acteur et l'ajouter dans un film

```SQL
call add_actor(
gen_random_uuid(),
'John',
'Doe',
'1988-12-15',
gen_random_uuid(),
'Iron man',
'Protagoniste',
'702a0dd6-12b5-4ea7-adc1-fab458f7f6b8'
);
```

## Filtrer les personnages et leur films

```SQL
select character_name, movie.movie_title, a.actor_firstname, a.actor_lastname from character c
join movie_characters m on m.character_id = c.character_id
join movie  on movie.movie_id = m.movie_id
join acting on acting.character_id = c.character_id
join actor a on a.actor_id = acting.actor_id;
```

## Filtrer les film des plus récents au plus anciens

```SQL
SELECT movie_title, movie_release_date, movie_id
FROM movie
ORDER BY movie_release_date DESC;
```

## Filtrer les acteurs de moins de 30 ans

```SQL
SELECT actor_firstname, actor_lastname, EXTRACT(YEAR FROM AGE(actor_birthdate)) AS age
FROM actor
WHERE EXTRACT(YEAR FROM AGE(actor_birthdate)) <30
ORDER BY actor_lastname, actor_firstname;
```

## Filtrer les films pour un acteur

```SQL
select actor_firstname, actor_lastname, m.movie_title from actor
join acting act on act.actor_id = actor.actor_id
join character c on c.character_id = act.character_id
join movie_characters mv on mv.character_id = c.character_id
join movie m on m.movie_id = mv.movie_id;
```

## Filtrer les personnages principaux selon un film donné

```SQL
select actor_firstname, actor_lastname,c.character_type, m.movie_title from actor
join acting act on act.actor_id = actor.actor_id
join character c on c.character_id = act.character_id
join movie_characters mv on mv.character_id = c.character_id
join movie m on m.movie_id = mv.movie_id
where character_type = 'Protagoniste'
and m.movie_title = 'King Kong';
```

## Modifier un film

```SQL
UPDATE movie
SET movie_title = 'Sonic',
    movie_release_date = '2015-01-10',
    movie_length = '01:20:00',
    director_id = '770561c0-81e7-4140-bf51-7588f9a8ceaa'
WHERE movie_id = '702a0dd6-12b5-4ea7-adc1-fab458f7f6b8';
```

## Ajouter un acteur

```SQL
insert into actor (actor_id, actor_firstname, actor_lastname, actor_birthdate)
values (gen_random_uuid(),'Michel', 'Gilbert', '1949-09-25' );
```

## Supprimer un acteur

```SQL
DELETE FROM actor
WHERE actor_id = '8b5b3470-264c-46d5-82f3-3e840b34a6b9';
```

# SQL avancée

## CRUD (actor)

### CREATE

```SQL
CREATE PROCEDURE create_actor(
    IN p_actor_id UUID,
    IN p_actor_firstname VARCHAR,
    IN p_actor_lastname VARCHAR,
    IN p_actor_birthdate DATE
)
AS $$
BEGIN
    INSERT INTO actor (actor_id, actor_firstname, actor_lastname, actor_birthdate)
    VALUES (p_actor_id, p_actor_firstname, p_actor_lastname, p_actor_birthdate);
END;
$$ language plpgsql;
```

```SQL
call add_actor(
gen_random_uuid(),
'Axel',
'Houairi',
'2000-04-03'
);
```

### READ

```SQL
CREATE FUNCTION get_actor(
    p_actor_id UUID
)
RETURNS TABLE (
    actor_id UUID,
    actor_firstname VARCHAR,
    actor_lastname VARCHAR,
    actor_birthdate DATE
)
AS $$
BEGIN
    RETURN QUERY
    SELECT a.actor_id, a.actor_firstname, a.actor_lastname, a.actor_birthdate
    FROM actor a
    WHERE a.actor_id = p_actor_id;
END;
$$ LANGUAGE plpgsql;
```

```SQL
SELECT * FROM get_actor('d4f2e6b1-1654-4f9f-9b98-ce4ea95ee957');
```

### UPDATE

```SQL
CREATE PROCEDURE update_actor(
    IN p_actor_id UUID,
    IN p_actor_firstname VARCHAR DEFAULT NULL,
    IN p_actor_lastname VARCHAR DEFAULT NULL,
    IN p_actor_birthdate DATE DEFAULT NULL
)
AS $$
BEGIN
    UPDATE actor
    SET
        actor_firstname = COALESCE(p_actor_firstname, actor_firstname),
        actor_lastname = COALESCE(p_actor_lastname, actor_lastname),
        actor_birthdate = COALESCE(p_actor_birthdate, actor_birthdate)
    WHERE actor_id = p_actor_id;
END;
$$ language plpgsql;
```

```SQL
CALL update_actor(
    '38cdeef0-10d4-48a9-9297-6bc8dd8c51f9',
    'Armin',
    'Dejaeger',
    NULL
);
```

### DELETE

```SQL
CREATE PROCEDURE delete_actor(
    IN p_actor_id UUID
)
AS $$
BEGIN
    DELETE FROM actor
    WHERE actor_id = p_actor_id;
END;
$$ language plpgsql;
```

```SQL
CALL delete_actor('d4f2e6b1-1654-4f9f-9b98-ce4ea95ee957');
```

## Commandes diverses

### Voir les films d'un réalisateur donné

```SQL
CREATE OR REPLACE FUNCTION get_director_movie(p_director_id UUID)
RETURNS TABLE(director_firstname VARCHAR, director_lastname VARCHAR ,movie_title VARCHAR, movie_release_date DATE) AS
$$
BEGIN
    RETURN QUERY
    SELECT d.director_firstname, d.director_lastname, m.movie_title, m.movie_release_date
    FROM movie m
    JOIN director d ON d.director_id = m.director_id
	WHERE m.director_id = p_director_id;
END;
$$ LANGUAGE plpgsql;
```

```SQL
SELECT * from get_director_movie('f43ac5dd-2d37-4766-a3ee-1de1f05f2aa6');
```

### Lier un acteur à un personnage et à un film

```SQL
CREATE PROCEDURE add_actor_to_movie(
IN p_actor_id UUID,
IN p_character_id UUID,
IN p_movie_id UUID
)
AS $$
BEGIN

INSERT INTO acting (actor_id,character_id)
VALUES (p_actor_id, p_character_id);

INSERT INTO movie_characters (movie_id, character_id)
VALUES (p_movie_id, p_character_id);

END;
$$ language plpgsql;
```

```SQL
call add_actor_to_movie('ad9386f1-6447-4bc4-858d-37ca7d968076','5a3a21e4-dbfc-406b-aab1-bb189daf9ea9','5c38d0c4-c470-4df3-8d7b-07326b77a670');
```

### Créer un acteur et lui assigné un personnage puis l'ajouter dans un film

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
$$ language plpgsql;
```

```SQL
call add_actor(
gen_random_uuid(),
'Ezra',
'Odyn',
'1985-08-28',
gen_random_uuid(),
'Black Panther',
'Personnage principal',
'702a0dd6-12b5-4ea7-adc1-fab458f7f6b8'
);
```

### Trigger pour les modifications de compte

```SQL
CREATE OR REPLACE FUNCTION cinephile_logs()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.cinephile_firstname IS DISTINCT FROM OLD.cinephile_firstname THEN
        INSERT INTO archive (archive_id, archive_newvalue, archive_oldvalue, cinephile_id)
        VALUES (
            gen_random_uuid(),
            NEW.cinephile_firstname,
            OLD.cinephile_firstname,
            NEW.cinephile_id
        );
    ELSIF NEW.cinephile_lastname IS DISTINCT FROM OLD.cinephile_lastname THEN
        INSERT INTO archive (archive_id, archive_newvalue, archive_oldvalue, cinephile_id)
        VALUES (
            gen_random_uuid(),
            NEW.cinephile_lastname,
            OLD.cinephile_lastname,
            NEW.cinephile_id
        );
    ELSIF NEW.cinephile_mail IS DISTINCT FROM OLD.cinephile_mail THEN
        INSERT INTO archive (archive_id, archive_newvalue, archive_oldvalue, cinephile_id)
        VALUES (
            gen_random_uuid(),
            NEW.cinephile_mail,
            OLD.cinephile_mail,
            NEW.cinephile_id
        );
		 ELSIF NEW.cinephile_password IS DISTINCT FROM OLD.cinephile_password THEN
        INSERT INTO archive (archive_id, archive_newvalue, archive_oldvalue, cinephile_id)
        VALUES (
            gen_random_uuid(),
            NEW.cinephile_password,
            OLD.cinephile_password,
            NEW.cinephile_id
        );

    END IF;
    RETURN NEW;
END;
$$ language plpgsql;
```

```SQL
CREATE TRIGGER cinephile_trigger
AFTER UPDATE ON cinephile
FOR EACH ROW
EXECUTE FUNCTION cinephile_logs();
```

### Trigger pour la colonne updated_at

```SQL
CREATE OR REPLACE FUNCTION update_logs()
RETURNS TRIGGER AS $$
BEGIN
   NEW.updated_at = CURRENT_TIMESTAMP;
   RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

```SQL
CREATE TRIGGER actor_update_logs
BEFORE UPDATE ON actor
FOR EACH ROW
EXECUTE FUNCTION update_logs();
```

### Filtrer les personnages favoris des cinephiles

```SQL
SELECT ci.cinephile_firstname, ci.cinephile_lastname, c.character_name
FROM cinephile ci
JOIN character_bookmark cb ON ci.cinephile_id = cb.cinephile_id
JOIN character c ON c.character_id = cb.character_id;
```

### Filtrer les personnages favoris d'un cinephile donné

```SQL
CREATE OR REPLACE FUNCTION cinephile_fav_character(p_cinephile_id UUID)
RETURNS TABLE(cinephile_firstname VARCHAR, cinephile_lastname VARCHAR ,character_name VARCHAR) AS
$$
BEGIN
    RETURN QUERY
	SELECT ci.cinephile_firstname, ci.cinephile_lastname, c.character_name
	FROM cinephile ci
	JOIN character_bookmark cb ON ci.cinephile_id = cb.cinephile_id
	JOIN character c ON c.character_id = cb.character_id
	WHERE ci.cinephile_id = p_cinephile_id;
END;
$$ LANGUAGE plpgsql;
```

```SQL
SELECT * FROM cinephile_fav_character('a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d');
```

### Filtrer les films favoris des cinephiles

```SQL
SELECT cinephile_firstname, cinephile_lastname, movie_title
FROM cinephile ci
JOIN movie_bookmark mb ON ci.cinephile_id = mb.cinephile_id
JOIN movie m ON m.movie_id = mb.movie_id;
```

### Filtrer les films favoris d'un cinephile donné

```SQL
CREATE OR REPLACE FUNCTION cinephile_fav_movie(p_cinephile_id UUID)
RETURNS TABLE(cinephile_firstname VARCHAR, cinephile_lastname VARCHAR ,movie_title VARCHAR) AS
$$
BEGIN
    RETURN QUERY
	SELECT ci.cinephile_firstname, ci.cinephile_lastname, m.movie_title
	FROM cinephile ci
	JOIN movie_bookmark mb ON ci.cinephile_id = mb.cinephile_id
	JOIN movie m ON m.movie_id = mb.movie_id
	WHERE ci.cinephile_id = p_cinephile_id;
END;
$$ LANGUAGE plpgsql;
```

```SQL
SELECT * FROM cinephile_fav_movie('a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d');
```

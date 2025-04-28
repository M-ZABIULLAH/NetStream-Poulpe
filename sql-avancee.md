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
CREATE OR REPLACE FUNCTION get_director_movie(director_id UUID)
RETURNS TABLE(director_firstname VARCHAR, director_lastname VARCHAR ,movie_title VARCHAR, movie_release_date DATE) AS
$$
BEGIN
    RETURN QUERY
    SELECT d.director_firstname, d.director_lastname, m.movie_title, m.movie_release_date
    FROM director d
    JOIN movie m ON d.director_id = m.direcor_id
    WHERE d.director_id = p.director_id;
END;
$$ LANGUAGE plpgsql;
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

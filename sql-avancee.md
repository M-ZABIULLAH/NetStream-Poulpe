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

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

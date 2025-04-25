# Script SQL

## Table actor

```SQL
CREATE TABLE actor(
   actor_id UUID PRIMARY KEY,
   actor_firstname VARCHAR(50),
   actor_lastname VARCHAR(50),
   actor_birthdate DATE
);
```

## Table director

```SQL
CREATE TABLE director(
   director_id UUID PRIMARY KEY,
   director_firstname VARCHAR(50),
   director_lastname VARCHAR(50)
);
```

## Table character

```SQL
CREATE TABLE character(
   character_id UUID PRIMARY KEY,
   character_name VARCHAR(50),
   character_type VARCHAR(50)
);
```

## Table archive

```SQL
CREATE TABLE archive(
   archive_id UUID PRIMARY KEY,
   archive_newvalue VARCHAR(50),
   archive_oldvalue VARCHAR(50),
   archive_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
   cinephile_id UUID NOT NULL,
   FOREIGN KEY(cinephile_id) REFERENCES cinephile(cinephile_id)
);
```

## Table movie

```SQL
CREATE TABLE movie(
   movie_id UUID PRIMARY KEY,
   movie_title VARCHAR(200) ,
   movie_release_date DATE,
   movie_length TIME NOT NULL,
   director_id UUID NOT NULL,
   FOREIGN KEY(director_id) REFERENCES director(director_id)
);
```

## Table cinephile

```SQL
CREATE TABLE cinephile(
   cinephile_id UUID PRIMARY KEY,
   cinephile_firstname VARCHAR(50),
   cinephile_lastname VARCHAR(50),
   cinephile_mail VARCHAR(128),
   cinephile_password VARCHAR(64)
);
```

## Table acting

```SQL
CREATE TABLE acting(
   actor_id UUID NOT NULL,
   character_id UUID NOT NULL,
   PRIMARY KEY(actor_id, character_id),
   FOREIGN KEY(actor_id) REFERENCES actor(actor_id),
   FOREIGN KEY(character_id) REFERENCES character(character_id)
);
```

## Table movie_bookmark

```SQL
CREATE TABLE movie_bookmark(
   movie_id UUID NOT NULL,
   cinephile_id UUID NOT NULL,
   PRIMARY KEY(movie_id, cinephile_id),
   FOREIGN KEY(movie_id) REFERENCES movie(movie_id),
   FOREIGN KEY(cinephile_id) REFERENCES cinephile(cinephile_id)
);
```

## Table movie_characters

```SQL
CREATE TABLE movie_characters(
   movie_id UUID NOT NULL,
   character_id UUID NOT NULL,
   PRIMARY KEY(movie_id, character_id),
   FOREIGN KEY(movie_id) REFERENCES movie(movie_id),
   FOREIGN KEY(character_id) REFERENCES character(character_id)
);
```

## Table character_bookmark

```SQL
CREATE TABLE character_bookmark(
   cinephile_id UUID NOT NULL,
   character_id UUID NOT NULL,
   PRIMARY KEY(cinephile_id, character_id),
   FOREIGN KEY(cinephile_id) REFERENCES cinephile(cinephile_id),
   FOREIGN KEY(character_id) REFERENCES character(character_id)
);
```

# Dictionnaire de données

| Table                  | Colonne             | Description                   | Type      | Contrainte      | Exemple                              |
| ---------------------- | ------------------- | ----------------------------- | --------- | --------------- | ------------------------------------ |
| **movie**              | movie_id            | Numéro du film                | UUID      | NOT NULL UNIQUE | b6276aa3-02f1-4cb0-a33c-9e61f14a369d |
|                        | movie_title         | Nom du film                   | VARCHAR   | NOT NULL        | King Kong                            |
|                        | movie_length        | Durée du film                 | INT       | NOT NULL        | 01:30:00                             |
|                        | movie_release_date  | Date de sortie du film        | DATE      | NOT NULL        | 2015-11-03                           |
| **actor**              | actor_id            | Numéro de l'acteur            | UUID      | NOT NULL UNIQUE | ca5aac21-c7ea-45ee-8209-5482ca95482c |
|                        | actor_firstname     | Prénom de l'acteur            | VARCHAR   | NOT NULL        | Jean                                 |
|                        | actor_lastname      | Nom de l'acteur               | VARCHAR   | NOT NULL        | Dujardin                             |
|                        | actor_birthdate     | Date de naissance de l'acteur | DATE      | NOT NULL        | 1974-06-15                           |
| **director**           | director_id         | Numéro du réalisateur         | UUID      | NOT NULL UNIQUE | 14a94469-f657-4d81-859e-0d56b34fba15 |
|                        | director_firstname  | Prénom du réalisateur         | VARCHAR   | NOT NULL        | Steven                               |
|                        | director_lastname   | Nom du réalisateur            | VARCHAR   | NOT NULL        | Spielsberg                           |
| **cinephile**          | cinephile_id        | Numéro du cinéphile           | UUID      | NOT NULL UNIQUE | 9db62d23-d3cf-43b2-b02e-e4715fbca28d |
|                        | cinephile_firstname | Prénom du cinéphile           | VARCHAR   | NOT NULL        | Titouan                              |
|                        | cinephile_lastname  | Nom du cinéphile              | VARCHAR   | NOT NULL        | Dupont                               |
|                        | cinephile_mail      | Mail du cinéphile             | VARCHAR   | NOT NULL UNIQUE | titouan59rpz@gmail.com               |
|                        | cinephile_password  | Mot de passe du cinéphile     | VARCHAR   | NOT NULL        | Titi59452.                           |
| **acting**             | movie_id            | Numéro du film                | UUID      | NOT NULL UNIQUE | b6276aa3-02f1-4cb0-a33c-9e61f14a369d |
|                        | character_id        | Numéro du personnage          | UUID      | NOT NULL UNIQUE | 431c0816-4f50-450c-9f3a-8eb095659dd5 |
| **character_bookmark** | cinephile_id        | Numéro du cinéphile           | UUID      | NOT NULL UNIQUE | 9db62d23-d3cf-43b2-b02e-e4715fbca28d |
|                        | character_id        | Numéro du personnage          | UUID      | NOT NULL UNIQUE | 431c0816-4f50-450c-9f3a-8eb095659dd5 |
| **movie_bookmark**     | cinephile_id        | Numéro du cinéphile           | UUID      | NOT NULL UNIQUE | 9db62d23-d3cf-43b2-b02e-e4715fbca28d |
|                        | movie_id            | Numéro du film                | UUID      | NOT NULL UNIQUE | b6276aa3-02f1-4cb0-a33c-9e61f14a369d |
| **movie_characters**   | character_id        | Numéro du personnage          | UUID      | NOT NULL UNIQUE | 431c0816-4f50-450c-9f3a-8eb095659dd5 |
|                        | movie_id            | Numéro du film                | UUID      | NOT NULL UNIQUE | b6276aa3-02f1-4cb0-a33c-9e61f14a369d |
| **archive**            | archive_id          | Numéro de l'archive           | UUID      | NOT NULL UNIQUE | 431c0816-4f50-450c-9f3a-8eb095659dd5 |
|                        | archive_date        | Date de modification          | TIMESTAMP | NOT NULL        | 2006-15-06 17:50:12                  |
|                        | archive_oldvalue    | Ancienne valeur               | VARCHAR   | NOT NULL        | titouan59rpz@gmail.com               |
|                        | archive_newvalue    | Nouvelle valeur               | VARCHAR   | NOT NULL        | titouan.dupont@gmail.com             |
| **character**          | character_id        | Numéro du personnage          | UUID      | NOT NULL UNIQUE | 431c0816-4f50-450c-9f3a-8eb095659dd5 |
|                        | character_name      | Nom du personnage             | VARCHAR   | NOT NULL        | Jack Sparrow                         |
|                        | character_type      | Type du personnage            | VARCHAR   | NOT NULL        | Personnage principal                 |

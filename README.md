# 🎬 NetStream-Poulpe

## 📑 Sommaire

- [📋 Règle de gestion](./Regles-de-gestion.md)
- [📚 Dictionnaire de données](./dictionnaire-de-donnees.md)
- [🔍 Photo MCD](./Photo-MCD-MLD-MPD/mcd.png)
- [🔍 Photo MLD](./Photo-MCD-MLD-MPD/mld.png)
- [🔍 Photo MPD](./Photo-MCD-MLD-MPD/mpd.png)
- [💾 Choix du SGBD](./choix-du-SGBDR.md)
- [📜 Script SQL](./script-sql.md)
- [🛠️ Crud SQL Avancée](./sql-avancee.md)
- [📖 Documentation](./Documentation.md)
- [💾 Documentation rétention de sauvegarde](./Documentation-de-la-politique-de-retention.md)
- [📝 Contexte](#📝-contexte-du-projet)
- [⚡ Requêtes SQL](#⚡-Requetes-SQL)
- [## 👥 Contributeurs](#-contributeurs)

## 📝 Contexte du projet

Nous sommes une équipe de trois développeurs passionnés de cinéma, curieux des coulisses et fascinés par la diversité des œuvres accessibles grâce aux plateformes de streaming.

Sur notre temps libre, nous avons décidé de créer notre propre plateforme. Mais avant de construire un site web complet, nous commençons par sa conception et sa mise en place de la base de données.

Nous avons par conséquent:

- 📊 Recenser les données nécessaires dans un dictionnaire de données.
- 🔄 Concevoir la base avec la méthode MERISE : MCD, MLD, MPD.
- 💻 Écrire les requêtes SQL pour interagir avec nos données.
- ⚙️ Mettre en place des procédures stockées et déclencheurs pour automatiser les actions courantes.

Ce projet est pour nous une première étape vers la création d'une vraie plateforme de découverte cinématographique.

## ⚡ Requetes SQL

### Sommaire

- [ 🎥 Titre et date de sorite de films du plus récent au plus ancien](#-les-titres-et-dates-de-sortie-des-films-du-plus-récent-au-plus-ancien)
- [👨‍🎤 Les noms, prénoms et âges des acteurs/actrices de plus de 30 ans dans l'ordre alphabétique](#-les-noms-prénoms-et-âges-des-acteursactrices-de-plus-de-30-ans-dans-lordre-alphabétique)
- [🌟 La liste des acteurs/actrices principaux pour un film donné](#-la-liste-des-acteursactrices-principaux-pour-un-film-donné)
- [🎭 La liste des films pour un acteur/actrice donné](#-la-liste-des-films-pour-un-acteuractrice-donné)
- [➕ Ajouter un film](#-ajouter-un-film)
- [➕ Ajouter un acteur/actrice](#-ajouter-un-acteuractrice)
- [🔄 Modifier un film](#-modifier-un-film)
- [🗑️ Supprimer un acteur/actrice](#️-supprimer-un-acteuractrice)
- [🕒 Afficher les 3 derniers acteurs/actrices ajouté(e)s](#-afficher-les-3-derniers-acteursactrices-ajoutées)

### 🎥 Les titres et dates de sortie des films du plus récent au plus ancien

```sql
SELECT movie_title, movie_release_date
FROM movie
ORDER BY movie_release_date DESC;
```

### 👨‍🎤 Les noms, prénoms et âges des acteurs/actrices de plus de 30 ans dans l'ordre alphabétique

```sql
SELECT actor_lastname, actor_firstname, DATE_PART('year', AGE(actor_birthdate)) AS age
FROM actor
WHERE DATE_PART('year', AGE(actor_birthdate)) > 30
ORDER BY actor_lastname;
```

### 🌟 La liste des acteurs/actrices principaux pour un film donné

```sql
SELECT actor_firstname, actor_lastname, c.character_type, m.movie_title FROM actor
JOIN acting act ON act.actor_id = actor.actor_id
JOIN character c ON c.character_id = act.character_id
JOIN movie_characters mv ON mv.character_id = c.character_id
JOIN movie m ON m.movie_id = mv.movie_id
WHERE character_type = 'Personnage principal'
AND m.movie_title = 'The Great Escape';
```

### 🎭 La liste des films pour un acteur/actrice donné

```sql
SELECT actor_firstname, actor_lastname, m.movie_title FROM actor
JOIN acting act ON act.actor_id = actor.actor_id
JOIN character c ON c.character_id = act.character_id
JOIN movie_characters mv ON mv.character_id = c.character_id
JOIN movie m ON m.movie_id = mv.movie_id;
```

### ➕ Ajouter un film

```sql
INSERT INTO movie (movie_id, movie_title, movie_release_date, movie_length, director_id)
VALUES (
gen_random_uuid(),
'Astérix et Cléopatre',
'1968-02-16',
'01:20:00',
'8b9d9b60-2cc0-4e61-87a7-5ef5a0e9e7c9'
);
```

### ➕ Ajouter un acteur/actrice

```sql
INSERT INTO actor (actor_id, actor_firstname, actor_lastname, actor_birthdate)
VALUES (
gen_random_uuid(),
'Will',
'Smith',
'1968-09-25'
);
```

### 🔄 Modifier un film

```sql
UPDATE movie
SET movie_title = 'Je suis une légende',
    movie_release_date = '2007-12-19',
    movie_length = '01:40:00',
WHERE movie_id = '1a2b3c4d-5e6f-7a8b-9c0d-1e2f3a4b5c6d';
```

### 🗑️ Supprimer un acteur/actrice

```sql
DELETE FROM actor
WHERE actor_id = '8b5b3470-264c-46d5-82f3-3e840b34a6b9';
```

ou

```sql
DELETE FROM acting
WHERE actor_id = 'c3d4e5f6-a7b8-9c0d-1e2f-3a4b5c6d7e8f';

DELETE FROM actor
WHERE actor_id = 'c3d4e5f6-a7b8-9c0d-1e2f-3a4b5c6d7e8f';
```

### 🕒 Afficher les 3 derniers acteurs/actrices ajouté(e)s

```sql
SELECT * FROM actor ORDER BY created_at DESC LIMIT 3;
```

## 👥 Contributeurs

Ce projet a été réalisé par :

- VMOHAMMADI Zabiullah (@M-ZABIULLAH)
- HOUAIRI Axel (@axelhri)
- DUFOUR Jody (@joydfr)

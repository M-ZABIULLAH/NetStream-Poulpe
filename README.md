# 🎬 NetStream-Poulpe

## 📑 Sommaire

- [📋 Règle de gestion](./management-rule.md)
- [📚 Dictionnaire de données](./data-dictionary.md)
- [🔍 Photo MCD](./Picture-MCD-MLD-MPD/mcd.png)
- [🔍 Photo MLD](./Picture-MCD-MLD-MPD/mld.png)
- [🔍 Photo MPD](./Picture-MCD-MLD-MPD/mpd.png)
- [💾 Choix du SGBD](./choice-of-SGBD.md)
- [📜 Script SQL](./script-sql.md)
- [🛠️ Crud SQL Avancée](./sql-avancee.md)
- [📖 Documentation](./Documentation.md)
- [💾 Documentation rétention de sauvegarde](./Documenting-the-backup-rentention-policy.md)
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
AND m.movie_title = 'King Kong';
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
VALUES (gen_random_uuid(), 'Sonic', '2015-01-10', '01:00:00', '770561c0-81e7-4140-bf51-7588f9a8ceaa');
```

### ➕ Ajouter un acteur/actrice

```sql
INSERT INTO actor (actor_id, actor_firstname, actor_lastname, actor_birthdate)
VALUES (gen_random_uuid(), 'Scarlett', 'Johansson', '1984-11-22');
```

### 🔄 Modifier un film

```sql
UPDATE movie
SET movie_title = 'Sonic',
    movie_release_date = '2015-01-10',
    movie_length = '01:20:00',
    director_id = '770561c0-81e7-4140-bf51-7588f9a8ceaa'
WHERE movie_id = '702a0dd6-12b5-4ea7-adc1-fab458f7f6b8';
```

### 🗑️ Supprimer un acteur/actrice

```sql
DELETE FROM actor
WHERE actor_id = '8b5b3470-264c-46d5-82f3-3e840b34a6b9';
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

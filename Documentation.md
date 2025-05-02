# Installation et Configuration de la Base de Données

## 1. Introduction

Cette documentation décrit toutes les étapes nécessaires pour installer, configurer et préparer la base de données utilisée pour le brief NetStream (Plateforme de streaming).  
La base de données choisie est **PostgreSQL**, un SGBDR open-source et sécurisé.

---

## 2. Prérequis

- Système d'exploitation compatible (Windows, Linux, MacOS).
- Droits d’administrateur pour installer des logiciels.
- Accès à Internet.
- Un outil de gestion comme **pgAdmin**, **pgcli** ou **psql**.

---

## 3. Installation de PostgreSQL

### 3.1. Téléchargement

- Site officiel : [https://www.postgresql.org/download/](https://www.postgresql.org/download/)

### 3.2. Procédure

- Lancez l’installeur.
- Définissez un mot de passe pour `postgres`.
- Laissez le port par défaut (5432).
- Terminez l'installation.

---

## 4. Configuration initiale

### 4.1. Connexion

```bash
pgcli -h 10.2.0.76 -U User -d netstream
```

### 4.2. Création de la base et de l'utilisateur

```sql
CREATE DATABASE Netstream;
CREATE USER administrator WITH PASSWORD 'admin';
GRANT ALL PRIVILEGES ON DATABASE NetStream TO administrator;
```

---

## 5. Extension nécessaire

```sql
\c cinephile_db
CREATE EXTENSION IF NOT EXISTS "pgcrypto";
```

---

## 6. Création des Tables

### 6.1. Table `cinephile`

```sql
CREATE TABLE cinephile(
   cinephile_id UUID PRIMARY KEY,
   cinephile_firstname VARCHAR(50) NOT NULL,
   cinephile_lastname VARCHAR(50) NOT NULL,
   cinephile_mail VARCHAR(128) NOT NULL UNIQUE,
   cinephile_password VARCHAR(64) NOT NULL,
   created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
   updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

### 6.2. Table `archive`

```sql

CREATE TABLE archive(
   archive_id UUID PRIMARY KEY,
   archive_newvalue VARCHAR(50) NOT NULL,
   archive_oldvalue VARCHAR(50) NOT NULL,
   archive_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
   cinephile_id UUID NOT NULL,
   FOREIGN KEY(cinephile_id) REFERENCES cinephile(cinephile_id)
);
```

---

## 7. Fonction et Trigger

### 7.1. Fonction de trigger

```sql

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

### 7.2. Trigger

```sql

CREATE TRIGGER cinephile_trigger
AFTER UPDATE ON cinephile
FOR EACH ROW
EXECUTE FUNCTION cinephile_logs();
```

---

## 8. Sauvegarde et Restauration

### 8.1. Exportation

```bash
pg_dump -U administrator -d netstream -f fichier.sql
```

### 8.2. Restauration

- Restauration de l'exportation

```bash
psql -U administrator -d netstream -f fichier.sql
```

- Avec .backup requiert pg_restore pour être restauré

```bash
pg_restore -U administrator -d netstream /chemin/vers/le_fichier.backup
```

### 8.3. Sauvegarde automatisé

Script dans le fichier **backup.sh** :

```bash
#!/bin/bash

# Répertoire
BACKUP_DIR="$HOME/Desktop/netstream-dump"

# Variables
DB_NAME="netstream"
DATE=$(date +"%Y-%m-%d_%H-%M-%S")
FILENAME="${DB_NAME}_backup_${DATE}.backup"

# Mot de passe de l'utilisateur psql
export PGPASSWORD="admin"

# Sauvegarde
pg_dump -U administrator -d "$DB_NAME" -F c -f "$BACKUP_DIR/$FILENAME"

# Nettoyage des sauvegardes de plus de 15 jours
find "$BACKUP_DIR" -type f -name "*.backup" -mtime +15 -delete
```

Fichier contenant le mot de passe psql de l'utilisateur administrator :

```bash
localhost:5432:netstream:administrator:admin
```

**cron** :

```bash
00 00 * * * /bin/bash $HOME/Desktop/netstream-dump/backup.sh
```

---

## 9. Conclusion

Toutes les modifications importantes des utilisateurs seront archivées automatiquement pour garantir la traçabilité.

---

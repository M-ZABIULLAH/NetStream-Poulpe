

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
CREATE DATABASE NetStream_db;
CREATE USER NetStream_user WITH PASSWORD 'motdepass';
GRANT ALL PRIVILEGES ON DATABASE NetStream_db TO NetStream_user;
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
   cinephile_firstname VARCHAR(50),
   cinephile_lastname VARCHAR(50),
   cinephile_mail VARCHAR(128),
   cinephile_password VARCHAR(64),
   created_at DEFAULT CURRENT_TIMESTAMP,
   updated_at DEFAULT CURRENT_TIMESTAMP
);
```

### 6.2. Table `archive`
```sql
CREATE TABLE archive(
   archive_id UUID PRIMARY KEY,
   archive_newvalue VARCHAR(50),
   archive_oldvalue VARCHAR(50),
   archive_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
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
pg_dump -h 10.2.0.76 -User -d netstream -F c -f base-de-donnee-netstream.backup
```

### 8.2. Restauration
```bash
psql -pg_restore -d ma_base sauvegarde.dump
```

---


## 9. Conclusion


Toutes les modifications importantes des utilisateurs seront archivées automatiquement pour garantir la traçabilité.

---

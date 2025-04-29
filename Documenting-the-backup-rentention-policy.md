# Documentation de rétention de sauvegarde

## 🎯 1 Objectif

Tout plan de sauvegarde solide repose sur une **politique de rétention des sauvegardes**, qui définit la durée pendant laquelle les données de sauvegarde doivent être **conservées avant d’être archivées, écrasées ou supprimées**.

Cette politique est déterminée selon les critères suivants :

- Quels types de données sont sauvegardés
- À quelle fréquence les sauvegardes sont effectuées
- Combien de temps chaque sauvegarde est conservée
- Comment et où les sauvegardes sont stockées
- Qui a accès aux sauvegardes
- Comment les données sont restaurées en cas d’incident

L’objectif est de garantir la **disponibilité**, **l’intégrité** et la **restauration rapide des données**, tout en respectant les **contraintes de sécurité**, de **confidentialité** et de **conformité réglementaire**.

## 2 Périmètre

- Application concernée : Netstream
- Base de données : PostgresSQL

## 3 Responsabilités

- Personne responsable de la sauvegarde : Jody
- Personne ayant les privilèges d'accès : Axel
- Contact en cas d'incident : Axel

## 4 Stratégie de sauvergarde

- Type de sauvegarde : Complète
- Fréquence : Une sauvegarde complète chaque jour à minuit
- Méthode : Script Pg_dump , Automatisation via cron , utilisation d'un trigger entre cinephile et archives (journalisation de modification des données cinéphiles)

## 5 Rétention

Durée de conservation :

- Sauvegarde quotidiennes : 15 jours.

Rotation :

- Méthode de suppression automatique (script, cron)

## 6 Sécurité

- Accès restreint: Seul l'administrateur possède les droit d'accès aux fichiers de sauvegarde
- Chiffrement des sauvegardes : non
- Droits sur la base PostgreSQL : utilisateur restreint aux opérations de lecture

## 7 Test de restauration

- Fréquence des tests de restauration : 1 fois par mois
- Procédure
- 📁 Chemin d'accès au sauvegarde
  - Sauvegarde journalière : $HOME/backups
- 🛠️ Prodédure de restauration PostgreSQL
  - 1 Choisir une sauvegarde pour test
  ```bash
  cd $HOME/backups
  ls
  ```
  ```bash
  netstream_backup_2025-04-28_14-17-06.dump
  ```
  - 2 Créer une base temporaire
  ```bash
  createdb netstream_test
  ```
  - 3 Restaurer dans cette base
  ```bash
  pg_restore -d netstream_test /tmp/netstream_restored.dump
  ```
  - 4 Vérification manuelle (optionnelle mais vivement conseillé)
  ```bash
  psql netsream_test
  ```
  ```sql
  \dt
  SELECT COUNT(*) FROM acting;
  ```
  - 5 si tout est OK nettoyage
  ```bash
  dropdb netstream_test
  rm/tmp/netstream_restored.dump
  ```

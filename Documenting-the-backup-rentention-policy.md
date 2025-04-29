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

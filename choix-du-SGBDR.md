# Choix du SGBDR : Pourquoi PostgreSQL ?

## Objectif du document

Ce document présente les raisons du choix de PostgreSQL comme Système de Gestion de Base de Données Relationnelle (SGBDR) pour le projet **NetStream**.

## 1. Qu’est-ce que PostgreSQL ?

PostgreSQL est un SGBDR open source, puissant, évolutif et conforme aux standards SQL. Il est développé activement depuis plus de 30 ans, avec une communauté très dynamique.

---

## 2. Raisons du choix

### Open source et gratuit

PostgreSQL est totalement libre et ne nécessite aucune licence. Cela le rend parfait pour les projets éducatifs, les PME ou les solutions à grande échelle.

### Compatibilité avec SQL standard

PostgreSQL respecte la norme SQL (SQL:2008) et offre des fonctionnalités avancées comme :

- Les types personnalisés
- Les fonctions stockées
- Les déclencheurs (triggers)
- Les contraintes CHECK
- La gestion fine des permissions

### Excellente gestion de la concurrence (MVCC)

Grâce à son système MVCC (Multiversion Concurrency Control), PostgreSQL gère très bien plusieurs accès simultanés à la base de données sans blocages majeurs.

### Sécurité et contrôle d’accès

PostgreSQL nous offre un contrôle d'accès détaillé à travers les rôles, mots de passe, et fichiers de configuration (`pg_hba.conf`). Cela répond parfaitement aux exigences de notre projet, où seuls certains utilisateurs peuvent insérer ou modifier des données.

### Bonne intégration avec les outils modernes

PostgreSQL est compatible avec :

- pgAdmin (interface graphique)
- DBeaver, DataGrip
- Langages comme Python, C#, Java, etc.
- ORM comme Sequelize, TypeORM, SQLAlchemy, etc.

---

## 🆚 3. Comparaison avec d'autres SGBDR

| Critère              | PostgreSQL      | MySQL              | SQLite          |
| -------------------- | --------------- | ------------------ | --------------- |
| Open Source          | ✅ Oui          | ✅ Oui             | ✅ Oui          |
| Standard SQL         | ✅ Complet      | ⚠️ Partiel         | ⚠️ Limité       |
| Concurrence          | ✅ Excellente   | ⚠️ Moins optimisée | ❌ Très limitée |
| Triggers & fonctions | ✅ Avancé       | ⚠️ Moins puissant  | ⚠️ Basique      |
| Sécurité             | ✅ Contrôle fin | ⚠️ Moins précis    | ❌ Très basique |
| Extensibilité        | ✅ Très élevée  | ⚠️ Moyenne         | ❌ Faible       |

### 🧩 4. Dans le contexte de notre projet

Dans le cadre de ce projet d'application pour un futur site de streaming et de recherche de films, nous avons fait le choix de **PostgreSQL** comme système de gestion de base de données relationnelle (SGBDR). Ce choix repose sur plusieurs critères techniques et contextuels, en lien direct avec la structure et les besoins de notre modèle.

- Dans un premier temps, **PostgreSQL nous offre un respect strict du modèle relationnel**, ce qui nous garantit l'intégrité des données grâce à sa gestion des clés primaires, étrangères et des contraintes. Nos schémas MPD, MLD et MPT comprennent plusieurs relations complexes (1,n et n,n), ainsi que des entités, dont l’une avec un suivi d’historique (`archive`), ce qui nécessite une base fiable et cohérente dans le temps.

- **Un système de triggers** nous a été demandé par le client pour l'entité `cinéphile`. PostgreSQL propose un système de triggers puissant et flexible, idéal pour automatiser la traçabilité des modifications, comme exigé par notre client. Grâce à PostgreSQL, nous pouvons également utiliser **des fonctions personnalisées**, comme celle qui nous permet, lors de la création d’un acteur ou d’une actrice, de lui attribuer un rôle directement rattaché à un film, ce qui facilite l’ajout d’informations dans notre base de données.

- Un autre avantage qui a motivé notre choix est **la richesse des fonctions natives**, notamment pour la manipulation des dates. Lors de la conception, nous avons été confrontés à deux possibilités : saisir la date de naissance des acteurs ou stocker directement leur âge. La date de naissance permet une meilleure mise à jour et évite des recalculs manuels. Avec PostgreSQL, le calcul de l’âge à partir de la date de naissance est directement possible grâce à des fonctions comme `AGE()` et `DATE_PART()`, sans nécessiter de logique additionnelle.

**PostgreSQL répond donc pleinement aux exigences fonctionnelles, techniques et évolutives** de notre projet, tout en s’adaptant à l'ajout futur de fonctionnalités telles que le streaming de films.

## 🏁 Conclusion

PostgreSQL est le choix idéal pour notre projet NetStream grâce à sa robustesse, sa richesse fonctionnelle, sa compatibilité SQL, et sa capacité à gérer la montée en charge et la sécurité. Il s’impose comme une solution moderne, performante et adaptée à un projet collaboratif et éducatif.

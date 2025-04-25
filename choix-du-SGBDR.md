## Pourquoi PostgreSQL pour notre SGBD

Dans le cadre de ce projet d'application pour un futur site de streaming et de recherche de films, nous avons fait le choix de **PostgreSQL** comme système de gestion de base de données relationnelle (SGBDR). Ce choix repose sur plusieurs critères techniques et contextuels, en lien direct avec la structure et les besoins de notre modèle.

- Dans un premier temps, **PostgreSQL nous offre un respect strict du modèle relationnel**, ce qui nous garantit l'intégrité des données grâce à sa gestion des clés primaires, étrangères et des contraintes. Nos schémas MPD, MLD et MPT comprennent plusieurs relations complexes (1,n et n,n), ainsi que des entités, dont l’une avec un suivi d’historique (`archive`), ce qui nécessite une base fiable et cohérente dans le temps.

- **Un système de triggers** nous a été demandé par le client pour l'entité `cinéphile`. PostgreSQL propose un système de triggers puissant et flexible, idéal pour automatiser la traçabilité des modifications, comme exigé par notre client. Grâce à PostgreSQL, nous pouvons également utiliser **des fonctions personnalisées**, comme celle qui nous permet, lors de la création d’un acteur ou d’une actrice, de lui attribuer un rôle directement rattaché à un film, ce qui facilite l’ajout d’informations dans notre base de données.

- Un autre avantage qui a motivé notre choix est **la richesse des fonctions natives**, notamment pour la manipulation des dates. Lors de la conception, nous avons été confrontés à deux possibilités : saisir la date de naissance des acteurs ou stocker directement leur âge. La date de naissance permet une meilleure mise à jour et évite des recalculs manuels. Avec PostgreSQL, le calcul de l’âge à partir de la date de naissance est directement possible grâce à des fonctions comme `AGE()` et `DATE_PART()`, sans nécessiter de logique additionnelle.

**PostgreSQL répond donc pleinement aux exigences fonctionnelles, techniques et évolutives** de notre projet, tout en s’adaptant à l'ajout futur de fonctionnalités telles que le streaming de films.

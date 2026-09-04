# Notes d'orateur, point d'étape de 6 minutes

Une slide par minute. Si je dépasse à la fin de la slide 3, je saute la slide 5 et je raccourcis la 4.

## 1. Le projet, 45 s

Leo Training est un hub pour les entraînements de laser-tag compétitif. La ligue LEO, environ mille joueurs, et les centres Megazone qui les accueillent. Aujourd'hui tout s'organise sur des Discord dispersés, un par centre : aucune vue d'ensemble, les centres subissent leur remplissage. Un seul objet dans le produit, le créneau : le centre le publie, le joueur s'inscrit en un clic, les places se mettent à jour. Je lis la phrase JTBD.

Une phrase de benchmark : personne ne couvre l'entraînement, ni le site de la ligue qui ne fait que les tournois, ni SportEasy qui gère une équipe, ni Playtomic qui vend des terrains.

## 2. Architecture, 1 min 15

Multicouche classique, pas hexagonal, trop lourd pour une V1. Front et back découplés, SPA React et API REST Express, JSON sur HTTPS. TypeScript des deux côtés, Prisma et PostgreSQL. Sécurité : Zod valide chaque entrée dans les contrôleurs, Helmet pour les en-têtes, rate limit sur la connexion, JWT court plus refresh token en cookie HttpOnly, bcrypt pour les mots de passe. Sobriété : pagination par défaut, payloads limités, pas de polling. Le diagramme montre les noeuds, dont Brevo pour les emails et Cloudinary pour les images.

## 3. Données, 1 min 15

Pour la base de données j'ai suivi la méthode Merise, en trois niveaux.

À gauche le MCD, le modèle conceptuel. C'est la vue métier, indépendante de toute technologie. On y retrouve les objets du projet, l'utilisateur, le centre, l'équipe, l'entraînement et le tournoi en vitrine, avec les liens entre eux et leurs cardinalités : un centre accueille plusieurs entraînements, un joueur s'inscrit à plusieurs entraînements, une équipe représente un centre. Les règles qui ne se dessinent pas sont notées à côté sous forme de contraintes d'intégrité.

Entre les deux, le MLD traduit ces entités en tables relationnelles avec les clés étrangères. Je ne l'affiche pas ici, il est dans le dépôt.

À droite le MPD, le modèle physique. C'est la traduction concrète pour PostgreSQL : chaque table avec ses colonnes typées, ses clés primaires et étrangères, ses contraintes. J'ai fait quelques choix à ce passage, des identifiants UUID plutôt que des clés naturelles, et des types énumérés pour tous les statuts. Le script SQL de création complet est dans le dépôt.

Une règle à retenir si on ne devait en garder qu'une : le nombre de places restantes ne se stocke pas, il se calcule à partir des inscriptions confirmées, ce qui évite d'avoir deux vérités en base.

## 4. Charte graphique, 45 s

Deux univers. La ligue est en bleu et blanc, son site ne gère que les tournois. Leo Training prend l'orange : séparation nette, et complémentarité, l'orange et le bleu sont complémentaires. Le navy du logo garde le lien. Interface claire sur fond blanc, un seul accent chaud, l'orange n'apparaît que là où il compte. J'écarte les codes esports, fonds noirs et néons, parce que l'outil doit inspirer confiance au gérant de salle qui l'utilise en plein jour sur son comptoir.

## 5. Du zoning à l'écran, 1 min

J'ai commencé au crayon : zoning, puis wireframes desktop et mobile. Deux maquettes Figma, l'accueil et l'inscription, pour fixer la charte. Puis je suis passé sur Claude Design pour produire les douze écrans, et je veux expliquer comment. Je lui ai donné mes deux maquettes Figma comme référence de disposition. J'ai écrit DESIGN.md en amont, palette, tokens, typographie, règles RGAA, comme un contrat qu'il devait respecter. J'ai décrit le contexte métier et l'intention de chaque page, états compris. Ensuite j'ai audité le résultat contre mon modèle de données et corrigé ce qui ne collait pas : le compteur d'équipe qui confondait effectif et inscrits, le centre inactif qui accueillait le tournoi, le capitaine qui pouvait choisir n'importe quel centre. J'ai spécifié, l'outil a exécuté, j'ai audité.

## 6. Démo, 45 s

L'accueil en desktop et en mobile. La vitrine du tournoi à gauche, les créneaux à droite avec la jauge de places. La bordure orange sur la troisième carte signale que ce centre accueille le tournoi en vitrine. En mobile, la vitrine devient un bandeau repliable, la liste passe en premier, la navigation descend en bas au niveau du pouce.

## 7. Ce qui reste, 30 s

Fait : PRD, specs Gherkin, architecture, MERISE avec DDL, charte, design system, douze écrans. Reste : la recherche JTBD avec trois sources réelles, l'export des diagrammes, le pitch de dix minutes et les retours des pairs. Deux questions pour vous : Claude Design à la place de Figma, est-ce recevable ? Et le périmètre en trois niveaux, est-ce la bonne lecture d'une seule fonctionnalité forte ?

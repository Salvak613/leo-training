# Notes d'orateur, point d'étape de 6 minutes

Dix slides, dans l'ordre du support Google Slides. Les temps sont indicatifs. Si je dépasse à la slide 5, je passe la 6 et la 8 en dix secondes chacune.

## 1. Titre, 10 s

Leo Training, un hub pour les entraînements de laser-tag compétitif. Je vous présente où j'en suis sur la phase de conception.

## 2. Qu'est-ce que LEO, 30 s

LEO, Laser Event Organization, c'est la ligue officielle du laser-tag compétitif en France. Elle organise les tournois, dont le championnat de France, avec le réseau des centres Megazone. Environ mille joueurs actifs.

## 3. Problématique, 45 s

La ligue organise les tournois, mais personne n'organise les entraînements. Chaque centre a son serveur Discord, l'information est éparpillée, on ne sait pas où et quand s'entraîner, et les centres subissent leur remplissage. Le besoin, c'est celui d'un joueur qui veut voir tous les créneaux au même endroit et réserver sa place en une minute. L'objectif de Leo Training tient en un mot : le créneau. Le centre le publie, le joueur s'inscrit, les places se mettent à jour.

## 4. Architecture, 1 min

J'ai choisi une architecture multicouche classique, routes, services, accès aux données, base. C'est simple à tester et suffisant pour une V1. Le front et le back sont découplés : une application React d'un côté, une API REST Node et Express de l'autre, qui parlent en JSON. TypeScript des deux côtés, avec Prisma et PostgreSQL.

Côté sécurité, Zod valide toutes les entrées, Helmet protège les en-têtes, un rate limit protège la connexion, l'authentification repose sur un JWT court et un refresh token en cookie HttpOnly, et les mots de passe sont hachés avec bcrypt. Côté sobriété, pagination par défaut et pas de polling. Le diagramme montre les briques : le client, l'API hébergée sur Render, la base sur AlwaysData, et deux services tiers, Brevo pour les emails et Cloudinary pour les images.

## 5. MCD et MPD, 1 min

J'ai suivi la méthode Merise. À gauche le modèle conceptuel, la vue métier : les objets du projet, utilisateur, centre, équipe, entraînement, tournoi, et les liens entre eux avec leurs cardinalités. À droite le modèle physique, la traduction pour PostgreSQL : seize tables avec leurs colonnes typées, leurs clés et leurs contraintes. Le script SQL complet est dans le dépôt. Un choix à retenir : le nombre de places restantes ne se stocke pas, il se calcule à partir des inscriptions confirmées, pour ne jamais avoir deux vérités en base.

## 6. Zoning et wireframes, 30 s

J'ai commencé au crayon, en zonant l'accueil en desktop et en mobile. Puis j'ai détaillé le desktop en wireframe, parce que c'est l'écran principal du gérant de salle. Le mobile, je l'ai décliné plus tard, au moment des maquettes finales.

## 7. Charte graphique, 45 s

Deux univers. La ligue communique en bleu et blanc, et son site ne gère que les tournois. Leo Training prend l'orange : ça sépare clairement les deux outils, et les deux couleurs sont complémentaires. Le navy du logo garde le lien. Une interface claire sur fond blanc, un seul accent chaud, l'orange seulement là où il compte, la jauge de places et le bouton principal. J'ai écarté les codes esports, fonds noirs et néons, parce que l'outil doit inspirer confiance à un gérant de salle qui l'utilise en plein jour sur son comptoir.

## 8. Maquette Figma, 20 s

Deux écrans maquettés dans Figma, l'accueil et l'inscription. C'est là que la charte s'est fixée : les cartes, la jauge, le placement de la vitrine tournoi.

## 9. Passage sur Claude Design, 1 min

Pour produire les douze écrans, en desktop et en mobile, je suis passé sur Claude Design, et je veux dire comment. Je lui ai donné mes deux maquettes Figma comme référence de disposition. J'ai écrit ma charte dans un fichier DESIGN.md, palette, typographie, règles d'accessibilité, comme un contrat à respecter. J'ai décrit page par page ce que je voulais, avec les états. Ensuite j'ai relu le résultat contre mon modèle de données et j'ai corrigé ce qui ne collait pas, par exemple un compteur qui confondait l'effectif d'une équipe et ses inscrits. J'ai spécifié, l'outil a exécuté, j'ai vérifié.

En mobile, la vitrine devient un bandeau repliable, la liste passe en premier et la navigation descend en bas, au niveau du pouce.

## 10. Où j'en suis, 30 s

Fait : le PRD, les spécifications, l'architecture, le modèle de données, la charte et les écrans. Reste : la recherche JTBD avec trois sources, l'export du diagramme de cas d'utilisation, le pitch de dix minutes et les retours des pairs.

Deux questions pour vous. Le prototype est fait avec Claude Design à partir de mes maquettes Figma, est-ce recevable au regard de la grille ? Et j'ai découpé le périmètre en trois niveaux, la fonctionnalité principale, le socle indispensable et un lot V1.1 : est-ce la bonne lecture de « une seule fonctionnalité forte » ?

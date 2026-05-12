# Architecture — Leo Training

---

## 1. Patron de conception

Pour le backend de Leo Training, le choix s'est porté sur une **Architecture Multicouche classique** (parfois appelée architecture N-Tiers ou architecture en couches).

**Pourquoi ce choix ?** Contrairement à une architecture Hexagonale (souvent trop lourde à mettre en place pour une V1) ou à un MVC basique (qui a tendance à surcharger les contrôleurs), l'approche multicouche offre le meilleur compromis entre rapidité de développement (approche Lean) et maintenabilité. Elle garantit une séparation claire des responsabilités _(Separation of Concerns)_, ce qui permet de tester et faire évoluer la logique métier indépendamment de la base de données ou des requêtes HTTP.

---

## 2. Le rôle de chaque couche côté backend

L'application côté serveur est strictement divisée en **4 couches successives** :

```
  Requête HTTP entrante
         │
         ▼
┌─────────────────────────────┐
│  Présentation               │  Routes & Controllers
│  (Express Router)           │
└────────────┬────────────────┘
             │
             ▼
┌─────────────────────────────┐
│  Métier                     │  Services
│  (Règles Leo Training)      │
└────────────┬────────────────┘
             │
             ▼
┌─────────────────────────────┐
│  Accès aux données          │  Repositories / Prisma ORM
│                             │
└────────────┬────────────────┘
             │
             ▼
┌─────────────────────────────┐
│  Données                    │  PostgreSQL
│                             │
└─────────────────────────────┘
```

| Couche                                       | Responsabilité                                                                                                                                                                                                                                                 |
| -------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Présentation** _(Routes & Controllers)_    | Point d'entrée. Le routeur Express intercepte la requête HTTP. Le contrôleur extrait les données (body, paramètres, headers), valide leur format, appelle le service correspondant, puis renvoie la réponse HTTP finale (200, 400, 404…) avec le payload JSON. |
| **Métier** _(Services)_                      | Cœur de l'application. Contient toutes les règles propres à Leo Training (ex : vérifier s'il reste de la place avant d'inscrire un joueur). Le service ne connaît ni le web (HTTP), ni le SQL.                                                                 |
| **Accès aux données** _(Repositories / ORM)_ | Communication avec la base de données. Reçoit les ordres du Service et utilise l'ORM pour exécuter les requêtes (lecture, écriture, suppression).                                                                                                              |
| **Données** _(BDD)_                          | Moteur de base de données physique. Stocke l'information de manière persistante et garantit l'intégrité référentielle (clés étrangères, contraintes d'unicité).                                                                                                |

---

## 3. Choix de sécurité concrets _(Bonnes pratiques ANSSI)_

- **Validation stricte des entrées** — Aucune donnée envoyée par le client n'est traitée aveuglément. Utilisation de la librairie **Zod** au niveau des contrôleurs pour valider le format, le type et la taille des payloads JSON avant qu'ils n'atteignent la logique métier.

- **Sécurisation des en-têtes HTTP** — Implémentation de **Helmet** pour configurer automatiquement les headers de sécurité de l'API (protection contre les failles XSS, clickjacking, et suppression de l'en-tête `X-Powered-By` pour ne pas exposer la stack technique).

- **Protection Brute-Force et DDoS** — Mise en place d'un middleware **express-rate-limit** pour limiter le nombre de requêtes autorisées par adresse IP sur un temps donné. Cela empêche les attaques par force brute sur les mots de passe et limite les risques d'engorgement du serveur.

- **Authentification et Autorisation** — L'authentification repose sur deux tokens complémentaires :
  - Un **access token JWT** à durée de vie courte (~15 min) contenant l'ID et le rôle de l'utilisateur.
  - Un **refresh token** longue durée (~7 jours) stocké dans un cookie `HttpOnly` (inaccessible au JavaScript).
  - Le renouvellement de l'access token est silencieux côté client.
  - Les permissions sont cloisonnées via des middlewares Express (ex : une route sera bloquée si le token ne contient pas le rôle `ORGANISATEUR`).

- **Stockage des secrets** — Les mots de passe sont systématiquement hachés et salés via **bcrypt** avant leur insertion en base. Les clés secrètes (JWT Secret, URL de la base de données) sont stockées dans des variables d'environnement (`.env`) et ne sont jamais versionnées sur Git.

---

## 4. Choix de sobriété concrets _(Éco-conception)_

- **Payloads limités** — L'API ne renvoie que le strict nécessaire. Lors de l'affichage de la liste des entraînements, l'API ne renverra pas toutes les données personnelles des joueurs inscrits, mais uniquement un identifiant et un compteur de places.

- **Pagination par défaut** — Pour éviter de surcharger la bande passante, le processeur et la mémoire (serveur comme client), les longues listes (ex : historique des entraînements) seront paginées côté serveur via l'ORM.

- **Pas de polling inutile** — Le frontend n'envoie pas de requêtes automatisées en boucle. Les données sont actualisées de manière mesurée : lors d'une action de l'utilisateur ou au rafraîchissement naturel de la page.

---

## 5. Stack technique

| Brique                 | Technologie       | Justification                                                                                                                                                                  |
| ---------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Langage**            | TypeScript        | Typage statique fort sur le front et le back. Détecte les erreurs dès l'écriture du code et garantit la cohérence des contrats de données (interfaces, types de réponses API). |
| **Framework Backend**  | Node.js + Express | Minimaliste, très bien documenté, parfait pour une API REST légère en cohérence avec l'approche Lean.                                                                          |
| **ORM**                | Prisma            | Génère un client de base de données 100% typé à partir du schéma. Protection native contre les injections SQL et gestion fluide des migrations.                                |
| **SGBD**               | PostgreSQL        | Base relationnelle robuste et open-source, adaptée aux relations fortes du modèle de données (Joueurs, Équipes, Créneaux, Inscriptions).                                       |
| **Framework Frontend** | React             | SPA totalement découplée du backend. Le typage TypeScript partagé entre front et back garantit la cohérence des contrats de données.                                           |
| **Emails**             | Nodemailer        | Librairie standard Node.js pour les e-mails transactionnels (confirmation de compte, mot de passe oublié) via un service SMTP externe (Brevo ou SendGrid).                     |
| **Backend hosting**    | Render            | PaaS géré : déploiement depuis GitHub, HTTPS automatique, scalabilité sans configuration serveur.                                                                              |
| **Base de données**    | AlwaysData        | Hébergement PostgreSQL managé. Gestion des sauvegardes et de la disponibilité déléguée à la plateforme.                                                                        |

---

## 6. Découplage Front / Back

L'architecture de Leo Training repose sur un **découplage strict** :

- Le **Frontend** (React) se comporte comme une SPA totalement autonome.
- Le **Backend** (Node/Express) est une API REST *headless* qui ne génère aucun fichier HTML.
- Les deux briques communiquent exclusivement via **HTTPS**, en s'échangeant des données au format **JSON**.
- Le HTTPS et le routage sont délégués aux plateformes d'hébergement (Render / AlwaysData) — aucun reverse proxy à gérer manuellement.

Ce découplage garantit que l'API pourra, à l'avenir, être consommée par d'autres clients (application mobile iOS/Android) sans modifier une seule ligne de code côté serveur.

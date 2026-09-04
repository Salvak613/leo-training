# MPD — Modèle Physique de Données (Leo Training)

> PostgreSQL 16 sur AlwaysData. Identifiants `UUID` (`gen_random_uuid()`), horodatages en
> `TIMESTAMPTZ`, `updated_at` géré par Prisma (`@updatedAt`). Nommage anglais (convention du
> code) ; la correspondance avec le MLD français est donnée en fin de document.

> Diagramme : `diagrams/mpd.png`, généré à partir des tables décrites ci-dessous.

## Décisions de passage MLD → MPD

| #   | Décision                                                                                | Justification                                                                                                    |
| --- | --------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| 1   | Clés de substitution UUID ; identifiants naturels du MCD en `UNIQUE`                    | Évite la propagation des clés naturelles en FK et les cascades de mise à jour ; UUID non énumérable dans les URL |
| 2   | `ROLE` devient l'ENUM `RoleType` (pas de table)                                         | Ensemble fermé, sans CRUD d'administration ; simplifie les jointures d'autorisation                              |
| 3   | `date/heure début/fin` d'entraînement fusionnées en `starts_at`/`ends_at` `TIMESTAMPTZ` | Comparaisons, tris et chevauchements triviaux ; l'atomicité du MCD n'impose pas la découpe physique              |
| 4   | Tables techniques hors MCD : `refresh_tokens`, `verification_tokens`                    | Infrastructure d'authentification, pas du domaine conceptuel                                                     |
| 5   | Statut `FULL` retiré de l'état d'un entraînement                                        | État déductible (inscriptions confirmées vs capacité) : le stocker crée une double vérité                        |
| 6   | Index explicites sur toutes les FK                                                      | PostgreSQL n'indexe pas les FK automatiquement                                                                   |

## ENUMs

| Type                 | Valeurs                                              | Origine MCD                                                                                                   |
| -------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `RoleType`           | `ADMIN` · `SUPER_ORGANIZER` · `ORGANIZER` · `PLAYER` | entité ROLE                                                                                                   |
| `CenterStatus`       | `ACTIVE` · `INACTIVE`                                | statut centre                                                                                                 |
| `UserStatus`         | `ACTIVE` · `SUSPENDED`                               | statut compte (V1.1, à reporter au MCD)                                                                       |
| `VisibilityType`     | `PUBLIC` · `PRIVATE`                                 | visibilite entrainement                                                                                       |
| `TrainingStatus`     | `DRAFT` · `SCHEDULED` · `CANCELED` · `COMPLETED`     | statut entrainement, `DRAFT` ajouté en V1.1                                                                                           |
| `AffiliationStatus`  | `PENDING` · `APPROVED` · `REJECTED`                  | statut affiliation                                                                                            |
| `MembershipStatus`   | `PENDING` · `ACTIVE` · `REJECTED`                    | statut adhesion (à ajouter au MCD)                                                                            |
| `TeamRole`           | `MEMBER` · `CAPTAIN`                                 | role équipe                                                                                                   |
| `RequestOrigin`      | `PLAYER` · `CAPTAIN`                                 | origine demande                                                                                               |
| `RegistrationStatus` | `CONFIRMED` · `CANCELED`                             | statut (S'inscrire) — inscription instantanée (UC04), rejet si complet (UC03) : ni attente ni liste d'attente |
| `TokenType`          | `EMAIL_VERIFY` · `PASSWORD_RESET`                    | — (technique)                                                                                                 |

## Référentiel géographique

### `countries` — MLD : PAYS

| Colonne    | Type         | Contraintes                          | Description                |
| ---------- | ------------ | ------------------------------------ | -------------------------- |
| `id`       | UUID         | PK                                   |                            |
| `iso_code` | CHAR(2)      | UNIQUE, NOT NULL, CHECK `^[A-Z]{2}$` | identifiant naturel du MCD |
| `name`     | VARCHAR(100) | NOT NULL                             |                            |

### `cities` — MLD : VILLE

| Colonne      | Type         | Contraintes                       | Description                |
| ------------ | ------------ | --------------------------------- | -------------------------- |
| `id`         | UUID         | PK                                |                            |
| `insee_code` | CHAR(5)      | UNIQUE, NOT NULL                  | identifiant naturel du MCD |
| `zip_code`   | CHAR(5)      | NOT NULL                          |                            |
| `name`       | VARCHAR(150) | NOT NULL                          |                            |
| `country_id` | UUID         | FK → countries, NOT NULL, indexée | Localiser                  |

## Centres

### `centers` — MLD : CENTRE

| Colonne                     | Type         | Contraintes                           | Description                                               |
| --------------------------- | ------------ | ------------------------------------- | --------------------------------------------------------- |
| `id`                        | UUID         | PK                                    |                                                           |
| `siret`                     | CHAR(14)     | UNIQUE, NOT NULL, CHECK `^[0-9]{14}$` | identifiant naturel du MCD                                |
| `name`                      | VARCHAR(150) | NOT NULL, UNIQUE avec `city_id`       | homonymes possibles entre villes, pas dans une même ville |
| `slug`                      | VARCHAR(160) | UNIQUE, NOT NULL                      | URL propre (ajout MPD)                                    |
| `address_number`            | VARCHAR(10)  |                                       |                                                           |
| `address_street`            | VARCHAR(200) | NOT NULL                              |                                                           |
| `latitude`                  | NUMERIC(9,6) | CHECK −90..90                         |                                                           |
| `longitude`                 | NUMERIC(9,6) | CHECK −180..180                       |                                                           |
| `phone`                     | VARCHAR(20)  |                                       |                                                           |
| `email`                     | VARCHAR(254) | NOT NULL                              |                                                           |
| `website`                   | VARCHAR(254) |                                       |                                                           |
| `opening_year`              | SMALLINT     | CHECK 1800..2100                      |                                                           |
| `status`                    | CenterStatus | NOT NULL, DEFAULT `ACTIVE`            |                                                           |
| `city_id`                   | UUID         | FK → cities, NOT NULL, indexée        | Situer — remplace `country`/`city`/`zip_code` en texte    |
| `created_at` / `updated_at` | TIMESTAMPTZ  |                                       |                                                           |

### `equipment` — MLD : EQUIPEMENT

| Colonne | Type         | Contraintes      | Description                         |
| ------- | ------------ | ---------------- | ----------------------------------- |
| `id`    | UUID         | PK               |                                     |
| `label` | VARCHAR(100) | UNIQUE, NOT NULL | remplace le champ texte `equipment` |

### `center_equipment` — MLD : DISPOSER

| Colonne        | Type | Contraintes                                  |
| -------------- | ---- | -------------------------------------------- |
| `center_id`    | UUID | PK composite, FK → centers ON DELETE CASCADE |
| `equipment_id` | UUID | PK composite, FK → equipment, indexée        |

## Tournoi vitrine

### `tournaments` — MLD : TOURNOI_VITRINE

| Colonne                   | Type         | Contraintes                                              | Description                                            |
| ------------------------- | ------------ | -------------------------------------------------------- | ------------------------------------------------------ |
| `id`                      | UUID         | PK                                                       |                                                        |
| `title`                   | VARCHAR(200) | NOT NULL, UNIQUE avec `start_date`                       | permet les éditions annuelles                          |
| `start_date` / `end_date` | DATE         | NOT NULL, CHECK `end >= start`                           |                                                        |
| `format`                  | VARCHAR(50)  | NOT NULL                                                 |                                                        |
| `registration_url`        | VARCHAR(254) |                                                          | site officiel de la ligue                              |
| `image_url`               | VARCHAR(254) |                                                          |                                                        |
| `is_featured`             | BOOLEAN      | DEFAULT FALSE ; index unique partiel `WHERE is_featured` | un seul tournoi mis en avant — à reporter en CI au MCD |
| `center_id`               | UUID         | FK → centers, NOT NULL, indexée                          | Se dérouler                                            |
| `updated_at`              | TIMESTAMPTZ  |                                                          |                                                        |

## Utilisateurs

### `users` — MLD : UTILISATEUR

| Colonne                     | Type         | Contraintes             | Description                       |
| --------------------------- | ------------ | ----------------------- | --------------------------------- |
| `id`                        | UUID         | PK                      |                                   |
| `email`                     | VARCHAR(254) | UNIQUE, NOT NULL        | identifiant naturel du MCD        |
| `username`                  | VARCHAR(50)  | UNIQUE, NOT NULL        |                                   |
| `first_name` / `last_name`  | VARCHAR(100) | NOT NULL                |                                   |
| `password_hash`             | TEXT         | NOT NULL                | empreinte bcrypt uniquement       |
| `phone`                     | VARCHAR(20)  |                         |                                   |
| `photo_url`                 | VARCHAR(500) |                         | Cloudinary                        |
| `status`                    | UserStatus   | NOT NULL, DEFAULT `ACTIVE` | suspension par un admin (UC20), un admin ne peut pas suspendre son propre compte (règle applicative) |
| `email_verified`            | BOOLEAN      | NOT NULL, DEFAULT FALSE | requis par le flux `EMAIL_VERIFY` |
| `created_at` / `updated_at` | TIMESTAMPTZ  |                         |                                   |

### `user_roles` — MLD : POSSEDER

| Colonne   | Type     | Contraintes                                |
| --------- | -------- | ------------------------------------------ |
| `user_id` | UUID     | PK composite, FK → users ON DELETE CASCADE |
| `role`    | RoleType | PK composite                               |

Cardinalité (1,n) du MCD — au moins un rôle — garantie applicativement à la création du compte.

### `center_managers` — MLD : GERER

| Colonne     | Type | Contraintes                                         |
| ----------- | ---- | --------------------------------------------------- |
| `user_id`   | UUID | PK composite, FK → users ON DELETE CASCADE, indexée |
| `center_id` | UUID | PK composite, FK → centers ON DELETE CASCADE        |

### `center_affiliations` — MLD : S_AFFILIER

| Colonne        | Type              | Contraintes                                         | Description                   |
| -------------- | ----------------- | --------------------------------------------------- | ----------------------------- |
| `user_id`      | UUID              | PK composite, FK → users ON DELETE CASCADE, indexée |                               |
| `center_id`    | UUID              | PK composite, FK → centers ON DELETE CASCADE        |                               |
| `requested_at` | TIMESTAMPTZ       | NOT NULL, DEFAULT now()                             |                               |
| `status`       | AffiliationStatus | NOT NULL, DEFAULT `PENDING`                         | validation : voir CI 2 du MCD |

PK composite fidèle au MLD : une occurrence par couple, pas d'historique des demandes
refusées puis renouvelées (limite assumée au MCD).

## Équipes

### `teams` — MLD : EQUIPE

| Colonne       | Type         | Contraintes                       | Description                                                                                                              |
| ------------- | ------------ | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `id`          | UUID         | PK                                |                                                                                                                          |
| `team_number` | VARCHAR(20)  | UNIQUE, NOT NULL                  | identifiant naturel du MCD, généré par le système à la création                                                          |
| `name`        | VARCHAR(100) | NOT NULL, UNIQUE avec `center_id` | plus d'unicité globale                                                                                                   |
| `tag`         | VARCHAR(10)  |                                   |                                                                                                                          |
| `logo_url`    | VARCHAR(255) |                                   | Cloudinary                                                                                                               |
| `center_id`   | UUID         | FK → centers, NOT NULL, indexée   | **Représenter** — choisi à la création parmi les centres où le créateur a une affiliation `APPROVED`, modifiable ensuite par le capitaine dans les mêmes limites (règle applicative) |
| `updated_at`  | TIMESTAMPTZ  |                                   |                                                                                                                          |

Plus de `captain_id` : le capitaine est une ligne de `team_members` avec `role = CAPTAIN`
(l'ancien schéma autorisait un capitaine non membre de son équipe).

### `team_members` — MLD : APPARTENIR

| Colonne        | Type             | Contraintes                                         | Description                             |
| -------------- | ---------------- | --------------------------------------------------- | --------------------------------------- |
| `user_id`      | UUID             | PK composite, FK → users ON DELETE CASCADE          |                                         |
| `team_id`      | UUID             | PK composite, FK → teams ON DELETE CASCADE, indexée |                                         |
| `requested_at` | TIMESTAMPTZ      | NOT NULL, DEFAULT now()                             |                                         |
| `origin`       | RequestOrigin    | NOT NULL                                            | qui a initié la demande                 |
| `role`         | TeamRole         | NOT NULL, DEFAULT `MEMBER`                          |                                         |
| `status`       | MembershipStatus | NOT NULL, DEFAULT `PENDING`                         | **à reporter au MCD** (statut adhesion) |

Index uniques partiels : un seul `CAPTAIN` par équipe (`WHERE role = 'CAPTAIN' AND status = 'ACTIVE'`) ;
un seul statut `ACTIVE` par joueur (`WHERE status = 'ACTIVE'`) — cette seconde règle restreint
le (0,n) du MCD et doit y figurer en CI si elle est conservée.
Règle applicative supplémentaire (UC07/UC10) : 5 membres `ACTIVE` maximum par équipe.

## Entraînements

### `trainings` — MLD : ENTRAINEMENT

| Colonne                 | Type           | Contraintes                                      | Description                      |
| ----------------------- | -------------- | ------------------------------------------------ | -------------------------------- |
| `id`                    | UUID           | PK                                               |                                  |
| `reference`             | VARCHAR(30)    | UNIQUE, NOT NULL                                 | identifiant naturel du MCD       |
| `starts_at` / `ends_at` | TIMESTAMPTZ    | NOT NULL, CHECK `ends_at > starts_at`            | fusion date + heure (décision 3) |
| `price`                 | NUMERIC(8,2)   | NOT NULL, DEFAULT 0, CHECK `>= 0`                |                                  |
| `visibility`            | VisibilityType | NOT NULL, DEFAULT `PRIVATE`                      |                                  |
| `status`                | TrainingStatus | NOT NULL, DEFAULT `SCHEDULED`                    | sans `FULL` (décision 5)         |
| `cancel_reason`         | VARCHAR(200)   | NULL                                             | motif saisi à l'annulation (UC15) |
| `description`           | TEXT           |                                                  |                                  |
| `max_capacity`          | SMALLINT       | NOT NULL, CHECK `> 0`                            |                                  |
| `center_id`             | UUID           | FK → centers, NOT NULL, indexée                  | Accueillir                       |
| `publisher_id`          | UUID           | FK → users ON DELETE RESTRICT, NOT NULL, indexée | Publier                          |
| `updated_at`            | TIMESTAMPTZ    |                                                  |                                  |

### `training_registrations` — MLD : S_INSCRIRE

| Colonne         | Type               | Contraintes                                             | Description                                                                                        |
| --------------- | ------------------ | ------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `user_id`       | UUID               | PK composite, FK → users ON DELETE CASCADE              |                                                                                                    |
| `training_id`   | UUID               | PK composite, FK → trainings ON DELETE CASCADE, indexée |                                                                                                    |
| `registered_at` | TIMESTAMPTZ        | NOT NULL, DEFAULT now()                                 |                                                                                                    |
| `status`        | RegistrationStatus | NOT NULL, DEFAULT `CONFIRMED`                           | la désinscription passe le statut à `CANCELED` (pas de suppression) ; la réinscription le réactive |
| `paid_at`       | TIMESTAMPTZ        | NULL                                                    | pointage du règlement encaissé au comptoir (UC22), `NULL` signifie « à régler »                    |

Aucune colonne d'équipe : l'équipe d'un inscrit est **dérivée** de son appartenance active
(`team_members` avec `status = ACTIVE`) au moment de l'affichage. Un joueur sans appartenance
active est présenté comme indépendant. Cet affichage est informatif, il n'engage pas la
composition des équipes pendant la séance.

Contrainte de capacité : comptage des seules `CONFIRMED`, en service, dans une transaction
avec `SELECT ... FOR UPDATE` sur la ligne d'entraînement. La désinscription est un
changement de statut, pas une suppression : traçabilité (jeu d'essai, pilotage US05) et
réinscription par simple réactivation de la ligne existante.

## Sécurité (tables techniques, hors MCD)

### `refresh_tokens`

| Colonne      | Type        | Contraintes                                     |
| ------------ | ----------- | ----------------------------------------------- |
| `id`         | UUID        | PK                                              |
| `user_id`    | UUID        | FK → users ON DELETE CASCADE, NOT NULL, indexée |
| `token_hash` | TEXT        | UNIQUE, NOT NULL — jamais le token brut         |
| `expires_at` | TIMESTAMPTZ | NOT NULL                                        |
| `revoked_at` | TIMESTAMPTZ | NULL = valide                                   |
| `created_at` | TIMESTAMPTZ | DEFAULT now()                                   |

### `verification_tokens`

| Colonne      | Type        | Contraintes                                     |
| ------------ | ----------- | ----------------------------------------------- |
| `id`         | UUID        | PK                                              |
| `user_id`    | UUID        | FK → users ON DELETE CASCADE, NOT NULL, indexée |
| `token_hash` | TEXT        | UNIQUE, NOT NULL                                |
| `type`       | TokenType   | NOT NULL                                        |
| `expires_at` | TIMESTAMPTZ | NOT NULL                                        |
| `used_at`    | TIMESTAMPTZ | NULL = non consommé (usage unique, traçabilité) |
| `created_at` | TIMESTAMPTZ | DEFAULT now()                                   |

## Correspondance MLD (français) → MPD (anglais)

| MLD             | MPD              | MLD          | MPD                                              |
| --------------- | ---------------- | ------------ | ------------------------------------------------ |
| PAYS            | countries        | EQUIPE       | teams                                            |
| VILLE           | cities           | APPARTENIR   | team_members                                     |
| CENTRE          | centers          | ENTRAINEMENT | trainings                                        |
| EQUIPEMENT      | equipment        | S_INSCRIRE   | training_registrations                           |
| DISPOSER        | center_equipment | POSSEDER     | user_roles                                       |
| TOURNOI_VITRINE | tournaments      | GERER        | center_managers                                  |
| UTILISATEUR     | users            | S_AFFILIER   | center_affiliations                              |
| ROLE            | ENUM RoleType    | —            | refresh_tokens, verification_tokens (techniques) |

## Écarts au MCD à régulariser

1. `MembershipStatus` (statut adhesion) : à ajouter sur l'association Appartenir du MCD.
2. « Une seule équipe active par joueur » et « un seul tournoi mis en avant » : à inscrire
   en CI au MCD si ces règles sont conservées.
3. `email_verified` : propriété à ajouter à UTILISATEUR au MCD, ou à documenter comme
   dérivée du flux technique.
4. `status` sur UTILISATEUR (suspension, UC20) : à ajouter au MCD.
5. `DRAFT` dans le statut d'ENTRAINEMENT (UC21) : à ajouter à l'ensemble des valeurs du MCD.
6. `paid_at` sur S'INSCRIRE (UC22) et `cancel_reason` sur ENTRAINEMENT (UC15) : propriétés
   à ajouter au MCD.

## DDL PostgreSQL 16

Script de création complet. Conventions : identifiants `UUID` générés par `gen_random_uuid()`,
horodatages en `TIMESTAMPTZ`, types énumérés en `snake_case` (Prisma les mappe sur les noms
`PascalCase` du tableau des ENUMs), et index explicites sur les clés étrangères. Pour les clés
primaires composites, l'index explicite porte sur la seconde colonne, la première étant déjà
couverte par l'index de la clé primaire.

```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- ---------- Types énumérés ----------
CREATE TYPE role_type           AS ENUM ('ADMIN', 'SUPER_ORGANIZER', 'ORGANIZER', 'PLAYER');
CREATE TYPE user_status         AS ENUM ('ACTIVE', 'SUSPENDED');
CREATE TYPE center_status       AS ENUM ('ACTIVE', 'INACTIVE');
CREATE TYPE visibility_type     AS ENUM ('PUBLIC', 'PRIVATE');
CREATE TYPE training_status     AS ENUM ('DRAFT', 'SCHEDULED', 'CANCELED', 'COMPLETED');
CREATE TYPE affiliation_status  AS ENUM ('PENDING', 'APPROVED', 'REJECTED');
CREATE TYPE membership_status   AS ENUM ('PENDING', 'ACTIVE', 'REJECTED');
CREATE TYPE team_role           AS ENUM ('MEMBER', 'CAPTAIN');
CREATE TYPE request_origin      AS ENUM ('PLAYER', 'CAPTAIN');
CREATE TYPE registration_status AS ENUM ('CONFIRMED', 'CANCELED');
CREATE TYPE token_type          AS ENUM ('EMAIL_VERIFY', 'PASSWORD_RESET');

-- ---------- Référentiel géographique ----------
CREATE TABLE countries (
  id       UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  iso_code CHAR(2)      NOT NULL UNIQUE CHECK (iso_code ~ '^[A-Z]{2}$'),
  name     VARCHAR(100) NOT NULL
);

CREATE TABLE cities (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  insee_code CHAR(5)      NOT NULL UNIQUE,
  zip_code   CHAR(5)      NOT NULL,
  name       VARCHAR(150) NOT NULL,
  country_id UUID         NOT NULL REFERENCES countries (id)
);
CREATE INDEX idx_cities_country ON cities (country_id);

-- ---------- Centres ----------
CREATE TABLE centers (
  id             UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  siret          CHAR(14)      NOT NULL UNIQUE CHECK (siret ~ '^[0-9]{14}$'),
  name           VARCHAR(150)  NOT NULL,
  slug           VARCHAR(160)  NOT NULL UNIQUE,
  address_number VARCHAR(10),
  address_street VARCHAR(200)  NOT NULL,
  latitude       NUMERIC(9, 6) CHECK (latitude BETWEEN -90 AND 90),
  longitude      NUMERIC(9, 6) CHECK (longitude BETWEEN -180 AND 180),
  phone          VARCHAR(20),
  email          VARCHAR(254)  NOT NULL,
  website        VARCHAR(254),
  opening_year   SMALLINT      CHECK (opening_year BETWEEN 1800 AND 2100),
  status         center_status NOT NULL DEFAULT 'ACTIVE',
  city_id        UUID          NOT NULL REFERENCES cities (id),
  created_at     TIMESTAMPTZ   NOT NULL DEFAULT now(),
  updated_at     TIMESTAMPTZ   NOT NULL DEFAULT now(),
  CONSTRAINT uq_centers_name_city UNIQUE (name, city_id)
);
CREATE INDEX idx_centers_city ON centers (city_id);

CREATE TABLE equipment (
  id    UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  label VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE center_equipment (
  center_id    UUID NOT NULL REFERENCES centers (id) ON DELETE CASCADE,
  equipment_id UUID NOT NULL REFERENCES equipment (id),
  PRIMARY KEY (center_id, equipment_id)
);
CREATE INDEX idx_center_equipment_equipment ON center_equipment (equipment_id);

-- ---------- Tournoi vitrine ----------
CREATE TABLE tournaments (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title            VARCHAR(200) NOT NULL,
  start_date       DATE         NOT NULL,
  end_date         DATE         NOT NULL,
  format           VARCHAR(50)  NOT NULL,
  registration_url VARCHAR(254),
  image_url        VARCHAR(254),
  is_featured      BOOLEAN      NOT NULL DEFAULT FALSE,
  center_id        UUID         NOT NULL REFERENCES centers (id),
  updated_at       TIMESTAMPTZ  NOT NULL DEFAULT now(),
  CONSTRAINT uq_tournaments_title_start UNIQUE (title, start_date),
  CONSTRAINT ck_tournaments_dates CHECK (end_date >= start_date)
);
CREATE INDEX idx_tournaments_center ON tournaments (center_id);
-- Une seule fiche mise en avant à la fois
CREATE UNIQUE INDEX uq_tournaments_featured ON tournaments ((is_featured)) WHERE is_featured;

-- ---------- Utilisateurs ----------
CREATE TABLE users (
  id             UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email          VARCHAR(254) NOT NULL UNIQUE,
  username       VARCHAR(50)  NOT NULL UNIQUE,
  first_name     VARCHAR(100) NOT NULL,
  last_name      VARCHAR(100) NOT NULL,
  password_hash  TEXT         NOT NULL,
  phone          VARCHAR(20),
  photo_url      VARCHAR(500),
  status         user_status  NOT NULL DEFAULT 'ACTIVE',
  email_verified BOOLEAN      NOT NULL DEFAULT FALSE,
  created_at     TIMESTAMPTZ  NOT NULL DEFAULT now(),
  updated_at     TIMESTAMPTZ  NOT NULL DEFAULT now()
);

CREATE TABLE user_roles (
  user_id UUID      NOT NULL REFERENCES users (id) ON DELETE CASCADE,
  role    role_type NOT NULL,
  PRIMARY KEY (user_id, role)
);

CREATE TABLE center_managers (
  user_id   UUID NOT NULL REFERENCES users (id) ON DELETE CASCADE,
  center_id UUID NOT NULL REFERENCES centers (id) ON DELETE CASCADE,
  PRIMARY KEY (user_id, center_id)
);
CREATE INDEX idx_center_managers_center ON center_managers (center_id);

CREATE TABLE center_affiliations (
  user_id      UUID               NOT NULL REFERENCES users (id) ON DELETE CASCADE,
  center_id    UUID               NOT NULL REFERENCES centers (id) ON DELETE CASCADE,
  requested_at TIMESTAMPTZ        NOT NULL DEFAULT now(),
  status       affiliation_status NOT NULL DEFAULT 'PENDING',
  PRIMARY KEY (user_id, center_id)
);
CREATE INDEX idx_center_affiliations_center ON center_affiliations (center_id);

-- ---------- Équipes ----------
CREATE TABLE teams (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  team_number VARCHAR(20)  NOT NULL UNIQUE,
  name        VARCHAR(100) NOT NULL,
  tag         VARCHAR(10),
  logo_url    VARCHAR(255),
  center_id   UUID         NOT NULL REFERENCES centers (id),
  updated_at  TIMESTAMPTZ  NOT NULL DEFAULT now(),
  CONSTRAINT uq_teams_name_center UNIQUE (name, center_id)
);
CREATE INDEX idx_teams_center ON teams (center_id);

CREATE TABLE team_members (
  user_id      UUID              NOT NULL REFERENCES users (id) ON DELETE CASCADE,
  team_id      UUID              NOT NULL REFERENCES teams (id) ON DELETE CASCADE,
  requested_at TIMESTAMPTZ       NOT NULL DEFAULT now(),
  origin       request_origin    NOT NULL,
  role         team_role         NOT NULL DEFAULT 'MEMBER',
  status       membership_status NOT NULL DEFAULT 'PENDING',
  PRIMARY KEY (user_id, team_id)
);
CREATE INDEX idx_team_members_team ON team_members (team_id);
-- Un seul capitaine actif par équipe
CREATE UNIQUE INDEX uq_team_one_captain ON team_members (team_id)
  WHERE role = 'CAPTAIN' AND status = 'ACTIVE';
-- Une seule équipe active par joueur
CREATE UNIQUE INDEX uq_user_one_active_team ON team_members (user_id)
  WHERE status = 'ACTIVE';

-- ---------- Entraînements ----------
CREATE TABLE trainings (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  reference     VARCHAR(30)     NOT NULL UNIQUE,
  starts_at     TIMESTAMPTZ     NOT NULL,
  ends_at       TIMESTAMPTZ     NOT NULL,
  price         NUMERIC(8, 2)   NOT NULL DEFAULT 0 CHECK (price >= 0),
  visibility    visibility_type NOT NULL DEFAULT 'PRIVATE',
  status        training_status NOT NULL DEFAULT 'SCHEDULED',
  cancel_reason VARCHAR(200),
  description   TEXT,
  max_capacity  SMALLINT        NOT NULL CHECK (max_capacity > 0),
  center_id     UUID            NOT NULL REFERENCES centers (id),
  publisher_id  UUID            NOT NULL REFERENCES users (id) ON DELETE RESTRICT,
  updated_at    TIMESTAMPTZ     NOT NULL DEFAULT now(),
  CONSTRAINT ck_trainings_period CHECK (ends_at > starts_at)
);
CREATE INDEX idx_trainings_center ON trainings (center_id);
CREATE INDEX idx_trainings_publisher ON trainings (publisher_id);
CREATE INDEX idx_trainings_starts_at ON trainings (starts_at);

CREATE TABLE training_registrations (
  user_id       UUID                NOT NULL REFERENCES users (id) ON DELETE CASCADE,
  training_id   UUID                NOT NULL REFERENCES trainings (id) ON DELETE CASCADE,
  registered_at TIMESTAMPTZ         NOT NULL DEFAULT now(),
  status        registration_status NOT NULL DEFAULT 'CONFIRMED',
  paid_at       TIMESTAMPTZ,
  PRIMARY KEY (user_id, training_id)
);
CREATE INDEX idx_training_registrations_training ON training_registrations (training_id);

-- ---------- Sécurité (tables techniques) ----------
CREATE TABLE refresh_tokens (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id    UUID        NOT NULL REFERENCES users (id) ON DELETE CASCADE,
  token_hash TEXT        NOT NULL UNIQUE,
  expires_at TIMESTAMPTZ NOT NULL,
  revoked_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_refresh_tokens_user ON refresh_tokens (user_id);

CREATE TABLE verification_tokens (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id    UUID        NOT NULL REFERENCES users (id) ON DELETE CASCADE,
  token_hash TEXT        NOT NULL UNIQUE,
  type       token_type  NOT NULL,
  expires_at TIMESTAMPTZ NOT NULL,
  used_at    TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_verification_tokens_user ON verification_tokens (user_id);
```

Deux règles ne sont pas exprimables en DDL et restent à la charge du service :

1. **Capacité d'un entraînement.** Le comptage porte sur les seules inscriptions `CONFIRMED`
   et s'effectue dans une transaction avec `SELECT ... FOR UPDATE` sur la ligne d'entraînement,
   afin d'empêcher deux inscriptions simultanées de dépasser `max_capacity`.
2. **Cinq membres actifs maximum par équipe** (UC07 et UC10), vérifié à l'acceptation d'une
   adhésion.

Les colonnes `updated_at` sont gérées par Prisma (`@updatedAt`). En cas d'écriture hors ORM,
prévoir un trigger `BEFORE UPDATE` équivalent.

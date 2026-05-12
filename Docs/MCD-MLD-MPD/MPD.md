# MPD — Modèle Physique de Données

> Base de données PostgreSQL. Les identifiants sont des UUID générés automatiquement. Les timestamps utilisent `TIMESTAMPTZ` pour gérer les fuseaux horaires. Les colonnes `updated_at` sont gérées automatiquement par Prisma (`@updatedAt`).

---

## Sommaire

- [ENUMs](#enums)
- [Tables principales](#tables-principales)
- [Tables de jointure](#tables-de-jointure)
- [Sécurité](#sécurité)

---

## ENUMs

| Type                | Valeurs                                              |
| ------------------- | ---------------------------------------------------- |
| `RoleType`          | `ADMIN` · `SUPER_ORGANIZER` · `ORGANIZER` · `PLAYER` |
| `CenterStatus`      | `ACTIVE` · `INACTIVE`                                |
| `VisibilityType`    | `PUBLIC` · `PRIVATE`                                 |
| `StatusType`        | `SCHEDULED` · `FULL` · `CANCELED` · `COMPLETED`      |
| `AffiliationStatus` | `PENDING` · `APPROVED` · `REJECTED`                  |
| `MembershipStatus`  | `PENDING` · `ACTIVE` · `REJECTED`                    |
| `RequestOrigin`     | `PLAYER` · `CAPTAIN`                                 |
| `TokenType`         | `EMAIL_VERIFY` · `PASSWORD_RESET`                    |

```sql
CREATE TYPE "RoleType"          AS ENUM ('ADMIN', 'SUPER_ORGANIZER', 'ORGANIZER', 'PLAYER');
CREATE TYPE "CenterStatus"      AS ENUM ('ACTIVE', 'INACTIVE');
CREATE TYPE "VisibilityType"    AS ENUM ('PUBLIC', 'PRIVATE');
CREATE TYPE "StatusType"        AS ENUM ('SCHEDULED', 'FULL', 'CANCELED', 'COMPLETED');
CREATE TYPE "AffiliationStatus" AS ENUM ('PENDING', 'APPROVED', 'REJECTED');
CREATE TYPE "MembershipStatus"  AS ENUM ('PENDING', 'ACTIVE', 'REJECTED');
CREATE TYPE "RequestOrigin"     AS ENUM ('PLAYER', 'CAPTAIN');
CREATE TYPE "TokenType"         AS ENUM ('EMAIL_VERIFY', 'PASSWORD_RESET');
```

---

## Tables principales

### `users`

Compte utilisateur. Un utilisateur peut cumuler plusieurs rôles via `user_roles` (ex : `PLAYER` + `ORGANIZER`).

```sql
CREATE TABLE "users" (
  "id"            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "first_name"    VARCHAR(100)  NOT NULL,
  "last_name"     VARCHAR(100)  NOT NULL,
  "username"      VARCHAR(50)   UNIQUE NOT NULL,
  "email"         VARCHAR(255)  UNIQUE NOT NULL,
  "password_hash" TEXT          NOT NULL,
  "phone"         VARCHAR(20),
  "created_at"    TIMESTAMPTZ   DEFAULT CURRENT_TIMESTAMP
);
```

### `centers`

Centre sportif. Contient les infos géographiques et de contact. Le statut est typé via l'ENUM `CenterStatus`.

```sql
CREATE TABLE "centers" (
  "id"           UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "name"         VARCHAR(150)   NOT NULL,
  "status"       "CenterStatus" DEFAULT 'ACTIVE',
  "country"      VARCHAR(100),
  "city"         VARCHAR(100),
  "zip_code"     VARCHAR(20),
  "address"      TEXT,
  "latitude"     DECIMAL(10, 8),
  "longitude"    DECIMAL(11, 8),
  "phone"        VARCHAR(20),
  "email"        VARCHAR(255),
  "website"      VARCHAR(255),
  "equipment"    TEXT,
  "vests"        INTEGER,
  "opening_year" INTEGER,
  "url"          VARCHAR(255),
  "slug"         VARCHAR(255)   UNIQUE NOT NULL,
  "updated_at"   TIMESTAMPTZ    DEFAULT CURRENT_TIMESTAMP
);
```

### `teams`

Équipe avec un capitaine obligatoire. Un joueur ne peut appartenir qu'à une seule équipe à la fois (contrainte sur `team_members`). Champ `tag` court (ex : `[LEO]`) et `logo_url` optionnels.

```sql
CREATE TABLE "teams" (
  "id"         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "name"       VARCHAR(100) UNIQUE NOT NULL,
  "tag"        VARCHAR(10),
  "logo_url"   VARCHAR(255),
  "captain_id" UUID         NOT NULL,
  "updated_at" TIMESTAMPTZ  DEFAULT CURRENT_TIMESTAMP,
  CONSTRAINT "fk_team_captain" FOREIGN KEY ("captain_id") REFERENCES "users"("id")
);
```

### `trainings`

Créneau d'entraînement publié dans un centre. Peut être public ou privé, avec une capacité max.

```sql
CREATE TABLE "trainings" (
  "id"           UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "center_id"    UUID             NOT NULL,
  "publisher_id" UUID             NOT NULL,
  "starts_at"    TIMESTAMPTZ      NOT NULL,
  "ends_at"      TIMESTAMPTZ      NOT NULL,
  "price"        DECIMAL(10, 2),
  "visibility"   "VisibilityType" DEFAULT 'PUBLIC',
  "status"       "StatusType"     DEFAULT 'SCHEDULED',
  "description"  TEXT,
  "max_capacity" INTEGER          NOT NULL,
  "updated_at"   TIMESTAMPTZ      DEFAULT CURRENT_TIMESTAMP,
  CONSTRAINT "fk_training_center"    FOREIGN KEY ("center_id")    REFERENCES "centers"("id"),
  CONSTRAINT "fk_training_publisher" FOREIGN KEY ("publisher_id") REFERENCES "users"("id")
);
```

### `tournaments`

Vitrine d'un tournoi lié à un centre. Pas de gestion des scores ni des brackets — `registration_url` renvoie vers le site officiel de la ligue. Un seul tournoi peut être mis en avant à la fois (index partiel sur `is_active`).

```sql
CREATE TABLE "tournaments" (
  "id"               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "center_id"        UUID         NOT NULL,
  "title"            VARCHAR(200) NOT NULL,
  "starts_at"        TIMESTAMPTZ  NOT NULL,
  "ends_at"          TIMESTAMPTZ  NOT NULL,
  "format"           VARCHAR(50)  NOT NULL,
  "registration_url" VARCHAR(255),
  "image_url"        VARCHAR(255),
  "is_active"        BOOLEAN      DEFAULT FALSE,
  "updated_at"       TIMESTAMPTZ  DEFAULT CURRENT_TIMESTAMP,
  CONSTRAINT "fk_tournament_center" FOREIGN KEY ("center_id") REFERENCES "centers"("id")
);

CREATE UNIQUE INDEX "idx_unique_active_tournament"
  ON "tournaments" ("is_active") WHERE ("is_active" = TRUE);
```

---

## Tables de jointure

### `user_roles`

Rôles attribués à un utilisateur. La PK composite `(user_id, role)` permet le cumul de rôles.

```sql
CREATE TABLE "user_roles" (
  "user_id" UUID REFERENCES "users"("id") ON DELETE CASCADE,
  "role"    "RoleType" NOT NULL,
  PRIMARY KEY ("user_id", "role")
);
```

### `center_managers`

Gestionnaires d'un centre (relation N:N).

```sql
CREATE TABLE "center_managers" (
  "user_id"   UUID REFERENCES "users"("id")   ON DELETE CASCADE,
  "center_id" UUID REFERENCES "centers"("id") ON DELETE CASCADE,
  PRIMARY KEY ("user_id", "center_id")
);
```

### `center_affiliations`

Demande d'affiliation d'un joueur à un centre, avec statut de validation.

```sql
CREATE TABLE "center_affiliations" (
  "user_id"      UUID REFERENCES "users"("id")   ON DELETE CASCADE,
  "center_id"    UUID REFERENCES "centers"("id") ON DELETE CASCADE,
  "requested_at" TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  "status"       "AffiliationStatus" DEFAULT 'PENDING',
  PRIMARY KEY ("user_id", "center_id")
);
```

### `team_members`

Adhésion d'un joueur à une équipe. L'`origin` indique qui a initié la demande. L'index partiel garantit qu'un joueur ne peut être `ACTIVE` que dans une seule équipe à la fois.

```sql
CREATE TABLE "team_members" (
  "user_id"      UUID REFERENCES "users"("id")  ON DELETE CASCADE,
  "team_id"      UUID REFERENCES "teams"("id")  ON DELETE CASCADE,
  "requested_at" TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  "origin"       "RequestOrigin"    NOT NULL,
  "status"       "MembershipStatus" DEFAULT 'PENDING',
  PRIMARY KEY ("user_id", "team_id")
);

CREATE UNIQUE INDEX "idx_unique_active_team_member"
  ON "team_members" ("user_id") WHERE ("status" = 'ACTIVE');
```

### `training_registrations`

Inscription d'un utilisateur à un entraînement.

```sql
CREATE TABLE "training_registrations" (
  "user_id"       UUID REFERENCES "users"("id")     ON DELETE CASCADE,
  "training_id"   UUID REFERENCES "trainings"("id") ON DELETE CASCADE,
  "registered_at" TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY ("user_id", "training_id")
);
```

---

## Sécurité

### `refresh_tokens`

Stocke les refresh tokens pour permettre l'invalidation de session (logout, changement de mot de passe, compromission). Seul le hash du token est persisté — jamais le token brut.

```sql
CREATE TABLE "refresh_tokens" (
  "id"         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "user_id"    UUID        NOT NULL REFERENCES "users"("id") ON DELETE CASCADE,
  "token_hash" TEXT        NOT NULL UNIQUE,
  "expires_at" TIMESTAMPTZ NOT NULL,
  "revoked_at" TIMESTAMPTZ,
  "created_at" TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);
```

### `verification_tokens`

Tokens à usage unique pour la vérification d'email et la réinitialisation de mot de passe. Le champ `used_at` marque la consommation du token sans le supprimer (traçabilité).

```sql
CREATE TABLE "verification_tokens" (
  "id"         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  "user_id"    UUID         NOT NULL REFERENCES "users"("id") ON DELETE CASCADE,
  "token_hash" TEXT         NOT NULL UNIQUE,
  "type"       "TokenType"  NOT NULL,
  "expires_at" TIMESTAMPTZ  NOT NULL,
  "used_at"    TIMESTAMPTZ,
  "created_at" TIMESTAMPTZ  DEFAULT CURRENT_TIMESTAMP
);
```

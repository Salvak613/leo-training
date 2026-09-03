# Leo Training

Micro-SaaS de niche pour le laser-tag compétitif francais. Il centralise la publication des créneaux d'entraînement par les centres Megazone et l'inscription des joueurs de la ligue LEO, à la place d'une organisation dispersée sur Discord.

Projet chef-d'oeuvre, dépôt unique pour les deux phases.
**Phase en cours : Phase 1, conception.** Aucun code applicatif à ce stade.

## Dossier de conception

| Document | Contenu |
| --- | --- |
| [docs/PRD.md](docs/PRD.md) | Problème, cible, proposition de valeur, fonctionnalité principale, métriques, hors-périmètre, risques |
| [docs/SPECS.md](docs/SPECS.md) | User stories et scénarios Gherkin |
| [docs/USE-CASES.md](docs/USE-CASES.md) | Fiches de cas d'utilisation détaillées (format Cockburn) |
| [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) | Patron de conception, couches, sécurité, sobriété, stack |
| [docs/MPD.md](docs/MPD.md) | Modèle physique de données et DDL PostgreSQL |
| [docs/DESIGN.md](docs/DESIGN.md) | Charte graphique, design tokens, accessibilité, lien vers le prototype |
| [docs/benchmark.md](docs/benchmark.md) | Références concurrentes et visuelles |
| [docs/moodboard.md](docs/moodboard.md) | Univers visuel |
| [docs/BACKLOG.md](docs/BACKLOG.md) | Annexe figée, vue d'ensemble des tâches (le suivi vit dans GitHub Projects) |
| [docs/diagrams/](docs/diagrams/) | Cas d'utilisation, déploiement, séquence, MCD, MLD, MPD, wireframes |

## Liens du projet

- Suivi de projet : GitHub Projects du dépôt (Epics, user stories, tâches, milestones A à D)
- Prototype interactif et maquettes haute fidélité : lien à renseigner
- Support du pitch : `docs/PITCH.pdf`

## Stack prévue pour la Phase 2

TypeScript de bout en bout, React en SPA découplée, Node.js et Express pour l'API REST, Prisma et PostgreSQL, hébergement Render pour l'API et AlwaysData pour la base. Justification complète dans [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

## Conventions

- Commits : [Conventional Commits](https://www.conventionalcommits.org) (`feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, `test:`)
- Aucun secret versionné, un `.env.example` sera fourni en Phase 2
- Arborescence Phase 2 à venir dans le même dépôt : `apps/`, `db/`, `tests/`, `compose.yml`

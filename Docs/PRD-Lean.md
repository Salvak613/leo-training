# PRD Lean — Leo Training

---

## 1. Le problème

L'organisation du laser-tag compétitif en France (Ligue LEO et réseau Megazone) souffre d'une gestion chaotique. Toute l'organisation des entraînements repose sur des serveurs Discord dispersés et gérés individuellement par les centres, sans aucune structure commune.

**Conséquences directes de cette dispersion :**

- Aucune visibilité globale sur les lieux et les dates d'entraînement.
- Un frein important aux déplacements inter-villes pour les joueurs.
- Une porte d'entrée complexe et intimidante pour les nouveaux joueurs souhaitant s'intégrer.

---

## 2. La cible _(persona précise)_

Le marché est une niche très qualifiée comprenant environ **1 000 joueurs compétitifs en France**, membres actifs de la ligue LEO.

La cible pour ce produit se divise en deux personas principaux indissociables :

- **L'Organisateur** _(Gérant de salle Megazone)_ — Il gère le centre de laser-tag, a besoin de publier ses créneaux d'entraînement simplement et de piloter les inscriptions pour optimiser le remplissage de son espace.
- **Le Joueur** _(et Capitaine LEO)_ — Un membre actif du circuit compétitif qui cherche à trouver rapidement les sessions disponibles et à s'y inscrire instantanément, ou à coordonner la présence de son équipe.

---

## 3. La proposition de valeur unique

> _"Synchronisez vos équipes, dominez l'arène."_

Leo Training est un **hub (Micro-SaaS B2B) centralisé** qui remplace l'organisation dispersée de Discord par une plateforme unique, fluide et professionnelle. Il offre une seule interface pour toute la ligue, connectant directement l'offre (les organisateurs qui publient) et la demande (les joueurs qui s'inscrivent).

---

## 4. Une seule fonctionnalité principale pour la V1

**Le Tableau de bord de publication et d'inscription avec gestion dynamique.**

- **Côté Organisateur** — Création et publication d'un créneau d'entraînement en quelques clics (date, heure, lieu, capacité max).
- **Côté Joueur** — Visualisation des créneaux et inscription instantanée, couplée à une gestion dynamique des places disponibles en temps réel.

---

## 5. Métriques de succès _(1 à 3)_

| Métrique                 | Description                                                                                                                    |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| **Acquisition (Supply)** | Nombre de salles Megazone ayant publié au moins un créneau d'entraînement au cours du premier mois de lancement.               |
| **Engagement (Demand)**  | Nombre d'inscriptions individuelles réalisées chaque semaine via l'application au lieu de Discord.                             |
| **Taux de remplissage**  | Pourcentage moyen des places d'entraînement réservées via la plateforme par rapport à la capacité totale des créneaux publiés. |

---

## 6. Le hors-périmètre explicite _(ce que l'application ne fait PAS)_

Pour conserver une proposition de valeur claire et éviter l'usine à gaz, les éléments suivants sont strictement exclus du produit :

- **La gestion des paiements** — L'application ne gérera aucune transaction financière. Le règlement des sessions continuera de se faire physiquement au comptoir du centre.
- **L'ouverture au grand public (Loisir)** — L'outil reste exclusif au circuit compétitif. Il ne gérera jamais les réservations de parties classiques, les anniversaires ou les événements d'entreprise.
- **La gestion des tournois officiels** — La plateforme se concentre à 100% sur les entraînements. La création d'arbres de tournois et la gestion des classements officiels de la ligue ne seront pas intégrées.
- **La synchronisation des scores (Hardware)** — La connexion directe au matériel Megazone pour faire remonter automatiquement les statistiques de jeu et les scores n'est pas prévue (ni à court, ni à moyen terme). Le logiciel reste un outil purement logistique.

---

## 7. Hypothèses et risques

**Hypothèse majeure** — La douleur de l'organisation actuelle (dispersion Discord) est suffisamment forte pour que les salles et les joueurs fassent l'effort de migrer vers un nouvel outil.

| Risque                                            | Description                                                                                                                                                             | Mitigation                                                                                                                         |
| ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Adoption (Joueurs)**                            | La friction liée à la sortie de Discord. S'ils ne perçoivent pas un gain de temps immédiat, ils pourraient refuser de créer un compte.                                  | Interface extrêmement claire, responsive, avec un temps de chargement minime.                                                      |
| **Usage (Salles — syndrome de la coquille vide)** | Si les gérants de salles Megazone ne jouent pas le jeu et continuent d'utiliser leurs serveurs locaux par habitude, les joueurs n'auront aucune raison de se connecter. | Onboarder manuellement 2 ou 3 salles très influentes au lancement pour "amorcer" la plateforme et prouver la valeur par l'exemple. |

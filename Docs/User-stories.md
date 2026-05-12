# User Stories — Leo Training

---

## User Story 1 — Publication d'un créneau d'entraînement

**En tant qu'** Organisateur _(Gérant de salle)_
**Je veux** pouvoir publier un créneau d'entraînement
**Afin que** les joueurs de la ligue puissent le voir et s'y inscrire.

|           |                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------ |
| **Given** | je suis un Organisateur authentifié                                                              |
| **When**  | je planifie un nouvel entraînement avec une date, un lieu et une capacité maximale de 30 joueurs |
| **Then**  | le créneau est publié sur le hub central de la ligue                                             |
| **And**   | la capacité disponible initiale de l'entraînement est fixée à 30 places                          |

---

## User Story 2 — Inscription standard à un entraînement

**En tant que** Joueur
**Je veux** m'inscrire à un créneau disponible
**Afin de** réserver ma place pour l'entraînement.

|           |                                                                             |
| --------- | --------------------------------------------------------------------------- |
| **Given** | un entraînement publié possède des places disponibles                       |
| **And**   | je suis un Joueur authentifié non encore inscrit à cet événement            |
| **When**  | je valide mon inscription à cet entraînement                                |
| **Then**  | mon inscription est confirmée et je suis ajouté à la liste des participants |
| **And**   | la capacité disponible du créneau est mise à jour                           |

---

## User Story 3 — Gestion d'un créneau complet

**En tant que** Joueur
**Je veux** être bloqué si l'entraînement est plein
**Afin d'** éviter le surbooking dans la salle.

|           |                                                                            |
| --------- | -------------------------------------------------------------------------- |
| **Given** | un entraînement publié a atteint sa capacité maximale (0 place disponible) |
| **And**   | je suis un Joueur authentifié                                              |
| **When**  | je tente de m'inscrire à cet entraînement                                  |
| **Then**  | le système rejette mon inscription                                         |
| **And**   | je suis explicitement informé que l'entraînement est complet               |

---

## User Story 4 — Désinscription et libération de place

**En tant que** Joueur
**Je veux** pouvoir annuler ma venue
**Afin de** libérer ma place pour un autre joueur compétitif.

|           |                                                                      |
| --------- | -------------------------------------------------------------------- |
| **Given** | je suis un Joueur authentifié                                        |
| **And**   | je suis inscrit à un entraînement à venir                            |
| **When**  | j'annule mon inscription                                             |
| **Then**  | je suis retiré de la liste des participants                          |
| **And**   | la capacité disponible du créneau est mise à jour                    |

---

## User Story 5 — Pilotage du remplissage par la salle

**En tant qu'** Organisateur
**Je veux** consulter l'état de mon créneau
**Afin de** savoir qui vient et quel est mon taux de remplissage.

|           |                                                                                           |
| --------- | ----------------------------------------------------------------------------------------- |
| **Given** | je suis l'Organisateur ayant publié un entraînement spécifique                            |
| **And**   | plusieurs joueurs y sont inscrits                                                         |
| **When**  | je consulte les détails de cet entraînement                                               |
| **Then**  | j'accède à la liste nominative des joueurs inscrits                                       |
| **And**   | je visualise précisément le nombre de places restantes par rapport à la capacité maximale |

## User Story 6 — Création d'une équipe

**En tant que** Joueur
**Je veux** créer une nouvelle équipe dans le système
**Afin de** regrouper mes coéquipiers sous une même bannière.

|           |                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------ |
| **Given** | je suis un Joueur authentifié et que je n'appartiens à aucune équipe                            |
| **When**  | je crée une équipe en renseignant son nom (et éventuellement un tag/logo)                       |
| **Then**  | l'équipe est créée dans le système                                                               |
| **And**   | je suis automatiquement affecté à cette équipe en tant que membre (et potentiellement capitaine) |

---

## User Story 7 — Ralliement à une équipe existante

**En tant que** Joueur
**Je veux** rejoindre une équipe déjà créée
**Afin que** mon affiliation soit visible lors de mes inscriptions aux entraînements.

|           |                                                                                                          |
| --------- | -------------------------------------------------------------------------------------------------------- |
| **Given** | une équipe existe déjà dans le système                                                                   |
| **And**   | je suis un Joueur authentifié n'appartenant à aucune équipe                                              |
| **When**  | je sélectionne cette équipe pour la rejoindre                                                            |
| **Then**  | je suis ajouté à la liste des membres de cette équipe                                                    |
| **And**   | mon affiliation d'équipe sera désormais visible à côté de mon nom sur les tableaux d'inscription         |

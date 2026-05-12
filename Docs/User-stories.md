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

|           |                                                   |
| --------- | ------------------------------------------------- |
| **Given** | je suis un Joueur authentifié                     |
| **And**   | je suis inscrit à un entraînement à venir         |
| **When**  | j'annule mon inscription                          |
| **Then**  | je suis retiré de la liste des participants       |
| **And**   | la capacité disponible du créneau est mise à jour |

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
| **Given** | je suis un Joueur authentifié et que je n'appartiens à aucune équipe                             |
| **When**  | je crée une équipe en renseignant son nom (et éventuellement un tag/logo)                        |
| **Then**  | l'équipe est créée dans le système                                                               |
| **And**   | je suis automatiquement affecté à cette équipe en tant que membre (et potentiellement capitaine) |

---

## User Story 7 — Ralliement à une équipe existante

**En tant que** Joueur
**Je veux** rejoindre une équipe déjà créée
**Afin que** mon affiliation soit visible lors de mes inscriptions aux entraînements.

|           |                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------ |
| **Given** | une équipe existe déjà dans le système                                                           |
| **And**   | je suis un Joueur authentifié n'appartenant à aucune équipe                                      |
| **When**  | je sélectionne cette équipe pour la rejoindre                                                    |
| **Then**  | je suis ajouté à la liste des membres de cette équipe                                            |
| **And**   | mon affiliation d'équipe sera désormais visible à côté de mon nom sur les tableaux d'inscription |

---

## User Story 8 — Création de compte

**En tant que** Visiteur _(non encore inscrit sur la plateforme)_
**Je veux** créer un compte sur Leo Training
**Afin d'** accéder aux fonctionnalités de la ligue.

|           |                                                                                       |
| --------- | ------------------------------------------------------------------------------------- |
| **Given** | je suis un visiteur non inscrit                                                       |
| **When**  | je remplis le formulaire d'inscription avec mon email, mon pseudo et mon mot de passe |
| **Then**  | mon compte est créé et un email de confirmation m'est envoyé                          |
| **And**   | je dois valider mon adresse email avant de pouvoir me connecter                       |

---

## User Story 9 — Connexion

**En tant qu'** Utilisateur inscrit
**Je veux** me connecter à mon compte
**Afin d'** accéder à l'ensemble des fonctionnalités de la plateforme.

|           |                                                                             |
| --------- | --------------------------------------------------------------------------- |
| **Given** | je possède un compte dont l'adresse email a été vérifiée                    |
| **When**  | je saisis mon email et mon mot de passe                                     |
| **Then**  | je suis authentifié et redirigé vers la page d'accueil                      |
| **And**   | je reste connecté jusqu'à déconnexion explicite ou expiration de ma session |

---

## User Story 10 — Consultation des créneaux disponibles

**En tant que** Joueur
**Je veux** consulter la liste des prochains entraînements
**Afin de** trouver rapidement une session à laquelle participer.

|           |                                                                                                         |
| --------- | ------------------------------------------------------------------------------------------------------- |
| **Given** | je suis un Joueur authentifié                                                                           |
| **When**  | je consulte la page d'accueil                                                                           |
| **Then**  | je vois la liste des prochains entraînements disponibles avec leur date, lieu, prix et places restantes |
| **And**   | je vois pour chaque créneau les équipes déjà inscrites et leur nombre de membres présents               |

---

## User Story 11 — Affiliation à un centre

**En tant que** Joueur
**Je veux** m'affilier à un centre Megazone
**Afin d'** accéder aux entraînements en visibilité privée organisés par ce centre.

|           |                                                                                     |
| --------- | ----------------------------------------------------------------------------------- |
| **Given** | je suis un Joueur authentifié non encore affilié à un centre                        |
| **And**   | ce centre propose des entraînements dont la visibilité est restreinte à ses membres |
| **When**  | j'envoie une demande d'affiliation à ce centre                                      |
| **Then**  | ma demande est enregistrée avec le statut "En attente"                              |
| **And**   | un Organisateur du centre peut approuver ou rejeter ma demande                      |

---

## User Story 12 — Invitation d'un joueur par le capitaine

**En tant que** Joueur _(Capitaine d'une équipe)_
**Je veux** inviter un joueur à rejoindre mon équipe
**Afin de** constituer mon roster sans attendre une demande de sa part.

|           |                                                                                       |
| --------- | ------------------------------------------------------------------------------------- |
| **Given** | je suis le capitaine d'une équipe                                                     |
| **And**   | le joueur ciblé n'appartient à aucune équipe                                          |
| **When**  | j'envoie une invitation à ce joueur                                                   |
| **Then**  | une demande d'adhésion est créée avec le statut "En attente" et l'origine "Capitaine" |
| **And**   | le joueur peut accepter ou refuser l'invitation                                       |

---

## User Story 13 — Annulation d'un créneau

**En tant qu'** Organisateur
**Je veux** pouvoir annuler un créneau que j'ai publié
**Afin d'** informer les joueurs inscrits que la session n'aura pas lieu.

|           |                                                                                |
| --------- | ------------------------------------------------------------------------------ |
| **Given** | je suis l'Organisateur ayant publié un entraînement dont le statut est "Prévu" |
| **When**  | j'annule ce créneau depuis mon tableau de bord                                 |
| **Then**  | le statut de l'entraînement passe à "Annulé"                                   |
| **And**   | le créneau n'est plus accessible aux nouvelles inscriptions                    |

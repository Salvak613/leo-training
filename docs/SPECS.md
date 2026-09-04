# Spécifications fonctionnelles, Leo Training

User stories et scénarios d'acceptation. Les scénarios sont écrits en **Gherkin francais** (`# language: fr`), au niveau du comportement métier observable : ils décrivent ce que le système garantit, jamais la suite de clics qui y mène.

Chaque user story renvoie à sa fiche de cas d'utilisation détaillée dans `USE-CASES.md`.

## Correspondance user stories et cas d'utilisation

| US | Intitulé | UC | Lot |
| --- | --- | --- | --- |
| US01 | Publication d'un créneau d'entraînement | UC12 | V1, principale |
| US02 | Inscription à un entraînement | UC04 | V1, principale |
| US03 | Blocage sur un créneau complet | UC04 (extension) | V1, principale |
| US04 | Désinscription et libération de place | UC05 | V1, principale |
| US05 | Pilotage du remplissage | UC13 | V1, principale |
| US06 | Création d'une équipe | UC06 | V1, socle |
| US07 | Ralliement à une équipe existante | UC07 | V1, socle |
| US08 | Création de compte | UC02 | V1, socle |
| US09 | Connexion | UC03 | V1, socle |
| US10 | Consultation des créneaux disponibles | UC01 | V1, principale |
| US11 | Affiliation à un centre | UC08 | V1, socle |
| US12 | Invitation d'un joueur par le capitaine | UC10 | V1, socle |
| US13 | Annulation d'un créneau | UC15 | V1, principale |
| US14 | Publication dans n'importe quel centre | UC16 | V1, principale |
| US15 | Modification de la photo de profil | UC09 | V1, socle |
| US16 | Modification du logo d'équipe | UC11 | V1, socle |
| US17 | Validation d'une demande d'affiliation | UC14 | V1, socle |
| US18 | Gestion des comptes et des rôles | UC17 | V1, socle |
| US19 | Affectation d'un organisateur à un centre | UC18 | V1, socle |
| US20 | Gestion de la vitrine tournoi | UC19 | V1, socle |
| US21 | Suspension d'un compte | UC20 | V1.1 |
| US22 | Enregistrement d'un créneau en brouillon | UC21 | V1.1 |
| US23 | Pointage du règlement encaissé sur place | UC22 | V1.1 |
| US24 | Modification d'un créneau publié | UC23 | V1.1 |
| US25 | Réinitialisation du mot de passe | UC24 | V1, socle |

---

# Fonctionnalité principale : le créneau d'entraînement

## US10, Consultation des créneaux disponibles

**En tant que** joueur, **je veux** consulter la liste des prochains entraînements, **afin de** trouver rapidement une session à laquelle participer.

```gherkin
# language: fr
Fonctionnalité: Consultation des créneaux

  Scénario: Un visiteur ne voit que les créneaux publics
    Etant donné que je ne suis pas authentifié
    Et qu'il existe un créneau public et un créneau privé
    Quand je consulte la liste des entraînements
    Alors seul le créneau public m'est présenté
    Et le créneau privé n'apparaît sous aucune forme, ni carte ni compteur

  Scénario: Un joueur affilié voit aussi les créneaux privés de son centre
    Etant donné que je suis un joueur authentifié affilié au centre "Megazone Lille"
    Et que ce centre propose un créneau en visibilité privée
    Quand je consulte la liste des entraînements
    Alors le créneau privé de "Megazone Lille" m'est présenté
    Et chaque créneau indique sa date, son centre, son tarif et ses places restantes
    Et chaque créneau indique les équipes déjà inscrites et le nombre de leurs membres présents

  Scénario: Aucun créneau à venir
    Etant donné qu'aucun entraînement à venir n'est publié
    Quand je consulte la liste des entraînements
    Alors le système m'informe qu'aucun entraînement n'est planifié
```

## US01, Publication d'un créneau d'entraînement

**En tant qu'** organisateur, **je veux** publier un créneau d'entraînement, **afin que** les joueurs de la ligue puissent le voir et s'y inscrire.

```gherkin
Fonctionnalité: Publication d'un créneau

  Scénario: Publication d'un créneau valide
    Etant donné que je suis un organisateur authentifié rattaché à un centre
    Quand je publie un entraînement à une date future avec une capacité de 30 joueurs
    Alors le créneau est publié sur le hub de la ligue, rattaché à mon centre
    Et sa capacité disponible initiale est de 30 places
    Et sa visibilité détermine qui peut le consulter

  Scénario: Refus d'une date passée
    Etant donné que je suis un organisateur authentifié
    Quand je tente de publier un entraînement à une date antérieure à aujourd'hui
    Alors le système rejette la publication
    Et m'indique que la date doit être postérieure à aujourd'hui

  Scénario: Refus d'une capacité nulle
    Etant donné que je suis un organisateur authentifié
    Quand je tente de publier un entraînement avec une capacité de 0 joueur
    Alors le système rejette la publication
    Et m'indique que la capacité doit être supérieure à zéro

  Scénario: Avertissement non bloquant en cas de chevauchement
    Etant donné qu'un créneau occupe déjà le samedi 14 octobre de 19h à 21h dans mon centre
    Quand je publie un second créneau sur le même horaire et le même centre
    Alors le système m'avertit du chevauchement
    Et la publication reste possible
```

## US02, Inscription à un entraînement

**En tant que** joueur, **je veux** m'inscrire à un créneau disponible, **afin de** réserver ma place pour l'entraînement.

```gherkin
Fonctionnalité: Inscription à un entraînement

  Scénario: Inscription à un créneau disponible
    Etant donné que je suis un joueur authentifié non inscrit à ce créneau
    Et que le créneau affiche 12 inscriptions confirmées pour 20 places
    Quand je valide mon inscription
    Alors mon inscription est confirmée
    Et je figure dans la liste des participants
    Et le créneau affiche 13 inscriptions confirmées pour 20 places

  Scénario: Inscription impossible sur un créneau annulé
    Etant donné que je suis un joueur authentifié
    Et que le créneau a été annulé par son organisateur
    Quand je consulte ce créneau
    Alors l'inscription m'est refusée
    Et le système m'informe que la session n'aura pas lieu

  Scénario: Une seule inscription par joueur et par créneau
    Etant donné que je suis déjà inscrit à ce créneau
    Quand je tente de m'inscrire à nouveau
    Alors le système rejette la demande et m'informe que je suis déjà inscrit
```

## US03, Blocage sur un créneau complet

**En tant que** joueur, **je veux** être bloqué si l'entraînement est plein, **afin d'** éviter le surbooking dans la salle.

```gherkin
Fonctionnalité: Créneau complet

  Scénario: Refus d'inscription sur un créneau plein
    Etant donné qu'un créneau a atteint sa capacité maximale
    Et que je suis un joueur authentifié
    Quand je tente de m'inscrire
    Alors le système rejette mon inscription
    Et m'informe explicitement que l'entraînement est complet
    Et aucune liste d'attente ne m'est proposée

  Scénario: Le créneau devient complet pendant ma consultation
    Etant donné qu'il restait une place quand j'ai ouvert la page du créneau
    Et que cette place a été prise entre-temps par un autre joueur
    Quand je valide mon inscription
    Alors le système rejette mon inscription et m'informe que le créneau est complet
```

## US04, Désinscription et libération de place

**En tant que** joueur, **je veux** pouvoir annuler ma venue, **afin de** libérer ma place pour un autre joueur.

```gherkin
Fonctionnalité: Désinscription

  Scénario: Annulation de ma participation
    Etant donné que je suis inscrit à un entraînement à venir
    Quand j'annule ma participation
    Alors mon inscription passe au statut annulé et reste conservée pour la traçabilité
    Et je ne figure plus parmi les participants confirmés
    Et la place est de nouveau disponible

  Scénario: Réinscription au même créneau
    Etant donné que j'ai annulé ma participation à un entraînement à venir
    Et qu'il reste au moins une place disponible
    Quand je me réinscris à ce créneau
    Alors mon inscription existante est réactivée et redevient confirmée

  Scénario: Désinscription impossible après la séance
    Etant donné que l'entraînement a déjà eu lieu
    Quand je tente d'annuler ma participation
    Alors le système refuse l'opération
```

## US05, Pilotage du remplissage

**En tant qu'** organisateur, **je veux** consulter l'état de mon créneau, **afin de** savoir qui vient et quel est mon taux de remplissage.

```gherkin
Fonctionnalité: Tableau de bord d'un créneau

  Scénario: Consultation des inscrits
    Etant donné que je suis l'organisateur d'un créneau publié
    Et que plusieurs joueurs y sont inscrits
    Quand je consulte le détail de ce créneau
    Alors j'accède à la liste nominative des joueurs confirmés
    Et chaque joueur est présenté avec son équipe active, ou comme indépendant s'il n'en a pas
    Et je vois le nombre de places restantes rapporté à la capacité maximale

  Scénario: Créneau sans aucun inscrit
    Etant donné que je suis l'organisateur d'un créneau publié sans inscription
    Quand je consulte le détail de ce créneau
    Alors la liste est vide et la totalité de la capacité est annoncée disponible
```

## US13, Annulation d'un créneau

**En tant qu'** organisateur, **je veux** annuler un créneau que j'ai publié, **afin d'** informer les joueurs inscrits que la session n'aura pas lieu.

```gherkin
Fonctionnalité: Annulation d'un créneau

  Scénario: Annulation d'un créneau prévu
    Etant donné que je suis l'organisateur d'un créneau au statut prévu
    Quand j'annule ce créneau en indiquant un motif et que je confirme
    Alors le statut du créneau passe à annulé
    Et le motif est visible par les joueurs sur la page du créneau
    Et les joueurs inscrits sont prévenus par email
    Et le créneau n'accepte plus aucune inscription

  Scénario: Annulation impossible sur un créneau terminé
    Etant donné qu'un créneau est déjà terminé
    Quand je tente de l'annuler
    Alors le système refuse l'opération
```

## US14, Publication dans n'importe quel centre

**En tant que** super organisateur, **je veux** publier un créneau dans un centre dont je ne suis pas gestionnaire, **afin de** préparer les joueurs de la ligue à un tournoi quel que soit le centre concerné.

```gherkin
Fonctionnalité: Publication par un super organisateur

  Scénario: Publication dans un centre du réseau
    Etant donné que je suis un super organisateur authentifié
    Quand je publie un entraînement en sélectionnant un centre actif du réseau
    Alors le créneau est publié et rattaché au centre sélectionné
    Et les joueurs éligibles peuvent s'y inscrire selon la visibilité choisie

  Scénario: Refus d'un centre inactif
    Etant donné que je suis un super organisateur authentifié
    Quand je tente de publier un entraînement dans un centre inactif
    Alors le système rejette la publication et m'en indique la raison
```

---

# Socle : comptes, équipes, centres

## US08, Création de compte

**En tant que** visiteur, **je veux** créer un compte sur Leo Training, **afin d'** accéder aux fonctionnalités de la ligue.

```gherkin
Fonctionnalité: Création de compte

  Scénario: Inscription avec des données valides
    Etant donné que je suis un visiteur non inscrit
    Quand je renseigne mon prénom, mon nom, mon pseudo, mon email et un mot de passe conforme
    Alors mon compte est créé avec le rôle joueur
    Et un email de vérification m'est envoyé
    Et je dois valider mon adresse avant de pouvoir me connecter

  Scénario: Mot de passe non conforme
    Etant donné que je suis un visiteur non inscrit
    Quand je choisis un mot de passe qui ne respecte pas les règles de la plateforme
    Alors le système rejette la création
    Et m'indique les critères non respectés parmi les quatre attendus : douze caractères minimum, une majuscule, un chiffre, un caractère spécial

  Scénario: Email ou pseudo déjà utilisé
    Etant donné qu'un compte existe déjà avec cet email
    Quand je tente de créer un compte avec le même email
    Alors le système rejette la création et m'en informe

  Scénario: Affiliation demandée dès l'inscription
    Etant donné que je crée mon compte
    Quand je sélectionne un centre d'affiliation, champ facultatif
    Alors une demande d'affiliation à ce centre est enregistrée en attente
    Et l'absence de choix ne bloque ni la création du compte ni la consultation des créneaux publics
```

## US09, Connexion

**En tant qu'** utilisateur inscrit, **je veux** me connecter à mon compte, **afin d'** accéder à l'ensemble des fonctionnalités.

```gherkin
Fonctionnalité: Connexion

  Scénario: Connexion réussie
    Etant donné que je possède un compte dont l'email est vérifié
    Quand je saisis des identifiants valides
    Alors je suis authentifié et redirigé vers la page d'accueil
    Et ma session est renouvelée silencieusement jusqu'à déconnexion explicite ou expiration

  Scénario: Identifiants invalides
    Etant donné que je possède un compte
    Quand je saisis un mot de passe erroné
    Alors le système refuse la connexion avec un message générique qui ne révèle pas si l'email existe

  Scénario: Compte non vérifié
    Etant donné que mon adresse email n'a pas encore été validée
    Quand je tente de me connecter
    Alors le système m'invite à valider mon adresse et me propose de renvoyer l'email

  Scénario: Trop de tentatives
    Etant donné que plusieurs tentatives de connexion ont échoué successivement
    Quand je tente à nouveau de me connecter
    Alors le système bloque temporairement la connexion et m'indique le délai d'attente
```

## US25, Réinitialisation du mot de passe

**En tant qu'** utilisateur inscrit, **je veux** réinitialiser mon mot de passe oublié, **afin de** retrouver l'accès à mon compte.

```gherkin
Fonctionnalité: Réinitialisation du mot de passe

  Scénario: Demande de réinitialisation
    Etant donné que j'ai oublié mon mot de passe
    Quand je demande une réinitialisation en saisissant une adresse email
    Alors le système affiche une confirmation neutre qui ne révèle pas si un compte existe
    Et un lien à usage unique et à durée limitée est envoyé si le compte existe

  Scénario: Lien expiré
    Etant donné que le lien de réinitialisation a dépassé sa durée de validité
    Quand je l'utilise
    Alors le système refuse et me propose de relancer une demande
```

## US15, Modification de la photo de profil

**En tant que** joueur, **je veux** ajouter ou modifier ma photo de profil, **afin d'** être reconnu par mes coéquipiers.

```gherkin
Fonctionnalité: Photo de profil

  Scénario: Téléversement d'une image valide
    Etant donné que je suis un joueur authentifié
    Quand je téléverse une image conforme aux formats et à la taille autorisés
    Alors l'image est hébergée sur le service de stockage externe
    Et son adresse est enregistrée dans mon profil
    Et ma nouvelle photo est visible sur ma fiche joueur

  Scénario: Fichier refusé
    Etant donné que je suis un joueur authentifié
    Quand je téléverse un fichier au format non supporté ou trop volumineux
    Alors le système rejette le fichier et m'indique la contrainte non respectée
    Et ma photo actuelle est conservée

  Scénario: Service de stockage indisponible
    Etant donné que le service de stockage externe ne répond pas
    Quand je téléverse une image
    Alors le système m'affiche une erreur et conserve ma photo actuelle
```

## US11, Affiliation à un centre

**En tant que** joueur, **je veux** m'affilier à un centre, **afin d'** accéder à ses entraînements en visibilité privée.

```gherkin
Fonctionnalité: Affiliation à un centre

  Scénario: Demande d'affiliation
    Etant donné que je suis un joueur authentifié non affilié à ce centre
    Quand j'envoie une demande d'affiliation
    Alors ma demande est enregistrée en attente
    Et un organisateur de ce centre, ou un administrateur, peut l'approuver ou la rejeter

  Scénario: Demande déjà en cours
    Etant donné que j'ai déjà une demande en attente pour ce centre
    Quand je tente d'en envoyer une nouvelle
    Alors le système m'informe que ma demande est déjà en cours d'examen
```

## US17, Validation d'une demande d'affiliation

**En tant qu'** organisateur, **je veux** approuver ou rejeter les demandes d'affiliation à mon centre, **afin de** contrôler l'accès aux créneaux privés.

```gherkin
Fonctionnalité: Traitement des demandes d'affiliation

  Scénario: Approbation d'une demande
    Etant donné que je suis un organisateur rattaché à un centre
    Et qu'un joueur a soumis une demande d'affiliation à ce centre
    Quand j'approuve la demande
    Alors le statut de la demande passe à approuvé
    Et le joueur accède aux créneaux en visibilité privée de ce centre

  Scénario: Centre sans organisateur rattaché
    Etant donné qu'un centre n'a aucun organisateur rattaché
    Et que des demandes d'affiliation y sont en attente
    Quand un administrateur consulte ce centre
    Alors le centre lui est signalé comme orphelin
    Et il peut traiter les demandes ou rattacher un organisateur
```

## US06, Création d'une équipe

**En tant que** joueur, **je veux** créer une équipe, **afin de** regrouper mes coéquipiers sous une même bannière.

```gherkin
Fonctionnalité: Création d'une équipe

  Scénario: Création par un joueur affilié
    Etant donné que je suis un joueur authentifié sans équipe active
    Et que je possède une affiliation approuvée à un centre
    Quand je crée une équipe en choisissant ce centre et en renseignant un nom disponible
    Alors l'équipe est créée et rattachée à ce centre
    Et un numéro d'équipe unique lui est attribué par le système
    Et je deviens capitaine et membre actif de cette équipe

  Scénario: Aucune affiliation approuvée
    Etant donné que je suis un joueur authentifié sans affiliation approuvée
    Quand je tente de créer une équipe
    Alors le système m'invite d'abord à demander une affiliation à un centre

  Scénario: Nom déjà pris dans ce centre
    Etant donné qu'une équipe portant ce nom représente déjà ce centre
    Quand je tente de créer une équipe avec le même nom pour ce centre
    Alors le système rejette la création
    Et le même nom reste disponible pour un autre centre

  Scénario: Un joueur ne peut appartenir qu'à une équipe active
    Etant donné que je suis déjà membre actif d'une équipe
    Quand je tente de créer une équipe
    Alors le système rejette la création et m'invite à quitter mon équipe actuelle
```

## US07, Ralliement à une équipe existante

**En tant que** joueur, **je veux** rejoindre une équipe déjà créée, **afin que** mon affiliation soit visible lors de mes inscriptions.

```gherkin
Fonctionnalité: Adhésion à une équipe

  Scénario: Demande acceptée par le capitaine
    Etant donné que je suis un joueur authentifié sans équipe active
    Quand j'envoie une demande d'adhésion à une équipe
    Alors une demande est enregistrée en attente, à mon initiative
    Et lorsque le capitaine l'accepte, je deviens membre actif de l'équipe
    Et mon appartenance apparaît à côté de mon nom sur les créneaux où je suis inscrit

  Scénario: Equipe complète
    Etant donné qu'une équipe compte déjà cinq membres actifs
    Quand une demande d'adhésion lui est adressée
    Alors le système refuse l'ajout tant qu'aucune place ne s'est libérée
```

## US12, Invitation d'un joueur par le capitaine

**En tant que** capitaine, **je veux** inviter un joueur à rejoindre mon équipe, **afin de** constituer mon roster sans attendre une demande de sa part.

```gherkin
Fonctionnalité: Invitation dans une équipe

  Scénario: Invitation acceptée
    Etant donné que je suis capitaine d'une équipe non complète
    Et que le joueur ciblé n'appartient à aucune équipe active
    Quand je lui envoie une invitation
    Alors une demande est enregistrée en attente, à l'initiative du capitaine
    Et lorsque le joueur accepte, il devient membre actif de l'équipe

  Scénario: Invitation refusée
    Etant donné qu'une invitation est en attente
    Quand le joueur la refuse
    Alors la demande passe au statut rejeté et l'effectif de l'équipe est inchangé
```

## US16, Modification du logo d'équipe

**En tant que** capitaine, **je veux** définir ou modifier le logo de mon équipe, **afin de** lui donner une identité visuelle.

```gherkin
Fonctionnalité: Logo d'équipe

  Scénario: Téléversement d'un logo valide
    Etant donné que je suis capitaine d'une équipe
    Quand je téléverse une image conforme aux formats et à la taille autorisés
    Alors l'image est hébergée sur le service de stockage externe
    Et son adresse est enregistrée comme logo de l'équipe
    Et le logo est visible sur la fiche de l'équipe

  Scénario: Changement du centre représenté
    Etant donné que je suis capitaine d'une équipe
    Quand je modifie le centre représenté par mon équipe
    Alors seuls les centres où je possède une affiliation approuvée me sont proposés
```

---

# Socle : administration de la ligue

## US18, Gestion des comptes et des rôles

**En tant qu'** administrateur, **je veux** gérer les comptes et attribuer les rôles, **afin d'** habiliter les bons acteurs sur la plateforme.

```gherkin
Fonctionnalité: Gestion des rôles

  Scénario: Attribution d'un rôle
    Etant donné que je suis un administrateur authentifié
    Quand j'ajoute le rôle organisateur à un utilisateur
    Alors ses rôles sont mis à jour
    Et il dispose immédiatement des droits correspondants

  Scénario: Le rôle joueur ne peut pas être retiré
    Etant donné que je suis un administrateur authentifié
    Quand je tente de retirer le rôle joueur à un compte joueur
    Alors le système refuse, ce rôle étant le socle de tout compte non administrateur

  Scénario: Un compte administrateur est un compte dédié
    Etant donné qu'une personne administre la ligue et joue dans une équipe
    Alors elle dispose de deux comptes distincts
    Et le compte administrateur porte le seul rôle administrateur
    Et il ne peut ni s'inscrire à un créneau ni appartenir à une équipe

  Scénario: Recherche sans résultat
    Etant donné que je suis un administrateur authentifié
    Quand je recherche un utilisateur qui n'existe pas
    Alors le système affiche une absence de résultat et me propose d'affiner ma recherche
```

## US19, Affectation d'un organisateur à un centre

**En tant qu'** administrateur, **je veux** rattacher un organisateur à un centre, **afin de** lui permettre d'y publier et d'y gérer des créneaux.

```gherkin
Fonctionnalité: Rattachement d'un organisateur

  Scénario: Rattachement valide
    Etant donné que je suis un administrateur authentifié
    Et que l'utilisateur ciblé possède le rôle organisateur
    Quand je le rattache à un centre
    Alors la relation est enregistrée
    Et il peut désormais publier des créneaux pour ce centre et traiter ses demandes d'affiliation

  Scénario: Utilisateur sans le rôle requis
    Etant donné que l'utilisateur ciblé ne possède pas le rôle organisateur
    Quand je tente de le rattacher à un centre
    Alors le système rejette l'affectation
```

## US20, Gestion de la vitrine tournoi

**En tant qu'** administrateur, **je veux** mettre en avant le prochain tournoi officiel, **afin d'** informer les joueurs des échéances compétitives.

```gherkin
Fonctionnalité: Vitrine du prochain tournoi

  Scénario: Mise en avant d'une fiche
    Etant donné que je suis un administrateur authentifié
    Quand je crée une fiche tournoi et que je la mets en avant
    Alors la vitrine est visible sur la page d'accueil
    Et toute fiche précédemment mise en avant cesse de l'être
    Et à aucun moment deux fiches ne sont mises en avant simultanément

  Scénario: Retrait de la vitrine
    Etant donné qu'une fiche est mise en avant
    Quand je la retire de la vitrine sans la supprimer
    Alors la page d'accueil présente la liste des entraînements sans bloc tournoi
```

---

# Lot V1.1

## US21, Suspension d'un compte

**En tant qu'** administrateur, **je veux** suspendre un compte, **afin de** couper l'accès d'un utilisateur sans supprimer son historique.

```gherkin
Fonctionnalité: Suspension d'un compte

  Scénario: Suspension d'un joueur
    Etant donné que je suis un administrateur authentifié
    Quand je suspends le compte d'un utilisateur et que je confirme
    Alors cet utilisateur ne peut plus se connecter ni s'inscrire à un créneau
    Et ses inscriptions passées sont conservées
    Et l'opération reste réversible

  Scénario: Auto-suspension impossible
    Etant donné que je suis un administrateur authentifié
    Quand je tente de suspendre mon propre compte
    Alors le système refuse l'opération
```

## US22, Enregistrement d'un créneau en brouillon

**En tant qu'** organisateur, **je veux** enregistrer un créneau en brouillon, **afin de** préparer une session avant de l'ouvrir aux inscriptions.

```gherkin
Fonctionnalité: Brouillon de créneau

  Scénario: Enregistrement sans publication
    Etant donné que je suis un organisateur authentifié
    Quand j'enregistre un créneau comme brouillon
    Alors le créneau n'apparaît pas sur le hub
    Et aucune inscription n'y est possible
    Et je peux le retrouver et le publier plus tard
```

## US23, Pointage du règlement encaissé sur place

**En tant qu'** organisateur, **je veux** marquer un joueur comme ayant réglé sa séance, **afin de** suivre les encaissements du comptoir sans quitter la plateforme.

```gherkin
Fonctionnalité: Pointage du règlement

  Scénario: Marquer une inscription comme réglée
    Etant donné que je suis l'organisateur d'un créneau
    Et qu'un joueur y est inscrit et confirmé
    Quand je marque son inscription comme réglée
    Alors la date du règlement est enregistrée
    Et le tableau de bord distingue les joueurs réglés de ceux qui restent à régler
    Et aucun paiement n'est traité par la plateforme
```

## US24, Modification d'un créneau publié

**En tant qu'** organisateur, **je veux** modifier un créneau déjà publié, **afin de** corriger une information sans annuler la session.

```gherkin
Fonctionnalité: Modification d'un créneau

  Scénario: Modification acceptée
    Etant donné que je suis l'organisateur d'un créneau publié
    Quand je modifie ses horaires, sa description ou son tarif
    Alors les modifications sont enregistrées
    Et les inscriptions déjà confirmées sont conservées

  Scénario: Capacité inférieure aux inscriptions confirmées
    Etant donné qu'un créneau compte douze inscriptions confirmées
    Quand je tente de ramener sa capacité à dix places
    Alors le système rejette la modification
    Et m'indique le nombre d'inscriptions déjà confirmées

  Scénario: Centre non modifiable
    Etant donné qu'un créneau est publié et rattaché à un centre
    Quand je modifie ce créneau
    Alors le centre hôte n'est pas modifiable
```

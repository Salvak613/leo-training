# Fiches de cas d'utilisation, Leo Training

Chaque fiche suit le format Cockburn : acteur principal, objectif, scénario nominal, extensions (flux alternatifs et exceptions). La correspondance avec les user stories figure en tête de `SPECS.md`.

Acteurs du système : Visiteur, Joueur (dont Capitaine), Organisateur, Super Organisateur, Administrateur de la ligue.

---

## UC01, Consulter les créneaux disponibles

**Acteur principal :** Visiteur
**Objectif :** Permettre à tout visiteur de consulter les entraînements à venir sans nécessiter de compte.

**Scénario nominal**

1. Le visiteur accède à la page d'accueil.
2. Le système affiche les créneaux publics à venir. Si le visiteur est un joueur authentifié, les créneaux privés des centres auxquels il est affilié s'affichent également.
3. Le visiteur sélectionne un créneau pour voir ses détails.
4. Le système affiche les équipes inscrites et leur nombre de membres présents.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : aucun créneau visible, le système affiche « Aucun entraînement planifié ».
- Alt 2 : erreur de chargement, le système affiche une erreur et propose de réessayer.
- Alt 3 : un créneau en visibilité privée dont le visiteur n'est pas membre n'est jamais affiché, ni sous forme de carte, ni dans les compteurs.

---

## UC02, Créer un compte

**Acteur principal :** Visiteur
**Objectif :** Permettre à un nouveau visiteur de créer un compte pour accéder aux fonctionnalités de la ligue.

**Scénario nominal**

1. Le visiteur clique sur « Créer un compte ».
2. Le système affiche le formulaire d'inscription.
3. Le visiteur saisit son prénom, son nom, son pseudo, son email et son mot de passe. Il peut, facultativement, sélectionner un centre d'affiliation.
4. Le système valide le format, la robustesse du mot de passe et l'unicité de l'email et du pseudo.
5. Le système crée le compte avec le rôle `PLAYER` par défaut, et enregistre le cas échéant une demande d'affiliation au centre choisi, au statut `PENDING`.
6. Le système envoie un email de vérification via Brevo.
7. Le système informe le visiteur de consulter sa boîte mail.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : email déjà utilisé, le système rejette et informe l'utilisateur.
- Alt 2 : pseudo déjà pris, le système suggère de choisir un autre pseudo.
- Alt 3 : mot de passe non conforme, le système indique lesquels des quatre critères ne sont pas respectés (douze caractères minimum, une majuscule, un chiffre, un caractère spécial).
- Alt 4 : email de vérification non reçu, l'utilisateur peut demander un renvoi.
- Alt 5 : aucun centre sélectionné, le compte est créé sans affiliation et le joueur pourra en demander une plus tard depuis la fiche d'un centre (UC08).

---

## UC03, Se connecter

**Acteur principal :** Visiteur
**Objectif :** Accéder à son compte pour utiliser les fonctionnalités authentifiées.

**Scénario nominal**

1. Le visiteur clique sur « Se connecter ».
2. Le système affiche le formulaire de connexion.
3. Le visiteur saisit son email et son mot de passe.
4. Le système vérifie les identifiants.
5. Le système génère un access token JWT (15 min) et un refresh token (7 jours).
6. Le système redirige l'utilisateur vers la page d'accueil.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : identifiants incorrects, le système rejette avec un message générique qui ne révèle pas si l'email existe.
- Alt 2 : compte non vérifié, le système informe l'utilisateur de valider son email et propose un renvoi.
- Alt 3 : trop de tentatives échouées, le système bloque temporairement la connexion (rate limiting) et indique le délai.
- Alt 4 : compte suspendu, le système refuse la connexion et invite l'utilisateur à contacter un administrateur (voir UC20).

---

## UC04, S'inscrire à un entraînement

**Acteur principal :** Joueur authentifié
**Objectif :** Réserver sa place dans un créneau d'entraînement.

**Scénario nominal**

1. Le joueur consulte la liste des créneaux disponibles.
2. Le joueur sélectionne un entraînement avec des places disponibles.
3. Le joueur clique sur « S'inscrire ».
4. Le système vérifie qu'il reste des places et que le joueur n'est pas déjà inscrit.
5. Le système enregistre l'inscription au statut `CONFIRMED`.
6. Le système décrémente le compteur de places disponibles.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : créneau complet au moment de la validation, le système rejette et informe le joueur. Aucune liste d'attente n'est proposée.
- Alt 2 : joueur déjà inscrit, le système rejette et informe.
- Alt 3 : créneau annulé, le système informe que la session n'aura pas lieu.
- Alt 4 : le joueur n'est pas affilié au centre d'un créneau privé, le créneau ne lui est pas visible et l'inscription est impossible.

---

## UC05, Se désinscrire d'un entraînement

**Acteur principal :** Joueur authentifié
**Objectif :** Annuler sa participation pour libérer une place à un autre joueur.

**Scénario nominal**

1. Le joueur accède à la liste de ses inscriptions ou au détail du créneau concerné.
2. Le joueur clique sur « Annuler ma participation » et confirme.
3. Le système passe le statut de l'inscription à `CANCELED`. L'inscription est conservée à des fins de traçabilité, elle n'est pas supprimée.
4. La place est libérée : le compteur de places disponibles, calculé sur les seules inscriptions `CONFIRMED`, est mis à jour.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : l'entraînement a déjà eu lieu, la désinscription est impossible.
- Alt 2 : le créneau est déjà annulé, l'action est sans objet et le système informe.
- Alt 3 : le joueur se réinscrit plus tard au même créneau, le système réactive l'inscription existante (`CANCELED` vers `CONFIRMED`), sous réserve de places disponibles.

---

## UC06, Créer une équipe

**Acteur principal :** Joueur authentifié sans équipe active
**Objectif :** Regrouper des coéquipiers sous une même bannière représentant un centre.

**Scénario nominal**

1. Le joueur accède à la section « Équipes ».
2. Le joueur clique sur « Créer une équipe ».
3. Le système propose la liste des centres où le joueur possède une affiliation approuvée (`APPROVED`).
4. Le joueur sélectionne le centre que l'équipe représentera, puis renseigne le nom, et facultativement un tag et un logo.
5. Le système vérifie l'unicité du nom au sein de ce centre.
6. Le système crée l'équipe rattachée au centre, lui attribue automatiquement un numéro d'équipe unique, et désigne le joueur comme capitaine.
7. Le système ajoute le joueur à l'équipe avec le statut `ACTIVE`.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : nom d'équipe déjà utilisé dans ce centre, le système rejette et informe. Le même nom reste disponible pour un autre centre.
- Alt 2 : joueur déjà membre d'une équipe active, le système rejette et demande de quitter l'équipe actuelle d'abord.
- Alt 3 : le joueur n'a aucune affiliation approuvée, le système l'invite à demander d'abord une affiliation à un centre (UC08).

---

## UC07, Rejoindre une équipe

**Acteur principal :** Joueur authentifié sans équipe active
**Objectif :** S'affilier à une équipe existante pour que son appartenance soit visible lors des inscriptions.

**Scénario nominal**

1. Le joueur consulte la liste des équipes.
2. Le joueur sélectionne une équipe et envoie une demande d'adhésion.
3. Le système crée une demande avec le statut `PENDING` et l'origine `PLAYER`.
4. Le capitaine reçoit et accepte la demande.
5. Le système met le statut à `ACTIVE` et ajoute le joueur à l'équipe.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : joueur déjà membre d'une équipe active, le système rejette.
- Alt 2 : le capitaine refuse, le statut passe à `REJECTED` et le joueur est informé.
- Alt 3 : équipe complète (5 membres actifs maximum), le système rejette la demande.

---

## UC08, Demander une affiliation à un centre

**Acteur principal :** Joueur authentifié
**Objectif :** Accéder aux créneaux en visibilité privée organisés par un centre Megazone.

**Scénario nominal**

1. Le joueur consulte la liste des centres de la ligue.
2. Le joueur sélectionne un centre et envoie une demande d'affiliation.
3. Le système enregistre la demande avec le statut `PENDING`.
4. Un organisateur du centre examine et approuve la demande.
5. Le statut passe à `APPROVED`.
6. Le joueur peut désormais voir et rejoindre les créneaux privés de ce centre.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : demande déjà en cours pour ce centre, le système informe que la demande est en attente.
- Alt 2 : le joueur est déjà affilié à ce centre, le système informe.
- Alt 3 : l'organisateur refuse, le statut passe à `REJECTED` et le joueur est informé.
- Alt 4 : la demande peut aussi être initiée à la création du compte (UC02), le parcours de traitement est identique.

---

## UC09, Modifier sa photo de profil

**Acteur principal :** Joueur authentifié
**Objectif :** Personnaliser son compte avec une photo visible sur sa fiche joueur.

**Scénario nominal**

1. Le joueur accède à son profil.
2. Le joueur sélectionne une image depuis son appareil.
3. Le système valide le format et la taille du fichier.
4. Le backend envoie l'image à Cloudinary.
5. Cloudinary retourne l'URL de l'image hébergée.
6. Le système enregistre l'URL dans le profil du joueur.
7. La nouvelle photo est visible sur sa fiche.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : format non supporté (hors JPG, PNG, WEBP), le système rejette et informe.
- Alt 2 : fichier trop lourd, le système rejette et indique la limite autorisée.
- Alt 3 : Cloudinary indisponible, le système affiche une erreur et conserve l'ancienne photo.

---

## UC10, Inviter un joueur dans l'équipe

**Acteur principal :** Capitaine
**Objectif :** Constituer son roster sans attendre une demande spontanée du joueur.

**Scénario nominal**

1. Le capitaine accède à la gestion de son équipe.
2. Le capitaine recherche un joueur sans équipe active.
3. Le capitaine lui envoie une invitation.
4. Le système crée une demande `PENDING` avec l'origine `CAPTAIN`.
5. Le joueur reçoit l'invitation et l'accepte.
6. Le système ajoute le joueur à l'équipe avec le statut `ACTIVE`.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : le joueur est déjà membre d'une équipe active, le système rejette l'invitation.
- Alt 2 : le joueur refuse l'invitation, le statut passe à `REJECTED`.
- Alt 3 : équipe déjà complète (5 membres actifs), le système rejette.
- Alt 4 : le capitaine annule une invitation encore en attente, la demande est retirée.

---

## UC11, Modifier le logo de l'équipe

**Acteur principal :** Capitaine
**Objectif :** Donner une identité visuelle à son équipe sur la plateforme.

**Scénario nominal**

1. Le capitaine accède à la gestion de son équipe.
2. Le capitaine sélectionne une image depuis son appareil.
3. Le système valide le format et la taille du fichier.
4. Le backend envoie l'image à Cloudinary.
5. Cloudinary retourne l'URL de l'image hébergée.
6. Le système enregistre l'URL comme logo de l'équipe.
7. Le logo est visible sur la page de l'équipe.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : format non supporté, le système rejette et informe.
- Alt 2 : fichier trop lourd, le système rejette et indique la limite.
- Alt 3 : Cloudinary indisponible, le système affiche une erreur et conserve l'ancien logo.
- Alt 4 : le capitaine modifie également le nom, le tag ou le centre représenté. Seuls les centres où il possède une affiliation `APPROVED` lui sont proposés.

---

## UC12, Publier un créneau d'entraînement

**Acteur principal :** Organisateur
**Objectif :** Rendre un créneau visible et ouvert aux inscriptions sur le hub de la ligue.

**Scénario nominal**

1. L'organisateur accède à son tableau de bord.
2. L'organisateur clique sur « Nouveau créneau ».
3. L'organisateur renseigne la date, les horaires de début et de fin, la capacité maximale, la visibilité, le tarif par joueur et une description.
4. Le système valide les données saisies.
5. Le système crée le créneau avec le statut `SCHEDULED`, rattaché au centre de l'organisateur.
6. Le créneau est visible sur le hub selon la visibilité choisie, publique ou privée.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : date dans le passé, le système rejette.
- Alt 2 : capacité invalide (inférieure ou égale à 0), le système rejette.
- Alt 3 : chevauchement horaire avec un créneau existant du même centre, le système avertit sans bloquer.
- Alt 4 : l'organisateur enregistre le créneau en brouillon plutôt que de le publier (UC21).

---

## UC13, Consulter l'état d'un créneau

**Acteur principal :** Organisateur ou Super Organisateur
**Objectif :** Piloter le taux de remplissage et connaître la liste nominative des inscrits.

**Scénario nominal**

1. L'acteur accède à son tableau de bord.
2. L'acteur sélectionne un créneau publié.
3. Le système affiche la liste nominative des joueurs confirmés. L'équipe affichée pour chaque joueur est celle de son appartenance active au moment de la consultation ; un joueur sans équipe active est présenté comme indépendant.
4. Le système affiche le nombre de places restantes par rapport à la capacité maximale.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : aucun joueur inscrit, le système affiche la liste vide avec la capacité totale disponible.
- Alt 2 : le créneau est annulé, le tableau de bord le signale et n'accepte plus d'action de gestion.

---

## UC14, Valider ou refuser une demande d'affiliation

**Acteur principal :** Organisateur du centre concerné, ou Administrateur pour n'importe quel centre (contrainte d'intégrité 2 du MCD, indispensable pour les centres auxquels aucun organisateur n'est encore rattaché).
**Objectif :** Contrôler l'accès aux créneaux privés en approuvant ou rejetant les demandes des joueurs.

**Scénario nominal**

1. L'organisateur accède à la liste des demandes d'affiliation en attente pour son centre.
2. L'organisateur consulte le profil du joueur demandeur.
3. L'organisateur approuve la demande.
4. Le système met le statut à `APPROVED`.
5. Le joueur peut désormais accéder aux créneaux privés du centre.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : l'organisateur refuse, le statut passe à `REJECTED` et le joueur est informé.
- Alt 2 : aucune demande en attente, le système affiche une liste vide.
- Alt 3 : le centre n'a aucun organisateur rattaché. Les demandes restent traitables par un administrateur depuis la console (écran Centres), qui signale le centre orphelin et propose de rattacher un organisateur (UC18).

---

## UC15, Annuler un créneau

**Acteur principal :** Organisateur ou Super Organisateur
**Objectif :** Informer les joueurs inscrits que la session n'aura pas lieu.

**Scénario nominal**

1. L'acteur accède à son tableau de bord.
2. L'acteur sélectionne un créneau avec le statut `SCHEDULED`.
3. L'acteur clique sur « Annuler le créneau », saisit un motif et confirme.
4. Le système passe le statut à `CANCELED` et enregistre le motif.
5. Le système notifie par email les joueurs dont l'inscription était `CONFIRMED`.
6. Le créneau n'accepte plus de nouvelles inscriptions et le motif est visible sur sa page.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : le créneau est déjà annulé, l'action est impossible.
- Alt 2 : le créneau est terminé (`COMPLETED`), l'annulation est impossible.
- Alt 3 : l'envoi des emails échoue, l'annulation reste effective et l'échec est journalisé pour reprise.

---

## UC16, Publier un créneau dans n'importe quel centre

**Acteur principal :** Super Organisateur
**Objectif :** Organiser un entraînement dans n'importe quel centre de la ligue sans en être gestionnaire.

**Scénario nominal**

1. Le super organisateur accède à la création de créneau.
2. Le super organisateur sélectionne un centre dans la liste complète des centres actifs de la ligue.
3. Le super organisateur renseigne la date, les horaires, la capacité maximale, la visibilité, le tarif et une description.
4. Le système crée le créneau rattaché au centre sélectionné avec le statut `SCHEDULED`.
5. Le créneau est visible sur le hub selon la visibilité choisie.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : centre sélectionné inactif (`INACTIVE`), le système le rend indisponible à la sélection et rejette la publication.
- Alt 2 : date dans le passé, le système rejette.
- Alt 3 : capacité invalide (inférieure ou égale à 0), le système rejette.

---

## UC17, Gérer les comptes utilisateurs

**Acteur principal :** Administrateur
**Objectif :** Maintenir l'intégrité de la base d'utilisateurs et attribuer les rôles sur la plateforme.

**Scénario nominal**

1. L'admin accède à la console de gestion des comptes.
2. L'admin recherche un utilisateur par nom, pseudo ou email.
3. L'admin consulte son profil et ses rôles actuels.
4. L'admin ajoute ou retire un rôle (`ORGANIZER`, `SUPER_ORGANIZER`).
5. Le système met à jour les rôles de l'utilisateur dans `user_roles`.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : utilisateur introuvable, le système affiche une absence de résultat.
- Alt 2 : tentative de retrait du rôle `PLAYER`, socle de tout compte, le système rejette et propose la suspension si l'accès doit être coupé (UC20).

---

## UC18, Rattacher un organisateur à un centre

**Acteur principal :** Administrateur
**Objectif :** Autoriser un organisateur à gérer et publier des créneaux pour un centre spécifique.

**Scénario nominal**

1. L'admin accède à la gestion des centres.
2. L'admin sélectionne un centre.
3. L'admin sélectionne un utilisateur ayant le rôle `ORGANIZER`.
4. L'admin valide l'affectation.
5. Le système crée la relation dans `center_managers`.
6. L'organisateur peut désormais publier des créneaux pour ce centre et traiter ses demandes d'affiliation.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : l'utilisateur n'a pas le rôle `ORGANIZER`, le système rejette.
- Alt 2 : affectation déjà existante, le système informe que l'organisateur est déjà rattaché à ce centre.
- Alt 3 : retrait d'un organisateur d'un centre, l'admin peut supprimer la relation existante.

---

## UC19, Gérer la vitrine tournoi

**Acteur principal :** Administrateur
**Objectif :** Mettre en avant le prochain tournoi officiel sur la page d'accueil de la plateforme.

**Scénario nominal**

1. L'admin accède à la gestion de la vitrine.
2. L'admin crée une fiche tournoi (titre, dates, format, centre hôte, URL d'inscription, image).
3. L'admin met la fiche en avant.
4. Le système, dans une même transaction, retire la mise en avant de la fiche précédente puis applique la nouvelle. L'index unique partiel sur `is_featured` garantit qu'à aucun moment deux fiches ne sont mises en avant simultanément ; il rejette l'incohérence, mais c'est le service qui effectue la bascule.
5. La vitrine du tournoi est visible sur la page d'accueil.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : URL d'inscription invalide, le système rejette et informe.
- Alt 2 : image non conforme (format ou taille), le système rejette et informe.
- Alt 3 : retrait de la vitrine, l'admin retire la mise en avant sans supprimer la fiche. La page d'accueil présente alors la liste des entraînements sur toute la largeur.

---

# Lot V1.1

---

## UC20, Suspendre ou réactiver un compte

**Acteur principal :** Administrateur
**Objectif :** Couper l'accès d'un utilisateur sans supprimer son compte ni son historique.

**Scénario nominal**

1. L'admin accède à la fiche d'un compte depuis la console.
2. L'admin clique sur « Suspendre le compte » et confirme dans la modale.
3. Le système passe le statut du compte à `SUSPENDED`.
4. L'utilisateur ne peut plus se connecter ni s'inscrire à un créneau. Ses inscriptions passées sont conservées.
5. L'admin peut réactiver le compte à tout moment, ce qui rétablit le statut `ACTIVE`.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : l'admin tente de suspendre son propre compte, le système refuse et l'invite à demander l'opération à un autre administrateur.
- Alt 2 : l'utilisateur suspendu est capitaine d'une équipe, l'équipe conserve son capitaine et la question de la succession est traitée hors périmètre V1.1.

---

## UC21, Enregistrer un créneau en brouillon

**Acteur principal :** Organisateur ou Super Organisateur
**Objectif :** Préparer un créneau sans l'ouvrir aux inscriptions.

**Scénario nominal**

1. L'acteur renseigne le formulaire de création de créneau.
2. L'acteur clique sur « Enregistrer comme brouillon ».
3. Le système crée le créneau avec le statut `DRAFT`.
4. Le créneau n'apparaît pas sur le hub et n'accepte aucune inscription.
5. L'acteur retrouve son brouillon depuis son tableau de bord et le publie plus tard, ce qui fait passer le statut à `SCHEDULED`.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : les champs obligatoires ne sont pas tous renseignés, le brouillon est tout de même enregistré, mais la publication reste impossible tant que la validation complète n'est pas satisfaite.
- Alt 2 : la date du brouillon est dépassée au moment de la publication, le système rejette la publication.

---

## UC22, Pointer le règlement encaissé sur place

**Acteur principal :** Organisateur
**Objectif :** Savoir qui a réglé sa séance au comptoir, sans traiter aucun paiement en ligne.

**Scénario nominal**

1. L'organisateur accède au tableau de bord de son créneau.
2. L'organisateur repère un joueur inscrit et confirmé.
3. L'organisateur marque son inscription comme réglée.
4. Le système enregistre la date du règlement sur l'inscription.
5. Le tableau de bord distingue les joueurs réglés de ceux qui restent à régler.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : erreur de pointage, l'organisateur retire la marque et la date de règlement est effacée.
- Alt 2 : le joueur se désinscrit après avoir réglé, l'inscription passe à `CANCELED` et la date de règlement est conservée pour la traçabilité du remboursement au comptoir.

---

## UC23, Modifier un créneau publié

**Acteur principal :** Organisateur ou Super Organisateur
**Objectif :** Corriger les informations d'une session déjà ouverte aux inscriptions.

**Scénario nominal**

1. L'acteur ouvre un créneau publié depuis son tableau de bord.
2. L'acteur clique sur « Modifier ».
3. Le système affiche le formulaire pré-rempli et rappelle le nombre de joueurs déjà inscrits.
4. L'acteur modifie la date, les horaires, la capacité, la visibilité, le tarif ou la description.
5. Le système valide et enregistre les modifications. Les inscriptions confirmées sont conservées.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : la nouvelle capacité est inférieure au nombre d'inscriptions `CONFIRMED`, le système rejette et indique ce nombre.
- Alt 2 : le centre hôte n'est pas modifiable après publication, le champ est verrouillé.
- Alt 3 : le créneau est annulé ou terminé, la modification est impossible.

---

## UC24, Réinitialiser son mot de passe

**Acteur principal :** Utilisateur inscrit
**Objectif :** Retrouver l'accès à son compte après un oubli de mot de passe.

**Scénario nominal**

1. L'utilisateur clique sur « Mot de passe oublié ».
2. L'utilisateur saisit son adresse email.
3. Le système affiche une confirmation neutre, qui ne révèle pas si un compte existe pour cette adresse.
4. Si le compte existe, le système envoie un lien contenant un jeton `PASSWORD_RESET` à usage unique et à durée limitée.
5. L'utilisateur ouvre le lien et saisit un nouveau mot de passe conforme aux quatre critères.
6. Le système enregistre l'empreinte du nouveau mot de passe, marque le jeton comme consommé et révoque les refresh tokens existants.

Le cas d'utilisation se termine.

**Extensions**

- Alt 1 : le lien est expiré ou déjà utilisé, le système refuse et propose de relancer une demande.
- Alt 2 : le nouveau mot de passe ne respecte pas les critères, le système indique lesquels.

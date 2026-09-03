# Backlog de réalisation, Leo Training

> **Annexe figée.** Le suivi de projet réel vit dans le GitHub Project du dépôt (Epics, user stories, tâches, milestones, estimations). Ce fichier sert de matrice pour créer les issues et de vue d'ensemble imprimable. En cas d'écart, les issues GitHub font foi.

Ce backlog couvre la **réalisation (Phase 2)**. Les Epics de la Phase 1 portent sur les livrables de conception (étapes A à D) et vivent directement dans le GitHub Project.

Chaque US renvoie à son numéro dans `SPECS.md`, où figurent l'énoncé complet et les scénarios d'acceptation.

---

## Lot 1, fonctionnalité principale : le créneau d'entraînement

### US10, Consultation des créneaux disponibles

- [ ] Endpoint de récupération des créneaux, avec filtrage par visibilité et affiliation
- [ ] Pagination par défaut sur la liste
- [ ] Page d'accueil avec la liste des créneaux
- [ ] Composant carte de créneau (date, centre, tarif, places, équipes)

### US01, Publication d'un créneau

- [ ] Endpoint de création de créneau (validation, rattachement au centre, contrôle des droits)
- [ ] Détection du chevauchement horaire, avertissement non bloquant
- [ ] Page et formulaire de création de créneau
- [ ] Aperçu du créneau en temps réel dans le formulaire

### US02, Inscription à un entraînement

- [ ] Endpoint d'inscription, transaction avec verrou sur le créneau, comptage des seules inscriptions confirmées
- [ ] Contrôle d'unicité et de visibilité (créneau privé réservé aux affiliés)
- [ ] Bouton d'inscription et panneau de places sur le détail du créneau

### US03, Gestion d'un créneau complet

- [ ] Erreur explicite si le créneau est complet, y compris en cas de course entre deux inscriptions
- [ ] Affichage du statut « Complet » et désactivation du bouton

### US04, Désinscription d'un entraînement

- [ ] Endpoint de désinscription, passage au statut annulé sans suppression
- [ ] Réactivation de l'inscription existante en cas de réinscription
- [ ] Bouton d'annulation sur le détail du créneau et sur le profil

### US05, Pilotage du remplissage

- [ ] Endpoint tableau de bord du créneau (liste des inscrits, places, taux de remplissage)
- [ ] Rattachement de chaque inscrit à son équipe active, dérivé de l'appartenance
- [ ] Page tableau de bord de l'organisateur

### US13, Annulation d'un créneau

- [ ] Endpoint d'annulation (contrôle du statut et des droits, enregistrement du motif)
- [ ] Notification par email des joueurs confirmés
- [ ] Bouton d'annulation avec saisie du motif et confirmation

### US14, Publication dans n'importe quel centre

- [ ] Accès à la liste complète des centres actifs pour le rôle super organisateur
- [ ] Exclusion des centres inactifs à la sélection
- [ ] Sélecteur de centre avec recherche dans le formulaire de création

---

## Lot 2, socle : comptes, équipes, centres

### US08, Création de compte

- [ ] Endpoint d'inscription (validation, règles de mot de passe, hachage, création du compte)
- [ ] Demande d'affiliation facultative enregistrée à la création
- [ ] Envoi de l'email de vérification
- [ ] Endpoint de confirmation d'email
- [ ] Page et formulaire d'inscription

### US09, Connexion

- [ ] Endpoint de connexion (JWT court et refresh token en cookie HttpOnly)
- [ ] Limitation du nombre de tentatives
- [ ] Déconnexion et révocation du refresh token
- [ ] Middleware d'authentification et cloisonnement par rôle
- [ ] Page et formulaire de connexion

### US25, Réinitialisation du mot de passe

- [ ] Endpoint de demande, jeton à usage unique et à durée limitée
- [ ] Endpoint de définition du nouveau mot de passe, révocation des sessions existantes
- [ ] Écrans de demande et de nouveau mot de passe

### US15, Modification de la photo de profil

- [ ] Endpoint d'upload (validation du fichier puis envoi à Cloudinary)
- [ ] Écran de profil joueur (identité, photo, affiliations, inscriptions)

### US11, Affiliation à un centre

- [ ] Endpoint de demande d'affiliation
- [ ] Page liste des centres et fiche d'un centre avec bouton de demande

### US17, Validation d'une demande d'affiliation

- [ ] Endpoint de validation ou de refus, contrôle des droits de l'organisateur
- [ ] Interface de traitement des demandes en attente

### US06, Création d'une équipe

- [ ] Endpoint de création (unicité du nom dans le centre, attribution du numéro, désignation du capitaine)
- [ ] Restriction du choix du centre aux affiliations approuvées
- [ ] Écran de création d'équipe et état bloquant « aucune affiliation approuvée »

### US07, Ralliement à une équipe existante

- [ ] Endpoint de demande d'adhésion et endpoint d'acceptation ou de refus
- [ ] Contrôle des cinq membres actifs maximum
- [ ] Liste des équipes avec demande d'adhésion, et interface de traitement côté capitaine

### US12, Invitation d'un joueur par le capitaine

- [ ] Endpoint d'invitation et endpoint d'acceptation ou de refus
- [ ] Interface d'invitation dans la gestion d'équipe

### US16, Modification du logo d'équipe

- [ ] Endpoint d'upload du logo (validation puis Cloudinary)
- [ ] Champs nom, tag, logo et centre représenté dans les paramètres de l'équipe

---

## Lot 3, socle : administration de la ligue

### US18, Gestion des comptes et des rôles

- [ ] Endpoints de recherche et de modification des rôles
- [ ] Refus du retrait du rôle socle
- [ ] Console admin, écran Comptes avec recherche et pagination

### US19, Affectation d'un organisateur à un centre

- [ ] Endpoints d'affectation et de retrait
- [ ] Signalement des centres orphelins
- [ ] Console admin, écran Centres

### US20, Gestion de la vitrine tournoi

- [ ] Endpoints de création et de mise en avant, bascule transactionnelle
- [ ] Composant vitrine sur la page d'accueil
- [ ] Console admin, écran Vitrine tournoi

---

## Lot 4, V1.1

### US21, Suspension d'un compte

- [ ] Colonne de statut sur le compte et blocage à la connexion et à l'inscription
- [ ] Refus de l'auto-suspension
- [ ] Actions de suspension et de réactivation dans la console admin

### US22, Enregistrement d'un créneau en brouillon

- [ ] Statut brouillon, exclusion du hub et des inscriptions
- [ ] Enregistrement et publication différée depuis le tableau de bord

### US23, Pointage du règlement encaissé sur place

- [ ] Endpoint de pointage, horodatage du règlement
- [ ] Colonne de règlement dans la liste des inscrits

### US24, Modification d'un créneau publié

- [ ] Endpoint de modification, refus si la capacité descend sous les inscriptions confirmées
- [ ] Formulaire de modification, centre hôte verrouillé

---

## Transverse

- [ ] Initialisation du dépôt Phase 2 (`apps/`, `db/`, `tests/`, `compose.yml`)
- [ ] Schéma Prisma aligné sur le DDL de `MPD.md` et première migration
- [ ] Jeu de données de démonstration (centres, équipes, créneaux)
- [ ] Validation des entrées avec Zod sur tous les endpoints
- [ ] En-têtes de sécurité et limitation de débit
- [ ] Design system front, tokens et composants issus de `DESIGN.md`
- [ ] Tests des règles métier critiques (capacité, unicité, droits)
- [ ] Intégration continue et déploiement

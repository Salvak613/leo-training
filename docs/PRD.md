# PRD Lean, Leo Training

Micro-SaaS de niche pour le laser-tag compétitif francais (ligue LEO, réseau Megazone).
Phase 1, dossier de conception. Document de référence unique, il remplace toute version antérieure.

---

## 1. Le problème

L'organisation des entraînements du laser-tag compétitif repose aujourd'hui sur des serveurs Discord dispersés, gérés individuellement par chaque centre, sans structure commune.

**Formulation Jobs To Be Done, côté joueur :**

> Quand je cherche où m'entraîner dans les prochaines semaines et que l'information est éparpillée sur les Discord de plusieurs centres, je veux voir au même endroit les créneaux ouverts et les places restantes, pour que je puisse réserver ma place en une minute sans relancer personne.

**Formulation Jobs To Be Done, côté centre :**

> Quand j'ouvre ma salle à la ligue pour une session d'entraînement, je veux publier le créneau et suivre les inscriptions au même endroit, pour que je sache combien de joueurs viennent sans avoir à compter les réactions d'un fil de discussion.

Conséquences directes de cette dispersion :

- aucune visibilité globale sur les lieux et les dates d'entraînement,
- un frein important aux déplacements entre villes pour les joueurs,
- une porte d'entrée intimidante pour les nouveaux joueurs qui veulent s'intégrer au circuit,
- pour les centres, un taux de remplissage subi plutôt que piloté.

---

## 2. La cible

Une niche très qualifiée : environ **1 000 joueurs compétitifs en France**, membres actifs de la ligue LEO, et les centres Megazone qui les accueillent.

**L'Organisateur (gérant de salle Megazone).** Il gère le centre, veut publier ses créneaux simplement et piloter les inscriptions pour optimiser le remplissage de ses terrains. C'est lui qui alimente la plateforme en offre.

**Le Super Organisateur (membre de l'association LEO).** Membre du bureau de la ligue. Contrairement à un organisateur classique, il publie des entraînements dans n'importe quel centre du réseau sans en être le gestionnaire. Il intervient quand la ligue prend en main une session inter-centres, typiquement la préparation d'un tournoi.

**Le Joueur (et capitaine d'équipe).** Membre actif du circuit compétitif. Il cherche à trouver rapidement une session disponible et à s'y inscrire, ou à coordonner la présence de son équipe.

Un quatrième acteur, l'**Administrateur de la ligue**, existe dans le système (attribution des rôles, rattachement des organisateurs aux centres, vitrine tournoi). Ce n'est pas une cible commerciale mais un rôle interne, décrit dans `SPECS.md` et `USE-CASES.md`.

---

## 3. La proposition de valeur unique

> « Synchronisez vos équipes, dominez l'arène. »

Leo Training est un hub centralisé qui remplace l'organisation dispersée de Discord par une interface unique pour toute la ligue, reliant directement l'offre (les centres qui publient) et la demande (les joueurs qui s'inscrivent).

---

## 4. La fonctionnalité principale de la V1

**Le créneau d'entraînement : publication par le centre, inscription instantanée par le joueur, places à jour en continu.**

- Côté organisateur : créer et publier un créneau en quelques champs (centre, date, horaires, capacité, visibilité, tarif indicatif), puis suivre le remplissage et la liste nominative des inscrits.
- Côté joueur : voir les créneaux qui le concernent, s'inscrire en un clic, se désinscrire pour libérer sa place, et savoir en permanence combien de places restent.

Tout le produit est construit autour de cet objet unique. Le reste n'existe que pour le rendre possible.

### Le socle indispensable

Ces éléments ne sont pas des fonctionnalités vendues, ce sont les conditions techniques et métier pour que le créneau fonctionne :

| Brique | Pourquoi elle est indispensable |
| --- | --- |
| Comptes et authentification | Une inscription nominative suppose un compte vérifié |
| Affiliation à un centre | Conditionne l'accès aux créneaux en visibilité « Privé Club » |
| Équipes | Un créneau affiche qui vient et avec quelle équipe, c'est l'information qui décide un joueur à venir |
| Administration de la ligue | Attribuer les rôles et rattacher les organisateurs aux centres, sans quoi personne ne peut publier |
| Vitrine du prochain tournoi | Donne la raison de s'entraîner, en lecture seule, sans aucune gestion de tournoi |

### Ce qui est décalé en V1.1

Fonctionnalités conçues et modélisées dès la Phase 1 (elles figurent dans le modèle de données et dans les maquettes), mais volontairement sorties du lot de livraison initial pour ne pas diluer la fonctionnalité principale :

- suspension d'un compte par un administrateur,
- enregistrement d'un créneau en brouillon avant publication,
- pointage du règlement encaissé au comptoir,
- modification d'un créneau déjà publié.

---

## 5. Métriques de succès

| Métrique | Description |
| --- | --- |
| **Acquisition (offre)** | Nombre de centres Megazone ayant publié au moins un créneau pendant le premier mois |
| **Engagement (demande)** | Nombre d'inscriptions réalisées chaque semaine sur la plateforme plutôt que sur Discord |
| **Taux de remplissage** | Part moyenne des places réservées via la plateforme rapportée à la capacité totale publiée |

---

## 6. Le hors-périmètre explicite

- **Aucune transaction financière en ligne.** Le règlement continue de se faire au comptoir du centre. La plateforme se limite au pointage des paiements encaissés sur place, pour que l'organisateur sache qui a réglé.
- **Aucune ouverture au grand public.** L'outil reste réservé au circuit compétitif. Il ne gérera jamais les parties loisir, les anniversaires ou les événements d'entreprise.
- **Aucune gestion de tournoi.** Ni arbres de tournoi, ni classements officiels de la ligue. Une vitrine informative du prochain tournoi (titre, dates, format, centre, lien vers le site officiel) reste en périmètre, en lecture seule.
- **Aucune synchronisation avec le matériel.** Pas de remontée automatique des scores depuis l'équipement Megazone, ni à court ni à moyen terme. L'outil reste purement logistique.
- **Pas de liste d'attente.** Un créneau complet refuse simplement l'inscription.

---

## 7. Hypothèses et risques

**Hypothèse majeure.** La douleur de l'organisation actuelle est assez forte pour que les centres et les joueurs fassent l'effort de quitter Discord pour un outil dédié.

| Risque | Description | Mitigation |
| --- | --- | --- |
| **Adoption des joueurs** | La friction de sortie de Discord. Sans gain de temps immédiat perceptible, ils ne créeront pas de compte. | Interface très directe, responsive, chargement rapide, inscription en un clic depuis la liste |
| **Coquille vide côté centres** | Si les gérants continuent d'utiliser leurs serveurs locaux par habitude, les joueurs n'ont aucune raison de se connecter. | Embarquer manuellement deux ou trois centres influents au lancement pour amorcer la plateforme |
| **Dépendance à la ligue** | Le produit se positionne à côté du portail officiel LEO sans en être l'instance officielle. | Positionnement d'outil communautaire assumé, y compris visuellement (voir `DESIGN.md`) |

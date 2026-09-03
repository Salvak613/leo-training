# Charte graphique et design system, Leo Training

---

## 1. Parti pris visuel

Leo Training adopte un design clair, épuré et professionnel, à mi-chemin entre l'application de gestion sportive et le SaaS B2B. L'interface n'est volontairement ni sombre ni « esports » : elle doit inspirer confiance aux gérants de salle tout en restant moderne pour des joueurs compétitifs.

**Le choix de l'orange est un positionnement.** L'association LEO communique en bleu et blanc, et son site officiel ne traite que la gestion des tournois officiels. Leo Training couvre l'entraînement au quotidien, ce que personne ne couvre. L'orange traduit visuellement cette position : une séparation nette, aucune confusion possible entre les deux interfaces, et une complémentarité avec le bleu de la ligue plutôt qu'une opposition. Le message est celui d'un outil communautaire porté par les joueurs, qui s'inscrit dans l'univers LEO sans prétendre en être l'instance officielle.

Le navy `#253746` du logo est conservé comme couleur d'encre secondaire, ce qui rattache discrètement l'identité au bleu de la ligue.

---

## 2. Palette de couleurs

### Couleurs de marque

| Rôle          | Nom                 | Hex       | Utilisation                                                                     |
| ------------- | ------------------- | --------- | ------------------------------------------------------------------------------- |
| Primary       | Orange Leo Training | `#F05A28` | Remplissages uniquement : barres de progression, pastilles, bordure de relation |
| Primary dark  | Orange foncé        | `#C44A1E` | Fond des boutons primaires, texte orange, anneau de focus                       |
| Primary light | Orange clair        | `#FEF0EA` | Fonds de badges et d'alertes légères                                            |
| Ink           | Navy du logo        | `#253746` | Couleur d'encre secondaire, filets et aplats sobres                             |

### Couleurs de statut

| Rôle    | Fond clair | Texte sur ce fond | Usage                                   |
| ------- | ---------- | ----------------- | --------------------------------------- |
| Succès  | `#DCFCE7`  | `#15803D`         | Badge « Public », inscription confirmée |
| Spécial | `#EDE9FE`  | `#7C3AED`         | Badge « Privé Club »                    |
| Alerte  | `#FEF3C7`  | `#B45309`         | Créneau presque complet, avertissement  |
| Danger  | `#FEE2E2`  | `#B91C1C`         | Créneau annulé, erreurs                 |
| Accent  | `#FEF0EA`  | `#A63C16`         | Badge de relation contextuelle          |

Les teintes vives `#16A34A`, `#D97706` et `#DC2626` restent utilisées pour les remplissages et les icônes. Sur un fond clair et à petite taille, c'est la variante foncée de la colonne « Texte » qui s'applique, pour respecter le seuil AA de 4,5:1.

### Couleurs neutres

| Rôle             | Hex       |
| ---------------- | --------- |
| Fond de page     | `#F9FAFB` |
| Fond de carte    | `#FFFFFF` |
| Fond discret     | `#F3F4F6` |
| Bordure de carte | `#E5E7EB` |
| Bordure de champ | `#D1D5DB` |
| Texte titre      | `#111827` |
| Texte courant    | `#374151` |
| Texte secondaire | `#6B7280` |
| Texte indicatif  | `#9CA3AF` |

---

## 3. Typographie

Police unique : **Inter**, chargée depuis Google Fonts, avec une pile de repli système.

```html
<link
  href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap"
  rel="stylesheet"
/>
```

| Token              | Taille | Usage                                             |
| ------------------ | ------ | ------------------------------------------------- |
| `--font-size-xs`   | 12 px  | Badges, mentions, libellés de colonnes            |
| `--font-size-sm`   | 14 px  | Métadonnées (date, lieu, tarif), texte secondaire |
| `--font-size-base` | 16 px  | Corps de texte                                    |
| `--font-size-lg`   | 18 px  | Titres de cartes                                  |
| `--font-size-xl`   | 20 px  | Titres de sections                                |
| `--font-size-2xl`  | 24 px  | Titres de page                                    |
| `--font-size-3xl`  | 30 px  | Logo texte                                        |

Graisses : 400 régulier, 500 medium, 600 semibold, 700 bold. Interlignages : 1,25 serré, 1,4 rapproché, 1,55 normal.

Six alias composites évitent d'assembler les tokens à la main dans les composants : `--type-page-title`, `--type-section-title`, `--type-card-title`, `--type-body`, `--type-meta`, `--type-badge`.

---

## 4. Design tokens

```css
:root {
  /* Marque */
  --color-primary: #f05a28;
  --color-primary-dark: #c44a1e;
  --color-primary-light: #fef0ea;
  --color-ink: #253746;

  /* Statuts */
  --color-success: #16a34a;
  --color-success-light: #dcfce7;
  --color-success-text: #15803d;
  --color-special: #7c3aed;
  --color-special-light: #ede9fe;
  --color-warning: #d97706;
  --color-warning-light: #fef3c7;
  --color-warning-text: #b45309;
  --color-danger: #dc2626;
  --color-danger-light: #fee2e2;
  --color-danger-text: #b91c1c;

  /* Neutres */
  --color-bg-page: #f9fafb;
  --color-bg-card: #ffffff;
  --color-bg-subtle: #f3f4f6;
  --color-border: #e5e7eb;
  --color-border-strong: #d1d5db;
  --color-text-primary: #111827;
  --color-text-secondary: #374151;
  --color-text-muted: #6b7280;
  --color-text-placeholder: #9ca3af;

  /* Alias sémantiques */
  --surface-page: var(--color-bg-page);
  --surface-card: var(--color-bg-card);
  --surface-subtle: var(--color-bg-subtle);
  --text-title: var(--color-text-primary);
  --text-body: var(--color-text-secondary);
  --text-meta: var(--color-text-muted);
  --border-card: var(--color-border);
  --border-input: var(--color-border-strong);

  /* Accent, usages autorisés uniquement */
  --accent-fill: var(--color-primary);
  --accent-relation-border: var(--color-primary);
  --accent-text-strong: var(--color-primary-dark);

  /* Actions */
  --action-primary-bg: var(--color-primary-dark);
  --action-primary-bg-hover: #a63c16;
  --action-primary-bg-active: #8e3312;
  --action-primary-fg: #ffffff;
  --action-secondary-bg: #ffffff;
  --action-secondary-fg: var(--color-primary-dark);
  --action-secondary-border: var(--color-primary);
  --action-ghost-fg: var(--color-text-secondary);
  --action-disabled-bg: var(--color-bg-subtle);
  --action-disabled-fg: var(--color-text-muted);

  /* Focus */
  --focus-ring-color: var(--color-primary-dark);
  --focus-ring-width: 2px;
  --focus-ring-offset: 2px;

  /* Typographie */
  --font-family:
    "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  --font-size-xs: 0.75rem;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;
  --font-size-xl: 1.25rem;
  --font-size-2xl: 1.5rem;
  --font-size-3xl: 1.875rem;
  --font-weight-regular: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
  --line-height-tight: 1.25;
  --line-height-snug: 1.4;
  --line-height-normal: 1.55;
  --letter-spacing-tight: -0.01em;
  --letter-spacing-wide: 0.02em;

  /* Espacements, base 4 px */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 20px;
  --space-6: 24px;
  --space-8: 32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;

  /* Rythme d'écran */
  --layout-max-width: 1280px;
  --layout-gutter-desktop: var(--space-6);
  --layout-gutter-mobile: var(--space-4);
  --layout-header-height: 64px;
  --card-padding: var(--space-4);
  --card-gap: var(--space-4);
  --tap-target-min: 44px;

  /* Bordures */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-full: 9999px;
  --border-width-hairline: 1px;
  --border-width-relation: 2px;

  /* Ombres */
  --shadow-card: 0 1px 3px rgba(0, 0, 0, 0.08), 0 1px 2px rgba(0, 0, 0, 0.04);
  --shadow-card-hover:
    0 4px 10px rgba(17, 24, 39, 0.08), 0 1px 3px rgba(17, 24, 39, 0.06);
  --shadow-modal: 0 10px 25px rgba(0, 0, 0, 0.12);
  --shadow-focus: 0 0 0 3px rgba(196, 74, 30, 0.18);

  /* Transitions */
  --transition-fast: 150ms ease;
  --transition-base: 200ms ease;
}
```

---

## 5. Composants clés

**Carte de créneau.** Fond blanc, bordure `--border-card`, rayon `--radius-md`. En-tête : badge de visibilité, date, horaires, centre et tarif sur une ligne. Corps : compteur de places avec barre de progression. Pied : équipes inscrites et avatars. Au survol, élévation `--shadow-card-hover`.

**Bouton primaire.** Fond `--action-primary-bg` (`#C44A1E`), texte blanc, rayon `--radius-md`. Survol `--action-primary-bg-hover`, appui `--action-primary-bg-active`. Pleine largeur dans les colonnes latérales, largeur automatique en ligne. L'orange vif `#F05A28` n'est jamais utilisé comme fond de bouton portant du texte : son contraste avec le blanc n'est que de 3,4:1.

**Badges de visibilité.** « Public » sur fond `--color-success-light` avec texte `--color-success-text`. « Privé Club » sur fond `--color-special-light` avec texte `--color-special`.

**Barre de progression des places.** Piste `--border-card`, remplissage `--accent-fill`, hauteur 6 px, rayon `--radius-full`. Un libellé textuel l'accompagne toujours, par exemple « 12/20 inscrits » : l'information ne repose jamais sur la seule couleur.

**Bordure orange de relation.** Une bordure de 2 px en `--accent-relation-border` est un marqueur de relation contextuelle, pas un état d'interaction. Elle signale un lien logique entre deux éléments :

- sur l'accueil, la carte du créneau dont le centre accueille le tournoi mis en vitrine,
- sur le détail d'un créneau, la ligne de l'équipe du joueur connecté.

Elle est toujours doublée d'un indicateur textuel ou iconique, jamais employée seule.

**Choix d'un centre.** Le réseau compte des centaines de centres : toute sélection de centre passe par un composant de recherche au clavier, insensible aux accents, portant sur le nom comme sur la ville. Cela vaut pour l'inscription, le centre représenté par une équipe, le centre hôte d'un créneau et celui d'une fiche tournoi.

---

## 6. Accessibilité, RGAA 4.1

### Contrastes vérifiés

| Association                                 | Ratio  | Verdict                                             |
| ------------------------------------------- | ------ | --------------------------------------------------- |
| `#111827` sur blanc                         | 16,1:1 | AAA                                                 |
| `#374151` sur blanc                         | 10,3:1 | AAA                                                 |
| `#6B7280` sur blanc                         | 4,9:1  | AA                                                  |
| Blanc sur `#C44A1E` (bouton primaire)       | 4,9:1  | AA                                                  |
| `#C44A1E` sur blanc (liens, texte accentué) | 4,9:1  | AA                                                  |
| `#15803D` sur `#DCFCE7` (badge Public)      | 4,6:1  | AA                                                  |
| `#7C3AED` sur `#EDE9FE` (badge Privé Club)  | 4,8:1  | AA                                                  |
| `#B45309` sur `#FEF3C7` (avertissement)     | 4,5:1  | AA                                                  |
| `#B91C1C` sur `#FEE2E2` (danger)            | 5,3:1  | AA                                                  |
| `#F05A28` sur blanc                         | 3,4:1  | Insuffisant pour du texte, réservé aux remplissages |

### Autres critères

| Critère                               | Application                                                                                                                                                                              |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Navigation clavier                    | Tous les contrôles sont atteignables au clavier, l'ordre de tabulation suit l'ordre visuel. Focus visible imposé globalement : contour de 2 px en `--focus-ring-color`, décalé de 2 px.  |
| Alternatives textuelles               | Avatars d'équipe avec le nom du joueur en alternative, icônes décoratives masquées aux lecteurs d'écran, icônes fonctionnelles porteuses d'un libellé accessible.                        |
| Formulaires                           | Chaque champ possède une étiquette associée. Les messages d'erreur et les aides sont reliés au champ par `aria-describedby`. Les critères de mot de passe sont annoncés champ par champ. |
| Composants riches                     | Modales avec rôle de dialogue, attribut modal et piège de focus. Bandeau repliable de la vitrine avec `aria-expanded` et `aria-controls`. Barre d'onglets avec état courant explicite.   |
| Cibles tactiles                       | Minimum 44 px sur mobile (`--tap-target-min`), notamment sur la barre de navigation basse et les lignes de liste cliquables.                                                             |
| Information non portée par la couleur | Chaque statut associe une couleur, un libellé et une icône. Places restantes toujours doublées d'un compteur chiffré.                                                                    |

---

## 7. Maquettes et prototype

Les maquettes haute fidélité et le prototype cliquable sont produits avec **Claude Design**, qui héberge à la fois le design system (tokens, composants, guidelines) et le kit d'écrans de l'application web.

> Lien du prototype : à renseigner

Le prototype couvre dix écrans, chacun déclinable en plusieurs états atteignables sans modifier le code, en deux formats : desktop 1280 px et mobile 390 px.

| Écran                        | États couverts                                                                                      |
| ---------------------------- | --------------------------------------------------------------------------------------------------- |
| Accueil                      | nominal connecté, visiteur, liste vide, erreur de chargement, sans vitrine tournoi                  |
| Détail d'un créneau          | non inscrit, déjà inscrit, complet, annulé, réinscription                                           |
| Page équipe                  | capitaine, membre, équipe complète                                                                  |
| Création d'un créneau        | nominal, erreurs de validation, chevauchement, super organisateur                                   |
| Tableau de bord organisateur | nominal, aucun inscrit, créneau annulé                                                              |
| Authentification             | connexion, inscription, erreur, compte non vérifié, blocage rate limit                              |
| Centres                      | visiteur, non affilié, demande en attente, affilié, demande refusée                                 |
| Console admin, comptes       | nominal, recherche sans résultat, retrait du dernier rôle, compte suspendu, auto-suspension bloquée |
| Console admin, centres       | nominal, sans rôle organisateur, affectation existante, centre inactif, centre orphelin             |
| Console admin, vitrine       | une fiche en vitrine, aucune fiche, erreurs de validation, vitrine désactivée                       |

Les wireframes qui ont servi de base sont versionnés dans `diagrams/wireframes/`. Le benchmark visuel et le moodboard qui ont orienté ces choix sont documentés dans `benchmark.md` et `moodboard.md`.

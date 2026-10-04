---
name: admin-shell
description: "Shell d'application back-office : barre haute fixe (notifications + profil) fusionnée sans couture avec un menu latéral rétractable, en-tête et pied de page figés, coin de contenu arrondi structurellement stable, état du menu mémorisé. Use when building or refactoring an admin app shell, merged sidebar and topbar, collapsible navigation drawer, sticky sidebar header and footer, rounded content panel corner, notification bell dropdown, profile menu, or when the user says shell admin, topbar notifications, menu lateral fusionne, sidebar retractable, layout back-office ou dashboard admin."
---

# Shell d'admin — topbar + menu latéral fusionnés

## Overview
Reproduit dans une back-office **existante** un shell d'application : une barre haute
fixe (notifications, profil) **fusionnée visuellement** avec un menu latéral rétractable,
un en-tête figé, un pied de page figé, et un coin de contenu arrondi structurellement
stable. On ne touche qu'au **layout shell** — jamais à la logique métier.

Domaine voisin : `backoffice-design` (principes admin panels, RBAC, tableaux, états).

## 0. Adaptation (obligatoire)
- Adapter tout le code à la **stack réelle** : framework / CSS utility ou CSS pur /
  bibliothèque d'icônes / bibliothèque de composants. Les classes Tailwind d'exemple
  sont **indicatives**.
- Adapter les couleurs à la **charte du projet** : couleur primaire foncée + chemin du
  logo. Ne jamais durcir `#013f44` comme valeur universelle.
- **Ne pas toucher à la logique métier existante** : uniquement sidebar + topbar +
  zone de contenu + footer.
- Avant d'écrire, lire les composants existants pour reprendre leurs conventions de
  nommage, d'imports et de styling.

## 1. Le shell fusionné (le point clé)
- Sidebar et topbar partagent **exactement la même couleur de fond** (teinte primaire
  foncée) : zéro couture visible, zéro dégradé, zéro ombre interne entre les deux —
  elles forment **une seule masse visuelle**.
- Bandeau haut de **64 px** (`h-16`) des deux côtés : en-tête de sidebar et topbar
  s'alignent au pixel, aucun décalage vertical.
- Shell à angles vifs ; **seul le panneau de contenu** porte un coin arrondi.
- Absence totale de bordures/coutures entre sidebar et topbar.

## 2. Menu latéral (sidebar)

### 2.1 En-tête (figé en haut)
- Carte blanche arrondie (`rounded-xl`, h ≈ 48 px, px-4) contenant le **logo complet**
  (image, h-8) — pas de nom en texte à côté.
- Juste à côté : **bouton de réduction** (chevron), cercle semi-transparent
  `bg-white/10` / hover `bg-white/20`, `aria-label="Réduire le menu"` /
  `"Étendre le menu"`.
- En-tête **collé en haut** pendant que la liste de nav défile dessous.

### 2.2 Navigation (défilable)
- Items = **icône vectorielle + libellé** (outline, trait constant 1,5–2 px,
  **jamais d'emoji**).
- États : actif `bg-white/15` ; inactif `text-white/70` ; hover `bg-white/10`.
  Item actif aussi marqué `aria-current="page"`.
- Scrollbar fine : **8 px**, `scrollbar-width: thin`, pouce translucide (blanc ~30 %
  sur sidebar, gris ardoise ~35 % dans le contenu), **flèches masquées**
  (`::-webkit-scrollbar-button`).
- Libellés animés (`opacity` + `width`) pour que le mode réduit ne casse pas la mise
  en page.

### 2.3 Pied de page (figé en bas)
- `sticky bottom`, séparé du reste par une fine ligne `border-white/10`.
- **Carte utilisateur** : avatar (initiales ou photo), nom, rôle — `bg-white/10`,
  arrondie.
- Sous la carte, deux liens discrets : **« Mon compte »** et **« Déconnexion »**
  (logout = confirmation puis redirection login).

### 2.4 États réduit / déplié
- **Par défaut : RÉDUIT** (icônes seules, 64 px, libellés masqués).
- Transition **300 ms** en `transform`/`width` — pas de saut de layout.
- État **mémorisé dans `localStorage`** (clé type `app-sidebar-collapsed`), rechargé à
  chaque visite, **synchronisé entre onglets** (écoute de l'événement `storage`).
  Côté React : préférer **`useSyncExternalStore`** (état serveur = état client → pas de
  mismatch d'hydratation, **pas de `setState` dans un `useEffect`**).
- Mode réduit : icônes seules centrées ; libellé conservé via `title` + `aria-label`.
- Les règles visuelles du mode réduit (libellés, paddings, marge du contenu) bornées à
  **`@media (min-width: 1024px)`** : sur mobile elles ne s'appliquent **jamais**
  (sinon le tiroir perd ses libellés).

### 2.5 Logo blanc « en coulisse »
- Menu réduit ⇒ **logo complet en blanc** glisse dans la topbar à gauche : image avec
  `filter: brightness(0) invert(1)`, états
  `max-width:0; opacity:0; translate(-16px,0)` ↔
  `max-width:192px; opacity:1; translate(0,0)`,
  `transition-all 300 ms` **parfaitement synchrone** avec l'état du menu.
- Masqué sur mobile, `aria-hidden="true"` (image décorative).

## 3. Topbar (barre haute, fixe)
- `sticky top-0`, **hauteur 64 px**, **même fond foncé que la sidebar**, bordure basse
  `border-white/10` seulement si le fond de contenu est clair.
- **Gauche** : bouton burger (uniquement mobile, ouvre le tiroir) + logo blanc
  coulissant (§ 2.5).
- **Droite** :
  1. **Notifications** : cloche + **badge rouge** compteur (`bg-red-500`),
     `aria-label="Notifications (N non lues)"`. Clic → dropdown (~380 px, coin arrondi,
     ombre, `max-h` + scroll interne) : titre, message, date relative, état lu/non-lu,
     lien « Tout marquer comme lu », **état vide géré**.
  2. **Profil** : avatar + nom + rôle (masqués en compact) → dropdown : en-tête
     identité, « Mon compte », « Déconnexion ».
- Les deux dropdowns : ouverture au clic, fermeture au **clic extérieur** + **`Escape`**,
  **focus piégé** pendant l'ouverture, `aria-expanded` / `aria-haspopup`.
- **Z-index maîtrisé** : notifications > profil > contenu > sidebar mobile.

## 4. Panneau de contenu
- Hauteur exacte **`calc(100vh - 64px)`** (ou `100dvh`), **défilement interne** :
  `overflow-y: auto` sur le panneau, le `document` ne défile pas.
- Fond clair (canvas), **coin supérieur gauche arrondi 16 px** (`rounded-tl-2xl`),
  ombre légère (`shadow-sm`) : le coin reste **structurellement figé** au scroll —
  c'est cette astuce qui donne la finition (pas d'overlay positionné).
- Marge gauche = largeur courante de la sidebar (transition synchronisée).
- **Scroll remis à zéro à chaque changement de route/page.**
- `@media print` : `height: auto; overflow: visible` sur le panneau (sinon l'impression
  coupe après un écran).

## 5. Comportement responsive
- **≥ 1024 px** : sidebar fixe (64 px réduite / 280 px dépliée), libellés OK.
- **< 1024 px** : sidebar = **tiroir** de 280 px superposé, libellés **toujours visibles**
  (jamais le mode réduit), scrim semi-transparent derrière, fermeture au clic sur le
  scrim, sur un item de nav, et au **`Escape`**. Burger visible dans la topbar.
- Cibles tactiles **≥ 44 px**, padding généreux.

## 6. Icônes & accessibilité
- **Une seule famille** d'icônes vectorielles, un seul style (outline), tailles
  tokenisées (20 px nav, 22–24 px topbar), `aria-hidden="true"` si décorative.
- **Aucun emoji** comme icône structurelle.
- Navigation clavier complète : Tab logique, `:focus-visible` visible (anneau blanc sur
  fond sombre), Enter/Space activent les items.
- Contraste : texte principal sur fond foncé **≥ 4,5:1** ; états actif/hover distincts
  aussi **sans couleur**.
- Tous les boutons icônes ont un `aria-label`.

## 7. Critères d'acceptation
- [ ] Aucune couture visible entre sidebar et topbar (même couleur, même hauteur 64 px).
- [ ] Menu réduit au premier chargement ; l'état survit au rechargement et se
      synchronise entre onglets.
- [ ] En-tête et pied de page de sidebar figés pendant que la nav défile.
- [ ] Coin supérieur gauche du contenu arrondi, stable au scroll, sans flip ni décalage.
- [ ] Logo blanc glisse dans la topbar en 300 ms, exactement synchro du collapse.
- [ ] Dropdowns notifications et profil : ouverture/fermeture propres (clic extérieur,
      Escape), badge de compteur correct.
- [ ] Mobile : tiroir plein avec libellés, scrim, fermeture propre.
- [ ] 0 erreur console / 0 mismatch d'hydratation.
- [ ] Imprimable sans troncature.

## Livrable attendu
Du code **intégré à l'arborescence existante**, composants découpés — pas un snippet
isolé :
- `Sidebar` (en-tête + nav scrollable + pied de page figé)
- `TopBar` (burger/logo coulissant + notifications + profil)
- `ContentPanel` (panneau à coin arrondi, scroll interne, reset au route change)
- **contexte d'état** partagé (collapse, tiroir mobile, ouverture des dropdowns)

Vérifier les critères §7 avant de rendre le code (test visuel local, console vide).

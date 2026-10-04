---
name: motion-design-web
description: "Direction artistique et principes du motion design pour le web. Micro-interactions, choreography, easing, timing, transitions de page, reveal patterns, Lottie, page loaders. Use when designing animation systems, choosing easing/timing, choreographing page transitions, adding micro-interactions, or reviewing motion quality in websites and apps."
version: 1.0.0
tags: [motion-design, animation, easing, micro-interactions, choreography, transitions]
---

# Motion Design Web

Principes et direction artistique du motion design pour sites web et produits numériques. Définit le *pourquoi* et le *comment ressent* une animation ; `gsap-*` définit l'implémentation.

## When to Use

- Définir un système d'animation / motion tokens pour un produit
- Choisir easing, durée, stagger et choreography
- Concevoir micro-interactions, transitions de page, reveals
- Évaluer la qualité d'animation existante
- Décider entre CSS, GSAP, Lottie ou WASM

## Les 6 Principes Fondamentaux

| Principe | Règle |
|---|---|
| **Purpose** | Chaque animation communique une relation causale ou spatiale |
| **Continuity** | L'objet qui entre vient d'un endroit logique |
| **Hierarchy** | L'élément le plus important bouge en premier ou le plus fort |
| **Timing** | La durée reflète la distance et l'importance |
| **Easing** | Le mouvement naturel n'est jamais linéaire |
| **Restraint** | Le silence est une animation — ne rien bouger est un choix valide |

## Motion Tokens

Systématiser plutôt que décider au cas par cas :

```css
:root {
  /* Duration */
  --motion-instant: 80ms;    /* toggle, press feedback */
  --motion-fast: 150ms;      /* hover, focus */
  --motion-base: 250ms;      /* dropdown, tooltip, tab */
  --motion-slow: 400ms;      /* modal, drawer, page */
  --motion-slower: 600ms;    /* hero, section reveal */

  /* Easing */
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);        /* entrances */
  --ease-in: cubic-bezier(0.7, 0, 0.84, 0);         /* exits */
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);    /* movement */
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1); /* playful, small elements */
}
```

**Règle :** les sorties sont toujours plus rapides que les entrées (~0.6×) pour un effet de réactivité.

## Guide d'Easing

| Easing | Sensation | Usage |
|---|---|---|
| `ease-out` | Réactif, net | Entrées, reveals, hover |
| `ease-in-out` | Fluide, naturel | Déplacement, resize |
| `ease-in` | Départ, fuite | Sorties, dismiss |
| Spring overshoot | Jouant, énergique | Toggles, FAB, micro-feedback |
| Linear | Mécanique, robot | Barres de progression, loaders uniquement |

**Anti-pattern :** easing linéaire sur un déplacement d'objet.

## Durées par Type d'Interaction

| Type | Durée | Pourquoi |
|---|---|---|
| Hover / focus | 100–150ms | Doit sentir instantané |
| Press / toggle | 80–120ms | Feedback immédiat |
| Dropdown / tooltip | 150–250ms | Assez pour comprendre |
| Modal / drawer | 250–400ms | C'est un changement de contexte |
| Page transition | 300–500ms | Jamais plus, sinon frustration |
| Section reveal | 400–700ms | Narratif |
| Compteur / number | 600–1200ms | Doit être lisible |

**Règle d'or :** si l'utilisateur doit *attendre* l'animation, < 300ms.

## Choreography (Stagger)

Ordonner les entrées pour guider l'œil :

| Pattern | Ordre | Usage |
|---|---|---|
| **Natural** | Haut → bas, gauche → droite | Listes, grilles, formulaires |
| **Reverse** | Bas → haut | Révélation dramatique |
| **Center-out** | Du centre vers l'extérieur | Hero, logos, badges |
| **Priority** | Important d'abord | Dashboards, cards |
| **Cascade** | Courte décalée (30–60ms) | Cartes, tags, mots |

**Stagger type :** 30–50ms entre éléments proches, 60–100ms pour des blocs distincts. Au-delà de 8 éléments, réduire le stagger ou animer par batch.

## Micro-Interactions

Structure canonique :

```text
Trigger (hover/click/state)
  -> Response (< 150ms)
  -> Motion (transform/opacity only)
  -> Settle (return or confirm)
```

Cas types :

- **Like / favorite** — scale overshoot 1 → 1.3 → 1 + particle optionnelle
- **Toggle** — knob translate + track color, 150ms ease-out
- **Add to cart** — badge count pop + icon fly-to-cart
- **Save state** — label morph "Save" → "Saved" + check draw
- **Loading** — skeleton shimmer > spinner (perception de rapidité)

Règle : toujours prévoir l'état `pressed` et l'état de confirmation.

## Reveal Patterns

| Pattern | Description | Efficace pour |
|---|---|---|
| Fade up | opacity 0→1 + translateY 16→0 | Sections, cards (par défaut) |
| Mask reveal | clip-path ou overflow hidden | Titres, images |
| Line draw | stroke-dashoffset SVG | Icônes, illustrations |
| Scale in | scale 0.96→1 + fade | Modals, toasts |
| Blur in | filter blur→0 | Images, hero premium |
| Text cascade | mot ou lettre décalé | Headlines marketing |
| Counter | number 0→value | KPIs, statistiques |

**Fade up reste le pattern par défaut sûr** — les autres sont des accents.

## Transitions de Page

```text
Exit current (150-200ms, ease-in)
  -> Overlap optional (crossfade 100ms)
  -> Enter next (250-400ms, ease-out)
```

Règles :
- Ne jamais bloquer la navigation plus de 300ms
- Préserver la position de scroll ou la restaurer explicitement
- Animer un conteneur, pas chaque enfant
- Le contenu critique (titre) apparaît en premier

## Choix de l'Outil

| Besoin | Outil |
|---|---|
| Hover, transitions simples, states | **CSS transitions** |
| Sequences, scroll, contrôle précis | **GSAP** (voir `gsap-*`) |
| Timeline complexe éditable | GSAP |
| Animation vectorielle exportée depuis After Effects | **Lottie** |
| Hero 3D | **Three.js / R3F** (voir `threejs-webgl`) |
| Path morphing / physics | GSAP plugins |
| Micro-interactions légères | CSS d'abord, GSAP si orchestré |

**Ordre de décision :** CSS → GSAP → Lottie/WASM. Ne pas introduire une librairie pour une transition de 150ms.

## Accessibilité Motion

Obligatoire :

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

- L'information ne doit jamais dépendre du mouvement
- Le contenu doit être lisible même sans animation (état initial = contenu visible)
- Pas d'autoplay infini sur élément porteur d'information
- Éviter les parallax forts (malaise vestibulaire)
- Proposer un toggle "Reduce motion" dans les settings si la 3D/animation est centrale

## Performance

| Faire | Éviter |
|---|---|
| Animer `transform` et `opacity` | `width`, `height`, `top`, `left` |
| `transform: translate3d()` pour forcer GPU | Animer `box-shadow` lourd |
| `will-change` temporairement | `will-change` permanent |
| Uniquement des éléments visibles | Animer hors écran |
| `stagger` par batch pour >20 éléments | 200 tweens simultanés |

## Anti-Patterns

- Animation sans but fonctionnel ("faire joli")
- Entrées lentes > 600ms sur du contenu critique
- Stagger trop long : la page semble cassée
- Bloquer le scroll pendant une animation
- Bouncing/excess de spring sur un produit sérieux
- Animations qui déclenchent des reflows (layout thrashing)
- Oublier l'état `reduced-motion`
- Faire attendre le CTA principal

## Review Checklist

- [ ] Motion tokens définis (duration + easing)
- [ ] Easing non-linéaire sur tous les mouvements d'objets
- [ ] Sorties plus rapides que les entrées
- [ ] Stagger < 100ms, total < 600ms
- [ ] Aucun layout property animé
- [ ] `prefers-reduced-motion` respecté
- [ ] Contenu visible avant animation (no-JS friendly)
- [ ] Micro-interactions ont feedback + confirmation
- [ ] Testé sur device faible (pas de jank)
- [ ] Motion cohérent entre écrans (même token = même ressenti)

## References

- GSAP Docs: https://gsap.com/docs/v3/
- Material Motion: https://m3.material.io/styles/motion/overview
- easing cheat sheet: https://easing.net
- Motion principles (Val Head): https://abookapart.com/products/designing-interface-animation

## Integration

- `gsap-core`, `gsap-timeline` → implémentation des tweens
- `gsap-scrolltrigger` → reveals au scroll
- `gsap-performance` → garanties de perf
- `saas-ui-design` / `3d-web-design` → contexte produit

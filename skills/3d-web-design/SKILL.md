---
name: 3d-web-design
description: "Direction artistique et UX pour sites web 3D, immersifs et interactifs. Use when designing 3D landing pages, product experiences, scroll-driven scenes, interactive heroes, or web experiences combining 3D and 2D UI."
version: 1.0.0
tags: [3d, web-design, immersive, interactive, ux, storytelling]
---

# 3D Web Design

Concevoir des expériences web 3D utiles, lisibles et performantes. La 3D doit clarifier le produit ou créer une émotion contrôlée, jamais masquer le contenu principal.

## When to Use

- Site marketing avec hero 3D
- Présentation interactive d'un produit ou d'un objet
- Scroll storytelling avec caméra ou scènes successives
- Portfolio, événement, expérience immersive ou showroom
- Interface combinant canvas 3D et contenu HTML

## Direction Artistique

Choisir une seule direction dominante :

| Direction | Usage | Risque à contrôler |
|---|---|---|
| Abstract / generative | Tech, AI, infrastructure | Visuel générique |
| Product showcase | Produit physique ou 3D | Temps de chargement |
| Editorial 3D | Marque premium, campagne | Lisibilité du contenu |
| Playful low-poly | Éducation, culture, communauté | Manque de sérieux |
| Spatial / architectural | Immobilier, spatial, data | Navigation complexe |

## Composition de Page

```text
Header HTML accessible
  -> Hero: promesse + CTA + scène 3D secondaire
  -> Preuve ou bénéfice lisible sans 3D
  -> Section interactive progressive
  -> Démonstration produit / données
  -> Fallback statique et CTA final
```

## Règles UX

- Le titre, la proposition de valeur et le CTA restent en HTML.
- La scène 3D ne doit pas empêcher la lecture ni le scroll naturel.
- Prévoir une animation d'entrée courte et une interaction explicitement indiquée.
- Ne pas utiliser le mouvement comme seul signal d'information.
- Prévoir un état de chargement, un état d'erreur et un fallback sans WebGL.
- Sur mobile, réduire la scène plutôt que simplement la mettre à l'échelle.
- Respecter `prefers-reduced-motion` et proposer une expérience statique équivalente.
- Les contrôles doivent être compréhensibles au clavier et au toucher.

## Interaction Patterns

| Pattern | Bon usage |
|---|---|
| Pointer parallax | Accent visuel léger dans un hero |
| Scroll camera | Raconter une séquence en étapes |
| Drag / orbit | Examiner un produit ou un modèle |
| Hotspots | Révéler des détails produit |
| Morphing | Montrer une transformation compréhensible |
| Particle field | Atmosphère, jamais contenu essentiel |

## Anti-Patterns

- Canvas plein écran sans message, CTA ou structure sémantique
- Animation continue lourde sans pause
- Intro obligatoire avant d'accéder au contenu
- Texte rendu uniquement dans WebGL
- Contrôles cachés ou dépendants uniquement de la souris
- Modèle 3D détaillé alors qu'une image optimisée suffit
- Scroll hijacking qui casse le comportement attendu du navigateur

## Design Handoff

Documenter avant l'implémentation :

- rôle de la 3D dans chaque section
- caméra initiale et limites de mouvement
- états : loading, ready, hover, active, error, reduced motion
- budget de poids des assets
- fallback mobile et fallback sans WebGL
- labels et descriptions accessibles

## Delivery Checklist

- [ ] Le contenu essentiel reste disponible sans WebGL
- [ ] Le CTA principal est visible immédiatement
- [ ] La scène a une fonction UX claire
- [ ] Le scroll reste natif et prévisible
- [ ] Les interactions sont possibles au clavier et au touch
- [ ] Une variante mobile est définie
- [ ] `prefers-reduced-motion` est pris en compte
- [ ] Loading, erreur et fallback sont conçus

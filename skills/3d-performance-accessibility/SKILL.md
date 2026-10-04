---
name: 3d-performance-accessibility
description: "Performance, robustesse et accessibilité pour expériences web 3D et WebGL. Use when reviewing, optimizing, or shipping 3D websites, Three.js scenes, React Three Fiber canvases, animations, assets, or WebGL fallbacks."
version: 1.0.0
tags: [3d, performance, accessibility, webgl, mobile, core-web-vitals]
---

# 3D Performance and Accessibility

Rendre une expérience 3D rapide, tolérante aux appareils faibles et utilisable sans souris, sans mouvement et sans WebGL.

## Performance Budget

Définir un budget avant de produire les assets :

| Élément | Règle de départ |
|---|---|
| JavaScript initial | Garder la 3D hors du chemin critique si possible |
| Modèle principal | Préférer un GLB compressé et version mobile |
| Textures | Résolution adaptée à la taille visible, KTX2/Basis si possible |
| Draw calls | Réduire le nombre de matériaux et objets rendus séparément |
| DPR | Plafonner et adapter selon l'appareil |
| Animation | Désactiver ou réduire hors écran |

Ces valeurs sont des points de départ, pas des garanties. Mesurer sur appareils réels.

## Asset Optimization

- Utiliser GLB/GLTF plutôt que des formats de scène lourds côté client.
- Compresser la géométrie avec Draco ou Meshopt lorsque le pipeline le permet.
- Compresser les textures avec KTX2/Basis et éviter les textures surdimensionnées.
- Réduire les matériaux, variantes, transparences et ombres dynamiques.
- Créer un niveau de détail mobile ou un modèle simplifié.
- Charger les assets par section ou interaction, pas toute la scène au démarrage.
- Précharger uniquement l'asset nécessaire au premier écran.

## Runtime Optimization

- Suspendre le rendu lorsque la scène est hors écran ou masquée.
- Utiliser `IntersectionObserver` pour les sections 3D non visibles.
- Limiter `devicePixelRatio` et ajuster la qualité après mesure.
- Utiliser instancing pour les objets répétés.
- Réutiliser géométries, matériaux et textures.
- Éviter les allocations dans `useFrame` ou dans la boucle d'animation.
- Utiliser des workers lorsque la préparation d'assets est coûteuse.
- Mesurer FPS, frame time, memory, draw calls et taille des assets.

## Loading Strategy

```text
HTML critique rendu immédiatement
  -> placeholder ou poster léger
  -> import dynamique du runtime 3D
  -> preload de l'asset principal
  -> scène interactive
  -> assets secondaires à la demande
```

Le contenu et le CTA ne doivent pas attendre l'initialisation de WebGL.

## Accessibility

- Garder tout le contenu sémantique en HTML.
- Fournir un nom accessible et une description pour les scènes informatives.
- Ne pas transmettre une information par la couleur ou le mouvement uniquement.
- Offrir des boutons ou liens HTML pour chaque action importante.
- Rendre les hotspots atteignables au clavier avec un focus visible.
- Fournir une alternative textuelle ou une image statique pour un modèle informatif.
- Respecter `prefers-reduced-motion` en supprimant rotation, parallax et transitions non nécessaires.
- Respecter le contraste WCAG pour les overlays HTML sur la scène.
- Ne pas capturer le focus clavier dans un canvas non contrôlable.
- Ne pas utiliser le canvas comme seul moyen de navigation.

## Fallback Matrix

| Situation | Fallback |
|---|---|
| WebGL indisponible | Image/poster + contenu HTML complet |
| Appareil faible | Modèle simplifié, effets réduits, pas de post-processing |
| Connexion lente | Poster immédiat, chargement différé |
| Reduced motion | Scène fixe ou transitions instantanées |
| Touch only | Contrôles tactiles explicites, pas de hover requis |
| Erreur asset | Message compréhensible + CTA HTML |

## Testing Matrix

- Chrome, Safari et Firefox
- Mobile iOS et Android
- Appareil récent et appareil faible
- WebGL désactivé ou indisponible
- `prefers-reduced-motion: reduce`
- Navigation clavier uniquement
- Zoom texte et viewport étroit
- Connexion lente ou offline après chargement initial
- Portrait et paysage

## Review Checklist

- [ ] Le LCP ne dépend pas de la scène 3D
- [ ] Le runtime 3D est chargé dynamiquement si pertinent
- [ ] Les modèles et textures sont compressés
- [ ] Le DPR est plafonné
- [ ] Les draw calls et frame times sont mesurés
- [ ] Les scènes hors écran sont suspendues ou simplifiées
- [ ] Le site reste utilisable sans WebGL
- [ ] Le mode reduced motion fonctionne réellement
- [ ] Les actions ont des alternatives HTML/clavier
- [ ] Les overlays respectent le contraste et le focus

## Useful Tools

- Chrome DevTools Performance et WebGL Insights
- Lighthouse et WebPageTest
- Spector.js pour inspecter les appels WebGL
- `@react-three/test-renderer` pour tests ciblés R3F
- Three.js renderer info pour suivre triangles, textures et draw calls

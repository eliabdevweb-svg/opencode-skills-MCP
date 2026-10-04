---
name: threejs-webgl
description: "Implémentation de scènes 3D web avec Three.js, React Three Fiber, Drei, WebGL et GLTF. Use when building interactive 3D websites, loading models, creating shaders, camera animations, particles, post-processing, or canvas-based product experiences."
version: 1.0.0
tags: [threejs, webgl, react-three-fiber, r3f, drei, gltf, shaders]
---

# Three.js and WebGL

Construire des scènes 3D web maintenables avec Three.js ou React Three Fiber. Préférer les primitives et composants existants avant d'écrire un shader ou un renderer personnalisé.

## Stack Selection

| Contexte | Choix |
|---|---|
| JavaScript vanilla | Three.js |
| React / Next.js | React Three Fiber + Drei |
| Interaction éditoriale rapide | Spline export/embed, avec fallback HTML |
| Modèles | GLTF/GLB |
| Compression | Draco pour géométrie, KTX2/Basis pour textures |
| Effets | Postprocessing ciblé, pas de chaîne excessive |

## Scene Architecture

```text
Scene
  -> Camera
  -> Lights / Environment
  -> Content group
      -> Model
      -> Interactive hotspots
  -> Effects (optionnel)
  -> HTML overlay accessible
```

Séparer les responsabilités :

- `SceneCanvas` : renderer, DPR, resize et contexte
- `SceneContent` : objets et matériaux
- `CameraRig` : position et transitions de caméra
- `InteractionLayer` : raycasting, hover, click et touch
- `LoadingState` : progression et erreurs
- HTML : titres, actions, descriptions et navigation

## React Three Fiber Pattern

```tsx
function ProductScene() {
  return (
    <Canvas camera={{ position: [0, 0, 5], fov: 35 }} dpr={[1, 2]}>
      <Suspense fallback={null}>
        <Environment preset="studio" />
        <ProductModel />
      </Suspense>
    </Canvas>
  )
}
```

Règles :

- Charger les modèles avec `useGLTF` et précharger uniquement les assets nécessaires.
- Utiliser `useFrame` uniquement pour la boucle réellement animée.
- Ne pas recréer géométries, matériaux ou textures à chaque rendu React.
- Utiliser `useLoader`/`Suspense` avec une UI de chargement externe au canvas.
- Nettoyer les ressources créées dynamiquement lors du démontage.
- Maintenir la logique métier et les données hors de la scène quand c'est possible.

## Model Pipeline

```text
Blender / Spline
  -> GLB
  -> Draco / KTX2 compression
  -> preload selectively
  -> GLTFLoader / useGLTF
  -> animation mixer / interaction
```

Avant livraison : vérifier les unités, l'origine, les normales, les animations, les textures et le nombre de matériaux.

## Interaction

- Utiliser le raycasting pour cibler un objet précis.
- Ajouter un état visuel stable pour hover, focus, sélection et désactivation.
- Supporter pointer, clavier et touch lorsque l'action est importante.
- Ne pas dépendre de `mousemove` continu pour une action essentielle.
- Limiter les interactions simultanées afin d'éviter les conflits orbit/scroll.
- Exposer les actions importantes dans l'interface HTML parallèle.

## Animation

- Préférer des transitions courtes, cohérentes et interruptibles.
- Utiliser une timeline pour le storytelling et un état explicite pour les étapes.
- Éviter les animations infinies sur mobile ou en mode réduit.
- Ne pas bloquer l'interface pendant une transition de caméra.
- Synchroniser le texte HTML avec la scène sans faire dépendre le contenu de WebGL.

## Shaders and Effects

Utiliser des shaders uniquement lorsqu'ils apportent une valeur visuelle réelle. Commencer par `MeshStandardMaterial`, `MeshPhysicalMaterial`, textures et lighting.

- Garder les uniforms documentés et typés.
- Prévoir un fallback lorsque WebGL ou une extension n'est pas disponible.
- Mesurer le coût du bloom, SSAO, DOF et des effets plein écran avant de les cumuler.
- Éviter les effets qui réduisent le contraste du texte ou masquent les contrôles.

## Responsive Canvas

- Adapter caméra, framing et densité d'objets aux breakpoints.
- Régler le DPR avec une limite raisonnable, jamais uniquement sur la résolution écran.
- Réduire lumières, ombres, particules et post-processing sur appareils faibles.
- Prévoir une image ou une scène simplifiée pour l'absence de WebGL.
- Tester portrait, paysage, clavier, touch et fenêtres redimensionnées.

## Implementation Checklist

- [ ] Canvas isolé dans un composant dédié
- [ ] Assets GLB compressés et chargés à la demande
- [ ] Loading, erreur et fallback HTML implémentés
- [ ] Interaction clavier/touch pour les actions importantes
- [ ] Animations interruptibles et mode réduit
- [ ] Matériaux et ressources réutilisés
- [ ] DPR et qualité adaptés à l'appareil
- [ ] Aucun texte essentiel rendu uniquement dans le canvas

## References

- Three.js: https://threejs.org/docs/
- React Three Fiber: https://r3f.docs.pmnd.rs/
- Drei: https://github.com/pmndrs/drei
- Spline: https://docs.spline.design/

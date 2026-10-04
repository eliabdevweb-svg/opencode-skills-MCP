# Règles globales — routage des skills et MCP

Ces règles s'appliquent à **toutes** les sessions. Elles servent à ce que l'agent charge
automatiquement le bon skill et le bon outil MCP sans avoir à être guidé.

## Règle n°1 — charger le skill AVANT de travailler

Avant toute tâche de design, de dev ou d'audit, **consulte la liste `<available_skills>`**
fournie par l'outil `skill` et **charge le skill correspondant en premier**, puis suis son
`SKILL.md`. Ne commence jamais un travail concernant un skill sans l'avoir chargé.

## Table de routage — famille Design

| Besoin | Skill |
|---|---|
| **Toute décision d'interface** (mauvais choix par défaut) | `ui-ux-pro-max` |
| Livrable visuel concret : logo, mockup, CIP, icône, image social | `design` |
| Code de style : CSS, Tailwind, shadcn, thème, dark mode | `ui-styling` |
| Une seule bannière / hero / visuel pub | `banner-design` |
| Identité de marque, ton de voix, charte, guidelines | `brand` |
| Design tokens, specs composants, échelles typographiques | `design-system` |
| Design system fondé sur shadcn / Radix | `design-system-shadcn` |
| Interfaces SaaS (onboarding, dashboards, palettes) | `saas-ui-design` |
| Design system SaaS multi-tenant | `saas-design-system` |
| Back-office, admin panel, dashboard interne | `backoffice-design` |
| Shell d'admin : topbar + menu latéral fusionnés, sidebar rétractable, coin de contenu arrondi | `admin-shell` |
| Formulaires, validation, wizards | `form-design` |
| Tableaux de données (tri, filtre, pagination) | `data-tables` |
| Accessibilité WCAG 2.1 AA, ARIA, navigation clavier | `accessibility` |
| Présentations HTML / slides | `slides` (data) ou `design` (habillage) |
| Bannières réseaux sociaux / print / ads | `banner-design` |

**Arbitrage en cas de chevauchement** : `ui-ux-pro-max` gagne pour tout ce qui est
**interface et UX** ; `design` gagne pour tout ce qui est **asset graphique produit** ;
`ui-styling` gagne dès qu'il s'agit d'**écrire du CSS** ; `admin-shell` gagne pour le
**layout shell** (sidebar + topbar + panneau de contenu) et `backoffice-design` pour le
**reste de la back-office** (RBAC, tableaux, états, hiérarchie).

## Table de routage — 3D et animation

| Besoin | Skill |
|---|---|
| Direction artistique / UX d'une expérience 3D | `3d-web-design` |
| Implémentation Three.js, R3F, Drei, GLTF, shaders | `threejs-webgl` |
| Performance, robustesse et a11y d'une scène 3D/WebGL | `3d-performance-accessibility` |
| Direction artistique du motion, easing, choreography | `motion-design-web` |
| GSAP — base (`gsap.to`, easing, stagger, matchMedia) | `gsap-core` |
| GSAP + React / Next (`useGSAP`, `gsap.context`, cleanup) | `gsap-react` |
| GSAP + Vue / Svelte / Nuxt | `gsap-frameworks` |
| ScrollTrigger, pinning, scrub, parallax | `gsap-scrolltrigger` |
| Séquencer une animation (`gsap.timeline`) | `gsap-timeline` |
| Plugins GSAP (ScrollTo, Flip, Draggable, SplitText…) | `gsap-plugins` |
| `gsap.utils` (clamp, mapRange, toArray, random…) | `gsap-utils` |
| Performance GSAP (transforms, will-change, batching) | `gsap-performance` |

## Table de routage — développement

| Besoin | Skill |
|---|---|
| Architecture logicielle, patterns, SOLID, microservices | `architecture` |
| Stratégie de tests, TDD, unitaires / E2E / couverture | `testing` |
| Sécurité applicative, OWASP, secrets, auth | `security` |
| Performance web, Core Web Vitals, bundle, cache | `performance` |
| CI/CD, GitHub Actions, pipelines, releases | `ci-cd-automation` |
| Workflow Git, branches, commits, PR | `git-workflow` |
| Conventions de code, clean code, linting | `coding-standards` |
| Monitoring, logs, métriques, traces, alerting | `monitoring-observability` |
| Documentation technique, README, docs API | `documentation` |
| Design d'API REST / GraphQL | `api-design` |
| Conception / optimisation de bases de données | `database` |
| Choix d'un state management (Redux, Zustand, Context…) | `state-management` |
| Design system SaaS générique | `saas-design-system` |
| Architecture de systèmes SaaS (2026) | `saas-system-design` |
| **Affirmer un fait, citer une source, chiffre, date ou version** | `anti-hallucination` |
| **Contexte qui gonfle, coût / nombre de tokens, compaction, mémoire d'agent** | `context-engineering` |

> **Avant toute affirmation factuelle** (nom propre, chiffre, date, numéro de version,
> statistique), charger `anti-hallucination`. Source : guide officiel Anthropic
> *Reduce hallucinations*. Règle : si la source n'est pas vérifiable, **ne pas citer**.

## Outils MCP — quand utiliser quoi

| Outil | Usage |
|---|---|
| `playwright` (`browser_*`) | **Tout test visuel ou interactif** : navigation, screenshot, snapshot d'accessibilité, console, réseau |
| `sequential-thinking` | Raisonnement complexe multi-étapes, architecture, arbitrage |
| `context7` | **Documentation à jour et versionnée** — obligatoire avant d'écrire un code dépendant d'une librairie. Supprime les APIs hallucinées. Invoquer avec *« use context7 »* |
| `exa` | Recherche web (recherches récentes, documentation) |
| `duckduckgo` | Recherche web **de secours** — index indépendant, sans quota, si `exa` est limité |

### Protocole de test visuel

1. Ouvre la page avec `browser_navigate` (`file:///...` pour le local).
2. Prends un `browser_take_screenshot` **après** chargement.
3. Prends un `browser_snapshot` pour vérifier le texte rendu.
4. Vérifie `browser_console_messages` (niveau `error`) — **0 erreur attendue**.
5. Au besoin, `browser_resize` pour tester le responsive.

Pour du headless Chrome sans Playwright, utiliser :
`--headless=new --virtual-time-budget=6000 --run-all-compositor-stages-before-draw`

## Langue

Répondre en **français** par défaut, sauf demande contraire. Les noms de skills, de
fichiers et de commandes restent en anglais.

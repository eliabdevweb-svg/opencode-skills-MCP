# OpenCode Skills & MCP

**39 skills professionnels + configurations MCP** pour [opencode](https://opencode.ai),
**Claude Code** et **Claude Desktop**.

Ce dépôt est une boîte à outils prête à l'emploi : des skills bien documentés, des
configurations MCP testées, et surtout des **procédures d'installation qui évitent les
conflits lors des mises à jour**.

> **Principe directeur** : le dépôt se clone **toujours** à part, dans son propre dossier,
> **jamais à l'intérieur d'un dossier de skills**. Voir [Éviter les conflits](#éviter-les-conflits-lors-des-mises-à-jour).

---

## Sommaire

- [Ce que contient le dépôt](#ce-que-contient-le-dépôt)
- [Les 39 skills](#les-39-skills)
- [Installation](#installation)
  - [1. opencode](#1-opencode)
  - [2. Claude Code](#2-claude-code)
  - [3. Claude Desktop](#3-claude-desktop)
  - [4. Antigravity](#4-antigravity)
  - [5. Vérification](#5-vérification)
- [⚠️ Éviter les conflits lors des mises à jour](#éviter-les-conflits-lors-des-mises-à-jour)
- [Serveurs MCP](#serveurs-mcp)
- [Fichiers de règles de routage](#fichiers-de-règles-de-routage)
- [Structure du dépôt](#structure-du-dépôt)
- [Contribuer](#contribuer)
- [Licence](#licence)

---

## Ce que contient le dépôt

| Dossier | Contenu |
|---|---|
| `skills/` | Les **39 skills** — chacun dans son propre dossier avec un `SKILL.md` |
| `mcp/` | Configurations MCP (`playwright`, `sequential-thinking`) pour 5 cibles, Windows + macOS/Linux |
| `rules/` | Fichiers `CLAUDE.md` de **routage automatique** : la table qui dit à l'IA quel skill charger |
| `README.md` | Ce fichier |
| `LICENSE` | Licence MIT |

**Chiffres** : 39 skills · 294 fichiers · 24 descriptions en français, 15 en anglais ·
7 catégories · 2 serveurs MCP · 5 outils couverts · 10 fichiers de configuration.

---

## Les 39 skills

Chaque skill est autonome : son nom et sa description sont injectés dans le contexte de
l'agent, qui **choisit le skill adapté** puis charge son `SKILL.md`. C'est pourquoi chaque
description contient une phrase déclencheuse explicite (« Use when… »).

### Frontend

| # | Skill | Description | Lang |
|---:|---|---|:--:|
| 1 | `accessibility` | Accessibilité web WCAG 2.1 AA, HTML sémantique, ARIA, navigation clavier, screen reader... | FR |
| 2 | `backoffice-design` | Design d'interfaces back-office, admin panels et dashboards internes. | FR |
| 3 | `data-tables` | Conception de tableaux de données : tri, filtrage, pagination, sélection, états vides... | FR |
| 4 | `form-design` | Conception de formulaires accessibles : validation, erreurs, progressive disclosure, wizards... | FR |
| 5 | `state-management` | Gestion d'état : patterns Redux, Zustand, Context, signals, architecture de store. | FR |
| 6 | `ui-styling` | Create beautiful, accessible user interfaces with shadcn/ui components (built on Radix UI +... | EN |
| 7 | `ui-ux-pro-max` | UI/UX design intelligence for web, mobile, and desktop. | EN |

### Design

| # | Skill | Description | Lang |
|---:|---|---|:--:|
| 8 | `banner-design` | Design banners for social media, ads, website heroes, creative assets, and print. | EN |
| 9 | `brand` | Brand voice, visual identity, messaging frameworks, asset management, brand consistency. | EN |
| 10 | `design` | Comprehensive design skill: brand identity, design tokens, UI styling, logo generation (55... | EN |
| 11 | `design-system` | Token architecture, component specifications, and slide generation. | EN |
| 12 | `design-system-shadcn` | Design system basé sur shadcn/ui et Radix UI. | FR |

### 3D & Animation

| # | Skill | Description | Lang |
|---:|---|---|:--:|
| 13 | `3d-performance-accessibility` | Performance, robustesse et accessibilité pour expériences web 3D et WebGL. | FR |
| 14 | `3d-web-design` | Direction artistique et UX pour sites web 3D, immersifs et interactifs. | FR |
| 15 | `gsap-core` | Official GSAP skill for the core API — gsap.to(), from(), fromTo(), easing, duration, stagger... | EN |
| 16 | `gsap-frameworks` | Official GSAP skill for Vue, Svelte, and other non-React frameworks — lifecycle, scoping... | EN |
| 17 | `gsap-performance` | Official GSAP skill for performance — prefer transforms, avoid layout thrashing, will-change... | EN |
| 18 | `gsap-plugins` | Official GSAP skill for GSAP plugins — registration, ScrollToPlugin, ScrollSmoother, Flip... | EN |
| 19 | `gsap-react` | Official GSAP skill for React — useGSAP hook, refs, gsap.context(), cleanup. | EN |
| 20 | `gsap-scrolltrigger` | Official GSAP skill for ScrollTrigger — scroll-linked animations, pinning, scrub, triggers. | EN |
| 21 | `gsap-timeline` | Official GSAP skill for timelines — gsap.timeline(), position parameter, nesting, playback. | EN |
| 22 | `gsap-utils` | Official GSAP skill for gsap.utils — clamp, mapRange, normalize, interpolate, random, snap... | EN |
| 23 | `motion-design-web` | Direction artistique et principes du motion design pour le web. | FR |
| 24 | `threejs-webgl` | Implémentation de scènes 3D web avec Three.js, React Three Fiber, Drei, WebGL et GLTF. | FR |

### SaaS

| # | Skill | Description | Lang |
|---:|---|---|:--:|
| 25 | `saas-design-system` | Design System spécifique SaaS. | FR |
| 26 | `saas-system-design` | System design et architecture SaaS modernes en 2026. | FR |
| 27 | `saas-ui-design` | UI/UX design moderne pour SaaS en 2026. | FR |

### Backend

| # | Skill | Description | Lang |
|---:|---|---|:--:|
| 28 | `api-design` | Design d'APIs REST/GraphQL : conventions de routes, versioning, pagination, gestion d'erreurs... | FR |
| 29 | `architecture` | Principes d'architecture logicielle, design patterns, Clean Architecture, SOLID, et... | FR |
| 30 | `database` | Conception et optimisation de bases de données : modélisation, index, migrations, ORM, SQL et... | FR |

### DevOps

| # | Skill | Description | Lang |
|---:|---|---|:--:|
| 31 | `ci-cd-automation` | Intégration continue, déploiement continu, pipelines CI/CD, et automatisation des workflows... | FR |
| 32 | `coding-standards` | Standards de codage professionnels, bonnes pratiques, clean code, et conventions pour un code... | FR |
| 33 | `git-workflow` | Workflow Git professionnel, branching strategies, commit conventions, et collaboration... | FR |
| 34 | `monitoring-observability` | Monitoring et observabilité : logs, métriques, traces, alerting, dashboards SRE. | FR |
| 35 | `performance` | Optimisation des performances web, Core Web Vitals, bundle analysis, caching, et monitoring. | FR |
| 36 | `security` | Sécurité applicative, OWASP Top 10, gestion des secrets, authentification, et bonnes pratiques... | FR |
| 37 | `testing` | Stratégies de test, TDD, tests unitaires, d'intégration, E2E, et couverture de code. | FR |

### Docs

| # | Skill | Description | Lang |
|---:|---|---|:--:|
| 38 | `documentation` | Rédaction de documentation technique : README, docs API, guides, changelogs. | FR |
| 39 | `slides` | Create strategic HTML presentations with Chart.js, design tokens, responsive layouts... | EN |

---

## Installation

### Étape 0 — cloner le dépôt **à part** (obligatoire)

Choisissez un emplacement **hors** de tout dossier de skills ou de projet :

```bash
# Linux / macOS
cd ~
git clone https://github.com/eliabdevweb-svg/opencode-skills-MCP.git
cd opencode-skills-MCP

# Windows (PowerShell)
cd $env:USERPROFILE
git clone https://github.com/eliabdevweb-svg/opencode-skills-MCP.git
cd opencode-skills-MCP
```

> ⛔ **Ne jamais** faire :
> `git clone … ~/.config/opencode/skills/` ni `git clone … ~/.claude/skills/`.
> Vous embarqueriez un dépôt Git dans un dossier que l'outil scanne : `git pull` ne
> fonctionnera plus correctement, `.git` pollue l'arborescence de skills, et vos
> personnalisations seront écrasées à la prochaine mise à jour.
>
> ✅ **Toujours** : un clone dédié, puis des **liens** ou une **copie contrôlée**.

---

### Où l'outil cherche-t-il les skills ?

| Outil | Emplacement lu en priorité |
|---|---|
| **opencode** — projet | `./.opencode/skills/` |
| **opencode** — global | `~/.config/opencode/skills/` |
| opencode — replis | `./.claude/skills/`, `./.agents/skills/`, `~/.claude/skills/`, `~/.agents/skills/` |
| **Claude Code** — global | `~/.claude/skills/` |
| **Claude Code** — projet | `./.claude/skills/` |
| **Claude Desktop** (mode dev) | bundle `skills-plugin` géré par l'application |
| **Antigravity** — workspace | `./.agents/skills/` |
| **Antigravity** — global | `~/.gemini/config/skills/` *(ancien emplacement `~/.gemini/antigravity/skills/` accepté)* |
| **Antigravity CLI** — global | `~/.gemini/antigravity-cli/skills/` |

> ⚠️ **Un même skill présent à deux endroits** sera chargé selon l'ordre de priorité
> ci-dessus : le premier trouvé gagne. D'où l'importance de ne **pas** installer deux
> fois le même skill par deux méthodes différentes.

---

### 1. opencode

**Option A — lien symbolique (recommandée)** : une seule source de vérité, `git pull`
suffit à tout mettre à jour.

```bash
# Linux / macOS
mkdir -p ~/.config/opencode/skills
for d in skills/*/; do
  name=$(basename "$d")
  ln -sfn "$PWD/skills/$name" "$HOME/.config/opencode/skills/$name"
done
```

```powershell
# Windows (PowerShell) — "junction", ne demande pas les droits administrateur
$dest = "$env:USERPROFILE\.config\opencode\skills"
New-Item -ItemType Directory -Force -Path $dest | Out-Null
Get-ChildItem "skills" -Directory | ForEach-Object {
  $t = Join-Path $dest $_.Name
  if (Test-Path $t) { Remove-Item $t -Recurse -Force -ErrorAction SilentlyContinue }
  New-Item -ItemType Junction -Path $t -Target $_.FullName | Out-Null
}
```

**Option B — copie simple** (sans lien) :

```bash
# Linux / macOS
mkdir -p ~/.config/opencode/skills
cp -R skills/* ~/.config/opencode/skills/
```

```powershell
# Windows (PowerShell)
Copy-Item -Path "skills\*" -Destination "$env:USERPROFILE\.config\opencode\skills" -Recurse -Force
```

**Option C — skills du projet uniquement** : copiez dans `./.opencode/skills/` à la
racine de votre projet au lieu du dossier global.

Ajoutez ensuite les serveurs MCP : voir [`mcp/README.md`](mcp/README.md).

---

### 2. Claude Code

```bash
# Linux / macOS
mkdir -p ~/.claude/skills
cp -R skills/* ~/.claude/skills/
```

```powershell
# Windows (PowerShell)
Copy-Item -Path "skills\*" -Destination "$env:USERPROFILE\.claude\skills" -Recurse -Force
```

Le lien symbolique fonctionne aussi (même commande que l'option A ci-dessus, avec
`$dest = "$env:USERPROFILE\.claude\skills"`).

Ensuite :

```bash
# Serveurs MCP recommandés
claude mcp add playwright            -- npx -y @playwright/mcp --browser chrome --caps vision,devtools
claude mcp add sequential-thinking   -- npx -y @modelcontextprotocol/server-sequential-thinking

# Contrôle
claude mcp list        # doit afficher "playwright ✓ Connected"
```

Puis le fichier de routage global :

```bash
# Linux / macOS
cp rules/CLAUDE.global.md ~/.claude/CLAUDE.md
# Windows
Copy-Item rules\CLAUDE.global.md "$env:USERPROFILE\.claude\CLAUDE.md"
```

> Si vous avez **déjà** un `~/.claude/CLAUDE.md`, ne remplacez pas : fusionnez le bloc
> « Table de routage » dans votre fichier existant.

---

### 3. Claude Desktop

**Skills** — le mode dev/Cowork de Claude Desktop lit ses skills depuis un bundle géré
par l'application (`skills-plugin`), dont le chemin contient des identifiants de session
et **change au fil des versions**. La copie manuelle y fonctionne, mais doit être
refaite après chaque mise à jour de Claude Desktop :

```powershell
# Localiser le bundle le plus récent
Get-ChildItem "$env:LOCALAPPDATA\Claude-3p\local-agent-mode-sessions\skills-plugin" -Recurse -Filter plugin.json |
  Select-Object -First 1 -ExpandProperty DirectoryName
```

Copiez ensuite les 39 dossiers dans le sous-dossier `skills/` de ce bundle.

**Serveurs MCP** — voir [`mcp/README.md`](mcp/README.md) pour les deux fichiers à
fusionner :

- standard : `%APPDATA%\Claude\claude_desktop_config.json`
- mode dev : `%LOCALAPPDATA%\Claude-3p\claude_desktop_config.json`

> Les deux sont **distincts** : modifier l'un ne met pas l'autre à jour.

---

### 4. Antigravity

[Antigravity](https://antigravity.google) (2.0, l'IDE, ses extensions et la CLI) utilise
le **standard ouvert** `SKILL.md` : les 39 skills de ce dépôt y fonctionnent **sans
aucune modification**.

**Skills** — copie ou lien vers l'un des deux emplacements :

```bash
# Linux / macOS — global (tous les projets)
mkdir -p ~/.gemini/config/skills
cp -R skills/* ~/.gemini/config/skills/

# Linux / macOS — workspace (uniquement le projet courant)
mkdir -p .agents/skills
cp -R /chemin/vers/opencode-skills-MCP/skills/* .agents/skills/
```

```powershell
# Windows (PowerShell) — global
Copy-Item -Path "skills\*" -Destination "$env:USERPROFILE\.gemini\config\skills" -Recurse -Force

# Windows (PowerShell) — workspace
Copy-Item -Path "skills\*" -Destination ".agents\skills" -Recurse -Force
```

> L'ancien emplacement `~/.gemini/antigravity/skills/` reste pris en charge (rétrocompatibilité).
> Dans l'IDE : panneau agent → **…** → **Customizations** → onglet **Skills** pour inspecter
> la liste active.

**Règles de routage** — ⚠️ **Antigravity ne lit pas `CLAUDE.md`**. Il lit `AGENTS.md`
ou `GEMINI.md`, **sans frontmatter YAML** :

```bash
# Linux / macOS
cp rules/CLAUDE.global.md ~/.gemini/AGENTS.md

# Windows (PowerShell)
Copy-Item rules\CLAUDE.global.md "$env:USERPROFILE\.gemini\AGENTS.md"
```

- **Workspace** : `AGENTS.md` ou `GEMINI.md` à la racine du projet (ou n'importe quel
  sous-dossier — Antigravity remonte l'arbre).
- **Global** : `~/.gemini/AGENTS.md`, `~/.gemini/GEMINI.md`, `~/.gemini/config/AGENTS.md`
  ou `~/.gemini/config/GEMINI.md`.
- Si `AGENTS.md` et `GEMINI.md` coexistent, **`AGENTS.md` gagne**.
- Les `.md` placés dans `.agents/rules/` exigent eux un frontmatter YAML avec un
  `trigger:` valide (`always_on` | `model_decision` | `glob` | `manual`) — un déclencheur
  absent ou mal orthographié fait **silencieusement** ignorer la règle.

**Serveurs MCP** : voir [`mcp/README.md`](mcp/README.md) (section 5) — fichier
`~/.gemini/config/mcp_config.json` (global) ou `.agents/mcp_config.json` (workspace).

> **Limites Antigravity** : 24 Ko max par fichier de règle, et 20 000 tokens au total
> pour l'ensemble des règles `always_on`. Notre fichier global fait 4,7 Ko → largement
> dans les clous.

---

### 5. Vérification

1. L'outil liste les skills disponibles (opencode : `<available_skills>` ; Claude Code :
   commande `/skills`).
2. Demandez un test : *« charge le skill `ui-ux-pro-max` »* → il doit se charger sans erreur.
3. Test visuel : *« ouvre `index.html` et prends une capture d'écran »* → doit passer par
   `browser_navigate` + `browser_take_screenshot`, **0 erreur console**.
4. **Redémarrez** opencode ou Claude Desktop : aucune configuration n'est rechargée à chaud.

---

## Éviter les conflits lors des mises à jour

C'est la partie la plus importante si vous comptez faire vivre cet installation dans le
temps.

### Les 4 pièges classiques

| # | Piège | Conséquence |
|---:|---|---|
| 1 | Cloner le dépôt **dans** `~/.config/opencode/skills/` ou `~/.claude/skills/` | Dépôt Git emboîté : `git pull` cassé, `.git` visible de l'outil, arborescence confuse |
| 2 | **Éditer directement** un skill du dépôt depuis le dossier de skills | `git pull` échoue au prochain update → conflit de fusion, ou votre modif est perdue |
| 3 | **Écraser** un skill existant portant le même nom | Votre version personnalisée disparaît silencieusement |
| 4 | Installer par **deux méthodes** (copie + lien, ou global + projet) | Deux copies divergentes ; l'ordre de priorité de l'outil décide laquelle gagne |

### Stratégie recommandée : clone dédié + liens

```
~/opencode-skills-MCP/                 ← clone Git (source de vérité, seul modifiable)
   └── skills/design/
~/.config/opencode/skills/design  ─┐
~/.claude/skills/design           ─┴── liens vers le clone
```

- **Mettre à jour** = une seule commande : `git pull`
- **Aucun conflit** : vous ne modifiez jamais un fichier du dépôt en place
- **Pas de doublon** : un seul exemplaire sur le disque
- **Pas de `.git`** dans les dossiers de skills

### Personnaliser sans conflit

> **Règle : ne jamais modifier un skill livré par le dépôt.**

- **Besoin léger** → créez **votre propre skill** à côté, avec un nom qui n'existe pas
  dans le dépôt (`mon-design`, `acme-brand`…). Aucune collision possible, et votre
  skill survit à toutes les mises à jour.
- **Besoin lourd** → **fork** le dépôt, appliquez vos changements dans votre fork, et
  récupérez l'upstream par un simple `git pull` / merge.
- **Réglage global** (routage, langue, MCP) → se fait dans `CLAUDE.md` / `AGENTS.md`,
  **jamais** dans les skills.

### Procédure de mise à jour

```bash
cd ~/opencode-skills-MCP
git pull
```

- **Installation par liens** : rien d'autre à faire.
- **Installation par copie** : re-copiez (l'`-Force` écrasera les fichiers du dépôt —
  c'est voulu, vous ne les modifiez pas) :

```bash
cp -R skills/* ~/.config/opencode/skills/
cp -R skills/* ~/.claude/skills/
```

```powershell
Copy-Item "skills\*" "$env:USERPROFILE\.config\opencode\skills" -Recurse -Force
Copy-Item "skills\*" "$env:USERPROFILE\.claude\skills" -Recurse -Force
```

Puis **redémarrez** les applications.

### Si un conflit survient quand même

```bash
cd ~/opencode-skills-MCP
git status          # quels fichiers sont "modified" ?
git stash           # met de côté vos modifications locales
git pull            # mise à jour
git stash pop       # restaure vos mods (conflit possible ici)
```

En cas de conflit irrésolu, vos modifications sont **toujours récupérables** avec
`git stash list`. Sinon, la solution radicale :

```bash
git checkout -- .   # annule les modifs locales du dépôt
git pull            # repart d'un état propre
```

> C'est précisément pour ça qu'on ne personnalise **jamais** les skills du dépôt :
> un `git checkout -- .` ne détruit alors rien qui vous appartienne.

### Nettoyer une installation faite par erreur

Si vous avez déjà cloné le dépôt **dans** un dossier de skills :

```bash
# identifier le dépôt parasite
ls -d ~/.claude/skills/.git ~/.config/opencode/skills/.git 2>/dev/null

# déplacer le clone vers sa place correcte (en conservant l'historique)
mv ~/.claude/skills/opencode-skills-MCP ~/opencode-skills-MCP

# supprimer les skills restants puis réinstaller proprement (liens)
rm -rf ~/.claude/skills/*
```

---

## Serveurs MCP

Deux serveurs MCP sont fournis, avec **10 fichiers de configuration** (5 cibles × 2
systèmes d'exploitation) :

| Serveur | Apport |
|---|---|
| **`playwright`** | Tests visuels réels : navigation, captures, snapshot d'accessibilité, console, réseau (~44 outils `browser_*`) |
| **`sequential-thinking`** | Raisonnement structuré multi-étapes |

**Cibles couvertes** : opencode · Claude Code · Claude Desktop · Claude Desktop (mode dev)
· **Antigravity** (2.0 / IDE / extensions / CLI)
**Systèmes** : Windows (`cmd /c npx …`) et macOS/Linux (`npx …`)

➡️ **Guide complet, procédure de fusion pas-à-pas et dépannage : [`mcp/README.md`](mcp/README.md)**

> Point critique : les fichiers de config contiennent **déjà** d'autres serveurs et
> clés. Il faut **fusionner** les blocs, jamais remplacer le fichier. Chaque guide
> commence par « sauvegarder puis fusionner ».

---

## Fichiers de règles de routage

Un skill n'est utile que si l'agent **le choisit**. Le routage repose sur deux leviers :

1. la **description** de chaque skill (contient une phrase déclencheuse « Use when… ») ;
2. un **fichier de règles** qui impose les arbitrages.

| Fichier | À copier vers | Rôle |
|---|---|---|
| `rules/CLAUDE.global.md` | `~/.claude/CLAUDE.md` | Table de routage complète (design, 3D/animation, dev) + règles MCP. Lu par opencode **et** Claude Code. |
| `rules/CLAUDE.global.md` | `~/.gemini/AGENTS.md` | Idem pour **Antigravity** — qui ne lit pas `CLAUDE.md` mais bien `AGENTS.md` (sans frontmatter) |
| `rules/README.md` | — | Modèle de `CLAUDE.md` projet + instructions d'installation |

Détails et modèle de règles projet : [`rules/README.md`](rules/README.md).

---

## Structure du dépôt

```
opencode-skills-MCP/
├── README.md                  ← ce fichier
├── LICENSE
├── .gitattributes
├── skills/                    ← 39 skills (294 fichiers)
│   ├── accessibility/SKILL.md
│   ├── api-design/SKILL.md
│   ├── …
│   └── ui-ux-pro-max/SKILL.md
├── mcp/
│   ├── README.md              ← guide MCP complet
│   └── configs/
│       ├── windows/           ← opencode.jsonc, claude-code.json,
│       │                        claude-desktop.json, claude-desktop-dev.json,
│       │                        antigravity.json
│       └── unix/              ← les mêmes 5, variante macOS/Linux
└── rules/
    ├── CLAUDE.global.md       ← table de routage globale
    └── README.md              ← modèle de règles projet
```

---

## Contribuer

1. Fork du dépôt
2. Branche fonctionnelle (`git checkout -b feature/mon-skill`)
3. Commit conventionnel (`git commit -m 'feat: add mon-skill'`)
4. Push (`git push origin feature/mon-skill`)
5. Pull Request

**Contraintes de qualité d'un skill** (vérifiées automatiquement) :

- dossier nommé en minuscules avec tirets, `name:` du frontmatter **identique** au nom du dossier ;
- `description:` ≤ 1024 caractères et contenant une phrase déclencheuse **« Use when… »** ;
- frontmatter YAML valide, fichier en UTF-8 **sans BOM** ;
- un titre `# ` juste après le frontmatter.

---

## Licence

MIT — voir [LICENSE](LICENSE).

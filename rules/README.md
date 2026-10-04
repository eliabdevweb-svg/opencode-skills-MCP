# CLAUDE.md — modèle de règles projet

> **À copier à la racine de votre projet** sous le nom `CLAUDE.md`.
> opencode le lit automatiquement (repli derrière `AGENTS.md`) **et** Claude Code le lit
> nativement : **un seul fichier, aucun doublon**.
>
> Si vous préférez `AGENTS.md` (convention opencode, versionné dans Git), sachez qu'il
> **prioritaire** : il masquera `CLAUDE.md` pour opencode. N'utilisez que l'un des deux.
>
> 🤖 **Antigravity** ne lit **ni** `CLAUDE.md` **ni** de frontmatter : il lit `AGENTS.md`
> ou `GEMINI.md` en markdown brut. Voir [Antigravity](#antigravity) plus bas.

---

```markdown
# <Nom du projet> — règles du projet

<2 ou 3 lignes de contexte : type d'application, stack, entrées principales.>

## Routage des skills

**Avant de commencer une tâche**, consulte la liste `<available_skills>` fournie par
l'outil `skill` et **charge le skill correspondant en premier**, puis suis son `SKILL.md`.

Le tableau de routage complet (design, 3D/animation, développement) se trouve dans
`~/.claude/CLAUDE.md` — c'est la source de vérité.

Arbitrages à connaître ici :

- Toute décision d'interface / UX → **`ui-ux-pro-max`**
- Livrable visuel (logo, mockup, icône, image social) → **`design`**
- Écrire du CSS / Tailwind / shadcn → **`ui-styling`**
- Une seule bannière ou hero → **`banner-design`**

## Conventions

- <commandes de build / lint / test>
- <structure des dossiers>
- <règles de nommage, langue des réponses…>

## Tests visuels

- Ouvrir les pages en local via `file:///chemin/absolu/vers/page.html`
- Le serveur MCP `playwright` expose ~44 outils `browser_*`
- Les configurations MCP ne sont **pas rechargées à chaud** : redémarrer l'application
```

---

## Installation du fichier global de routage

Copiez `rules/CLAUDE.global.md` vers **`~/.claude/CLAUDE.md`** :

```bash
# Linux / macOS
cp rules/CLAUDE.global.md ~/.claude/CLAUDE.md

# Windows (PowerShell)
Copy-Item rules\CLAUDE.global.md "$env:USERPROFILE\.claude\CLAUDE.md"
```

opencode le lit aussi en repli (`~/.config/opencode/AGENTS.md` a la priorité s'il existe —
ne le créez pas si vous voulez que ce fichier s'applique).

**Si vous avez déjà un `~/.claude/CLAUDE.md`** : ne remplacez pas, **fusionnez** —
ajoutez le bloc « Table de routage » à votre fichier existant.

---

## Antigravity

Antigravity (2.0, l'IDE et ses extensions, la CLI) utilise son propre système de règles.

**Emplacements** :

| Portée | Emplacement |
|---|---|
| Workspace | `AGENTS.md` ou `GEMINI.md` à la racine du projet (ou dans un sous-dossier) |
| Workspace — modulaire | `.agents/rules/*.md` |
| Global | `~/.gemini/AGENTS.md`, `~/.gemini/GEMINI.md`, `~/.gemini/config/AGENTS.md`, `~/.gemini/config/GEMINI.md` |
| Global — modulaire | `~/.gemini/config/rules/*.md` |

**Installation du fichier global de routage :**

```bash
# Linux / macOS
cp rules/CLAUDE.global.md ~/.gemini/AGENTS.md

# Windows (PowerShell)
Copy-Item rules\CLAUDE.global.md "$env:USERPROFILE\.gemini\AGENTS.md"
```

**3 règles à connaître :**

1. **`AGENTS.md` et `GEMINI.md` n'ont PAS de frontmatter.** Antigravity traite tout le
   fichier comme du markdown brut et l'active en permanence (`always_on`). Notre
   `CLAUDE.global.md` respecte déjà cette contrainte — il suffit de le renommer.
   Si les deux coexistent, **`AGENTS.md` gagne**.
2. **Les fichiers de `.agents/rules/` exigent** un frontmatter YAML avec un `trigger:`
   valide (`always_on`, `model_decision`, `glob`, `manual`) et, pour `model_decision`,
   un `description:`. Un déclencheur absent ou mal orthographié (`alwaysOn` en camelCase)
   fait **ignorer silencieusement** la règle.
3. **Limites** : 24 000 octets par fichier, et 20 000 tokens au total pour toutes les
   règles `always_on`. Au-delà, Antigravity remplace automatiquement les plus gros
   fichiers par de simples pointeurs. Notre fichier global (4,7 Ko) reste largement
   en dessous.

**Rapport règle ↔ skill** : Antigravity recommande d'écrire une *règle* pour les
contraintes (« toujours utiliser zod ») et un *skill* pour les procédures multi-étapes
(« comment lancer les migrations »). Le tableau de routage de `CLAUDE.global.md` est une
règle ; les 39 dossiers de `skills/` sont des skills.

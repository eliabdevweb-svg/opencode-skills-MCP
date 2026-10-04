# CLAUDE.md — modèle de règles projet

> **À copier à la racine de votre projet** sous le nom `CLAUDE.md`.
> opencode le lit automatiquement (repli derrière `AGENTS.md`) **et** Claude Code le lit
> nativement : **un seul fichier, aucun doublon**.
>
> Si vous préférez `AGENTS.md` (convention opencode, versionné dans Git), sachez qu'il
> **prioritaire** : il masquera `CLAUDE.md` pour opencode. N'utilisez que l'un des deux.

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

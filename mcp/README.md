# Guide d'installation — serveurs MCP

Ce dossier contient les configurations pour **trois** serveurs MCP complémentaires aux skills :

| Serveur MCP | Ce qu'il apporte | Rôle | Outils exposés |
|---|---|---|---|
| **`playwright`** | Tests visuels et interactifs réels : navigation, capture d'écran, snapshot d'accessibilité, console, réseau | 🛡️ vérification empirique | ~44 outils `browser_*` |
| **`sequential-thinking`** | Raisonnement structuré multi-étapes pour les tâches complexes | 🛡️ vérification du raisonnement | `sequential_thinking_*` |
| **`context7`** | Documentation **à jour et versionnée** injectée dans le prompt | 🛡️ **anti-hallucination** | 2 outils (`resolve-library-id`, `query-docs`) |

> **Prérequis** : Node.js ≥ 18 (`node -v`). Les trois serveurs sont lancés via `npx`,
> aucun install globale n'est nécessaire.
>
> **`context7` n'exige AUCUNE clé API** pour un usage standard (la clé ne sert qu'aux
> limits de débit plus élevées et aux dépôts privés — [context7.com/dashboard](https://context7.com/dashboard)).
> C'est le serveur MCP le plus utilisé de l'écosystème, et il cible directement les
> *« hallucinated APIs that don't even exist »* (Upstash).

---

## ⚠️ Règle n°1 — fusionner, ne jamais remplacer

Vos fichiers de configuration MCP contiennent **déjà** d'autres serveurs, des préférences,
des chemins personnels ou des clés API. Un remplacement les détruirait.

**Procédure sûre à chaque fois :**

1. **Sauvegarder** le fichier : `cp fichier.json fichier.json.bak`
2. Ouvrir le fichier et **rajouter uniquement** les blocs `playwright` /
   `sequential-thinking` / `context7`
   à l'intérieur de la clé `mcpServers` (ou `mcp` pour opencode) **qui existe déjà**.
   Si la clé n'existe pas encore, la créer.
3. Ne toucher à **aucune autre clé** du fichier.
4. **Redémarrer** l'application (voir plus bas).

Les fichiers de `configs/` sont des **fragments à fusionner**, pas des fichiers complets
à écraser.

---

## Où se trouvent les fichiers de configuration ?

| Outil | Fichier | Emplacement |
|---|---|---|
| **opencode** | `opencode.json` ou `opencode.jsonc` | Projet : `./opencode.jsonc`<br>Global : `~/.config/opencode/opencode.jsonc` |
| **Claude Code** | — (config unique) | `~/.claude.json` |
| **Claude Desktop** | `claude_desktop_config.json` | Windows : `%APPDATA%\Claude\claude_desktop_config.json`<br>macOS : `~/Library/Application Support/Claude/claude_desktop_config.json` |
| **Claude Desktop — mode dev / Cowork** | `claude_desktop_config.json` | Windows : `%LOCALAPPDATA%\Claude-3p\claude_desktop_config.json`<br>*(cible **distincte** — le mode dev ne lit pas le fichier précédent)* |
| **Antigravity** (2.0 / IDE / CLI) | `mcp_config.json` | Global : `~/.gemini/config/mcp_config.json`<br>Workspace : `<racine>/.agents/mcp_config.json` |

Choisissez la variante correspondant à votre système dans `configs/windows/` ou `configs/unix/`.

---

## 1. opencode

Fichier : `configs/<os>/opencode.jsonc`

Clé à utiliser : **`mcp`** (opencode), avec `"type": "local"` et la commande en **tableau** :

```jsonc
{
  "mcp": {
    "playwright": {
      "type": "local",
      "command": ["npx", "-y", "@playwright/mcp", "--browser", "chrome", "--caps", "vision,devtools", "--allow-unrestricted-file-access"],
      "enabled": true,
      "timeout": 20000
    },
    "sequential-thinking": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-sequential-thinking"],
      "enabled": true
    }
  }
}
```

> Sous Windows, préfixez la commande par `cmd /c` : `"command": ["cmd", "/c", "npx", "-y", ...]`.

**Pour désactiver temporairement** : `"enabled": false` (la clé reste, aucun conflit à la mise à jour).
**Pour désactiver globalement un serveur** : dans `tools`, ajouter `"<outil>_*": false`.

---

## 2. Claude Code

Fichier : `configs/<os>/claude-code.json` → à fusionner dans `~/.claude.json`

```json
{
  "mcpServers": {
    "playwright": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@playwright/mcp", "--browser", "chrome", "--caps", "vision,devtools", "--allow-unrestricted-file-access"],
      "env": {}
    },
    "sequential-thinking": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"],
      "env": {}
    }
  }
}
```

Alternative recommandée en ligne de commande (évite d'éditer le JSON à la main) :

```bash
claude mcp add playwright     -- npx -y @playwright/mcp --browser chrome --caps vision,devtools
claude mcp add sequential-thinking -- npx -y @modelcontextprotocol/server-sequential-thinking
```

Vérification :

```bash
claude mcp list      # doit afficher "playwright ✓ Connected"
```

> **Windows** : les serveurs MCP *stdio* doivent passer par `cmd /c` sinon le processus
> fils ne détache pas et la connexion bloque. Utilisez `"command": "cmd", "args": ["/c", "npx", ...]`.

---

## 3. Claude Desktop

Fichier : `configs/<os>/claude-desktop.json` → à fusionner dans `claude_desktop_config.json`

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp", "--browser", "chrome", "--caps", "vision,devtools", "--allow-unrestricted-file-access"]
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
    }
  }
}
```

⚠️ Conservez **toutes** les clés existantes du fichier : `deploymentMode`, `preferences`,
`coworkUserFilesPath`, autres serveurs… Seul le bloc `mcpServers` doit être complété.

---

## 4. Claude Desktop — mode dev / Cowork

Même contenu, **fichier différent** : `configs/<os>/claude-desktop-dev.json`
à fusionner dans `%LOCALAPPDATA%\Claude-3p\claude_desktop_config.json`.

Le mode dev possède sa propre arborescence (sessions, `.claude` temporaires,
`skills-plugin`) et **ne lit pas** le `claude_desktop_config.json` standard.
Les deux configurations doivent donc être mises à jour **séparément**.

---

## 5. Antigravity (2.0 / IDE / extensions / CLI)

Fichier : `configs/<os>/antigravity.json`

Antigravity utilise le **même format `mcpServers`** que Claude Desktop.

**Éditeur graphique** (recommandé) :

1. Panneau agent → menu **…** → **MCP Servers** → **Manage MCP Servers**
2. **View raw config** — ouvre le `mcp_config.json`
3. Fusionner les blocs `playwright` et `sequential-thinking`

**À la main**, dans `~/.gemini/config/mcp_config.json` (global) :

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp", "--browser", "chrome", "--caps", "vision,devtools", "--allow-unrestricted-file-access"],
      "env": {}
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"],
      "env": {}
    }
  }
}
```

Notes Antigravity :

- **Portée** : global (`~/.gemini/config/mcp_config.json`) **ou** workspace
  (`<racine>/.agents/mcp_config.json`). Les deux coexistent ; le workspace l'emporte.
- **MCP Store** : Antigravity propose un magasin intégré — utile pour découvrir des
  serveurs, mais un serveur *custom* passe obligatoirement par l'édition du fichier.
- **Windows** : comme pour les autres cibles, préfixez par `cmd /c`.
- **Champ distant** : pour un serveur HTTP/SSE, utilisez `"serverUrl"` (les champs
  legacy `url` / `httpUrl` sont **rejetés**).
- L'authentification OAuth des serveurs distants est stockée dans
  `~/.gemini/antigravity/mcp_oauth_tokens.json` — ne pas y toucher à la main.

---

## Context7 — usage et invocation

Le bloc à fusionner est fourni dans `configs/<os>/<outil>.json` sous la clé `context7`.
Il est **identique pour les 5 cibles** (seul le format de la clé change : `mcp` chez
opencode, `mcpServers` chez les autres).

**Formats :**

```jsonc
// opencode (mcp, commande en tableau)
"context7": { "type": "local", "command": ["npx", "-y", "@upstash/context7-mcp"], "enabled": true, "timeout": 20000 }

// Claude Code / Claude Desktop / Claude Desktop dev (mcpServers, type stdio)
"context7": { "type": "stdio", "command": "npx", "args": ["-y", "@upstash/context7-mcp"], "env": {} }

// Antigravity (mcpServers, sans type)
"context7": { "command": "npx", "args": ["-y", "@upstash/context7-mcp"], "env": {} }

// Windows — variante systématique
{ "command": "cmd", "args": ["/c", "npx", "-y", "@upstash/context7-mcp"] }
```

**Invocation** : ajouter `use context7` à la demande, ou poser une règle permanente.
Exemple : *« Crée un middleware Next.js qui valide un JWT. use context7 »*.

**Pourquoi c'est un anti-hallucination** : le serveur injecte la documentation
**réelle et versionnée** du dépôt source, ce qui supprime les deux causes les plus
fréquentes d'hallucination en développement — les APIs inventées et les exemples
issus de données d'entraînement périmées.

**Coût de schéma** : ~1 200 tokens pour 2 outils. C'est l'un des serveurs les plus
économes de l'écosystème — un bon choix si vous craignez la charge des schémas MCP.

**Windows** : si `npx` échoue avec Context7 alors que les autres marchent, le readme
Upstash signale que `SystemRoot` et `APPDATA` doivent être présents dans `env` :

```json
"env": { "SystemRoot": "C:\\Windows", "APPDATA": "C:\\Users\\<vous>\\AppData\\Roaming" }
```

---

## Redémarrage obligatoire

**Aucune configuration n'est rechargée à chaud.** Après chaque modification :

- **opencode** — quitter complètement l'application et la relancer
- **Claude Code** — relancer la session `claude`
- **Antigravity** — quitter puis relancer l'application (ou recharger la fenêtre)
- **Claude Desktop** — `Ctrl+Shift+R` (recharger la fenêtre) ou quitter/repartir

## Vérification

1. L'outil doit annoncer les serveurs connectés (ex. `playwright ✓ Connected`).
2. Demandez un test visuel : *« ouvre `index.html` et prends une capture »*.
3. Vérifiez l'absence d'erreurs — `browser_console_messages` doit renvoyer **0 erreur**.

## Dépannage

| Symptôme | Cause | Correction |
|---|---|---|
| `spawn npx ENOENT` | Node.js absent du `PATH` | Installer Node ≥ 18, redémarrer la session |
| Serveur en échec sur Windows | `npx` lancé sans `cmd /c` | Utiliser `"command": "cmd", "args": ["/c", "npx", ...]` |
| Le serveur disparaît après une mise à jour | Le fichier entier a été écrasé | Restaurer le `.bak` et **fusionner** les blocs |
| Rien ne se passe après modification | Config non rechargée | Redémarrer complètement l'application |
| Antigravity n'affiche pas le serveur | Mauvais fichier édité (config Claude au lieu de `mcp_config.json`) | Éditer `~/.gemini/config/mcp_config.json` ou `.agents/mcp_config.json` |
| Antigravity rejette un serveur distant | Champ `url` / `httpUrl` (legacy) | Utiliser `"serverUrl"` |
| Context7 : `spawn npx ENOENT` uniquement sur ce serveur | `SystemRoot` / `APPDATA` absents de `env` | Ajouter les deux clés dans le bloc `env` (voir section Context7) |
| Context7 : limites de débit atteintes | Pas de clé API (usage anonyme) | Clé optionnelle sur [context7.com/dashboard](https://context7.com/dashboard), passer par `--api-key` |
| Conflit à `git pull` du dépôt | Clone effectué **dans** le dossier de skills | Voir la section « Éviter les conflits » du README principal |

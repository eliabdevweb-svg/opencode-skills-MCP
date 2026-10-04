# Guide d'installation — serveurs MCP

Ce dossier contient les configurations pour deux serveurs MCP complémentaires aux skills :

| Serveur MCP | Ce qu'il apporte | Outils `browser_*` / outils exposés |
|---|---|---|
| **`playwright`** | Tests visuels et interactifs réels : navigation, capture d'écran, snapshot d'accessibilité, console, réseau | ~44 outils `browser_*` |
| **`sequential-thinking`** | Raisonnement structuré multi-étapes pour les tâches complexes | outils `sequential_thinking_*` |

> **Prérequis** : Node.js ≥ 18 (`node -v`). Les deux serveurs sont lancés via `npx`, aucun install globale n'est nécessaire.

---

## ⚠️ Règle n°1 — fusionner, ne jamais remplacer

Vos fichiers de configuration MCP contiennent **déjà** d'autres serveurs, des préférences,
des chemins personnels ou des clés API. Un remplacement les détruirait.

**Procédure sûre à chaque fois :**

1. **Sauvegarder** le fichier : `cp fichier.json fichier.json.bak`
2. Ouvrir le fichier et **rajouter uniquement** le bloc `playwright` / `sequential-thinking`
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

## Redémarrage obligatoire

**Aucune configuration n'est rechargée à chaud.** Après chaque modification :

- **opencode** — quitter complètement l'application et la relancer
- **Claude Code** — relancer la session `claude`
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
| Conflit à `git pull` du dépôt | Clone effectué **dans** le dossier de skills | Voir la section « Éviter les conflits » du README principal |

---
name: context-engineering
description: "Context engineering et économie de tokens selon Anthropic : attention budget, context rot, compaction, tool-result clearing, structured note-taking, subagents, progressive disclosure, just-in-time retrieval, prompt caching. Use when a conversation grows long, the context window fills up, token cost or usage must be reduced, choosing between a subagent and inline work, designing tool outputs, or when the user says tokens, fenetre de contexte, compaction, cache, memoire d agent, economiser ou trop long."
version: 1.0.0
tags: [tokens, context, compaction, caching, memory, agents, performance]
---

# Context Engineering

Gestion du contexte comme **ressource finie**.
Source : [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — Anthropic, 29/09/2025.

## Les deux principes

- **Context rot** — plus le contexte se remplit, plus le rappel est mauvais.
  C'est un effet d'architecture (relations pairwise O(n²)), pas un défaut de modèle.
  *La surcharge de contexte est une cause d'hallucination, pas seulement un coût.*
- **Attention budget** — chaque token dépense un budget d'attention fini.

> Objectif : **le plus petit ensemble de tokens à haut signal** qui maximise la
> probabilité du résultat voulu.

## Les 4 échecs à connaître

| Échec | Description |
|---|---|
| **Context poisoning** | Une mauvaise info est ressassée → l'erreur se compound d'un tour à l'autre |
| **Context distraction** | Le contexte est si long qu'il écrase l'apprentissage d'entraînement |
| **Context confusion** | Le modèle mélange les sources et attribue le mauvais contenu |
| **Context rot** | Délitement progressif du rappel quand la fenêtre se remplit |

## Leviers, par ordre de rentabilité

### 1. Tool-result clearing (le plus sûr et le plus léger)

Une fois un résultat d'outil consigné profondément dans l'historique, **inutile de le
remontrer**. Anthropic le présente comme la forme de compaction la plus légère.

```text
Vide les anciens résultats d'outils du contexte ; conserve uniquement les conclusions.
```

### 2. Compaction

Résumer un contexte proche de la limite, puis rouvrir avec le résumé.

- Conserver : décisions d'architecture, bugs ouverts, détails d'implémentation
- Jeter : sorties d'outils redondantes, messages intermédiaires
- **Régler d'abord la *rappel*, puis améliorer la *précision*** (trop de compaction
  perd du contexte subtil dont l'importance n'apparaît qu'après coup)

### 3. Structured note-taking (mémoire agencée)

Écrire hors contexte, relire à la demande. Coût quasi nul, continuité sur des heures.

- Fichier `NOTES.md`, liste de tâches, plan de session — l'agent écrit, puis relit
- Survit aux compactions et aux resets de contexte
- Équivalent API : le **memory tool** d'Anthropic

### 4. Subagents — compression par fenêtres séparées

Un sous-agent explore **des dizaines de milliers de tokens** dans sa propre fenêtre et
ne rend **qu'un résumé condensé de 1 000 à 2 000 tokens**.

⚠️ Coût réel (Anthropic) : un agent = **~4×** les tokens d'un chat, un système
multi-agent = **~15×**. À réserver aux tâches réellement parallélisables.

### 5. Just-in-time retrieval

Ne garder que des **références légères** — chemins de fichiers, URLs, requêtes — et
charger le détail **uniquement au besoin**.

```text
# Au lieu de : tout lire maintenant
# → ne conserver que : src/services/auth.ts, ligne 40-82
#   et relire à la demande
```

### 6. Progressive disclosure (skills et outils)

Ne charger que la métadonnée (description) jusqu'au moment où la compétence est
réellement requise. **C'est exactement le mécanisme des Agent Skills.**

### 7. Prompt caching

| | Multiplicateur |
|---|---|
| Écriture cache (5 min) | 1,25× le prix input |
| **Lecture cache** | **0,1×** le prix input |
| Gain observé | **−90 % coût, −85 % latence** sur les prompts longs |

Règles pour préserver le cache :
- Contenu **statique en premier** (tools → system → messages)
- Changer de modèle = **invalidation totale** du cache
- Ordre de stabilité décroissante : prompt système, contexte projet, conversation

## Diagnostic rapide

```
La fenêtre se remplit vite ?
├─ Beaucoup de résultats d'outils bruts   → tool-result clearing
├─ Historique très long                   → compaction / /compact
├─ L'agent répète ses propres erreurs     → context poisoning : purger + NOTER
├─ Tout est lu "par sécurité"             → just-in-time retrieval
├─ Trop d'outils MCP chargés              → réduire le nb d'outils (coût de schéma)
└─ Tâche vaste et parallélisable          → subagent (en acceptant le coût)
```

## Coût de schéma MCP — souvent oublié

Chaque outil MCP ajoute sa définition à **chaque** requête. Un serveur à 30 outils
coûte plus cher qu'un serveur à 2 outils, même si on n'en utilise jamais la moitié.

Privilégier les serveurs à **1–5 outils** : Context7 (2 outils, ~1,2 k tokens),
Fetch, Exa (~600 tokens).

## Vérification

Après une optimisation, confirmer que la **qualité n'a pas régressé** :
1. Les décisions d'architecture sont-elles encore présentes après compaction ?
2. Les fichiers critiques sont-ils toujours référencés ?
3. Le taux d'erreur a-t-il bougé ? — le token le moins cher est celui qui ne dégrade pas
   la réponse.

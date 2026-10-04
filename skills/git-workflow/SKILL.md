---
name: git-workflow
description: "Workflow Git professionnel, branching strategies, commit conventions, et collaboration d'équipe. Use when creating branches, writing or fixing commits, rebasing, resolving merge conflicts, reviewing pull requests, or when the user mentions git, branche, commit, merge, rebase ou convention de commit."
version: 1.0.0
tags: [git, workflow, branching, commits, collaboration]
---

# Git Workflow

Guide pour un workflow Git professionnel et une collaboration d'équipe efficace.

## Branching Strategy

### Git Flow

```
main (production)
├── develop (intégration)
│   ├── feature/user-auth
│   ├── feature/payment
│   └── feature/dashboard
├── release/v1.2.0
└── hotfix/critical-bug
```

### GitHub Flow (Recommandé)

```
main (production)
├── feature/user-auth
├── feature/payment
└── feature/dashboard
```

**Règles :**
- `main` est toujours déployable
- Toutes les branches partent de `main`
- Les PRs sont requises pour merger
- Le CI doit passer avant le merge

## Conventional Commits

### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type | Description | Exemple |
|------|-------------|---------|
| `feat` | Nouvelle fonctionnalité | `feat(auth): add OAuth login` |
| `fix` | Correction de bug | `fix(api): handle null response` |
| `docs` | Documentation | `docs(readme): update install steps` |
| `style` | Formatage (pas de changement logique) | `style: fix prettier issues` |
| `refactor` | Refactorisation | `refactor(auth): extract validation` |
| `perf` | Amélioration de performance | `perf(api): add caching` |
| `test` | Ajout de tests | `test(auth): add unit tests` |
| `build` | Build system | `build: update webpack config` |
| `ci` | CI/CD | `ci: add github actions` |
| `chore` | Maintenance | `chore: update dependencies` |
| `revert` | Revert | `revert: feat(auth): add OAuth` |

### Exemples

```bash
# Feature
git commit -m "feat(user): add profile page"

# Fix
git commit -m "fix(auth): prevent token expiration race condition"

# Breaking change
git commit -m "feat(api)!: change response format

BREAKING CHANGE: API responses now use camelCase instead of snake_case"

# Avec body
git commit -m "feat(payment): add Stripe integration

- Add Stripe SDK
- Create payment intent endpoint
- Handle webhook events
- Add idempotency keys

Closes #123"
```

## Workflow Quotidien

### 1. Début de journée

```bash
# Récupérer les dernières modifications
git fetch origin
git checkout main
git pull origin main

# Créer ou mettre à jour sa branche feature
git checkout -b feature/my-feature
# ou
git checkout feature/my-feature
git rebase main
```

### 2. Pendant le développement

```bash
# Travailler par petits commits
git add -p  # Stage sélectif
git commit -m "feat(auth): add login form"

# Pousser régulièrement
git push origin feature/my-feature
```

### 3. Avant de créer une PR

```bash
# Nettoyer l'historique
git rebase -i main

# Squasher les commits de wip
git rebase -i HEAD~5

# Pousser avec force (après rebase)
git push origin feature/my-feature --force-with-lease
```

### 4. Créer une PR

```bash
# Depuis GitHub CLI
gh pr create \
  --title "feat(auth): add OAuth login" \
  --body "## Description
Add OAuth login with Google and GitHub

## Changes
- Add OAuth provider configuration
- Create callback handlers
- Add session management

## Testing
- [ ] Unit tests added
- [ ] Integration tests pass
- [ ] Manual testing done

Closes #123" \
  --reviewer team-leads
```

## Rebase vs Merge

### Rebase (Recommandé pour les features)

```bash
# Avant
main: A → B → C
feature: D → E

# Après rebase
main: A → B → C
feature: D' → E' (rejoués sur C)

# Commande
git checkout feature
git rebase main
```

**Avantages :** Historique linéaire, propre
**Inconvénients :** Réécrit l'historique, dangereux sur les branches partagées

### Merge (Recommandé pour les releases)

```bash
# Après merge
main: A → B → C → M (merge commit)
feature: D → E

# Commande
git checkout main
git merge feature
```

**Avantages :** Préserve l'historique, sûr pour les branches partagées
**Inconvénients :** Historique plus complexe

## Sauvegarde et Récupération

### Stash

```bash
# Sauvegarder les changements
git stash push -m "work in progress"

# Lister les stashes
git stash list

# Récupérer le stash
git stash pop

# Récupérer un stash spécifique
git stash apply stash@{1}
```

### Undo

```bash
# Annuler le dernier commit (garder les changements)
git reset --soft HEAD~1

# Annuler le dernier commit (perdre les changements)
git reset --hard HEAD~1

# Annuler un push (dangereux)
git push --force-with-lease

# Révertir un commit (crée un nouveau commit)
git revert <commit-hash>
```

## Git Hooks

### Husky + lint-staged

```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  },
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}
```

### Commitlint

```javascript
// commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'docs', 'style', 'refactor',
      'perf', 'test', 'build', 'ci', 'chore', 'revert'
    ]],
    'subject-max-length': [2, 'always', 72]
  }
};
```

## Commands Utiles

```bash
# Information
git log --oneline --graph --all  # Visualiser l'historique
git diff main                    # Diff avec main
git blame file.ts                # Qui a écrit quoi
git shortlog -sn                 # Contributeurs

# Nettoyage
git gc --aggressive              # Garbage collection
git clean -fd                    # Supprimer les fichiers non trackés

# Avancé
git bisect start                 # Trouver le commit qui a cassé
git bisect bad                   # Commit actuel est mauvais
git bisect good <commit>         # Ce commit était bon
```

## Checklist PR

- [ ] Le code suit-il les conventions de commit ?
- [ ] Les tests passent-ils ?
- [ ] Le lint passe-t-il ?
- [ ] La documentation est-elle mise à jour ?
- [ ] Les breaking changes sont-ils documentés ?
- [ ] Le reviewer est-il assigné ?
- [ ] La description de la PR est-elle claire ?

## Anti-Patterns

| Pattern | Problème | Solution |
|---------|----------|----------|
| Force push sur main | Perd l'historique | Utiliser des PRs |
| Grand commit | Difficile à review | Séparer par fonctionnalité |
| Merge sans PR | Pas de review | Toujours passer par des PRs |
| Commit de secrets | Sécurité compromise | Utiliser des .env |
| Historique sale | Difficile à naviguer | Utiliser rebase régulièrement |

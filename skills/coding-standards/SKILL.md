---
name: coding-standards
description: "Standards de codage professionnels, bonnes pratiques, clean code, et conventions pour un code maintenable et scalable. Use when reviewing code style, applying clean code rules, naming and formatting conventions, refactoring for readability, or when the user asks about coding standards, linting, bonnes pratiques ou conventions de code."
version: 1.0.0
tags: [coding, standards, clean-code, best-practices, quality]
---

# Coding Standards

Standards de codage professionnels basés sur les meilleures pratiques de l'industrie (Google, Airbnb, Airbnb).

## Principes Fondamentaux

### Clean Code Rules
- **Nommage explicite** : Les noms doivent révéler l'intention (`getUserById` pas `fetch`)
- **Fonctions courtes** : Max 20 lignes, une seule responsabilité
- **DRY** : Don't Repeat Yourself - extraire la logique réutilisable
- **KISS** : Keep It Simple, Stupid - privilégier la simplicité
- **YAGNI** : You Ain't Gonna Need It - ne pas anticiper

### Structure du Code

```
src/
├── components/      # Composants UI réutilisables
├── services/        # Logique métier et appels API
├── hooks/           # Hooks React personnalisés
├── utils/           # Fonctions utilitaires pures
├── types/           # Définitions de types TypeScript
├── constants/       # Constantes et configurations
└── tests/           # Tests unitaires et d'intégration
```

## Conventions par Langage

### JavaScript/TypeScript

```typescript
// ✅ Bon - Nommage explicite, types clairs
interface User {
  id: string;
  name: string;
  email: string;
}

async function getUserById(userId: string): Promise<User | null> {
  // Logique ici
}

// ❌ Mauvais - Nommage vague, pas de types
async function fetch(id) {
  // Logique ici
}
```

**Règles TypeScript :**
- Toujours utiliser `strict: true` dans tsconfig
- Privilégier `interface` aux `type` pour les objets
- Éviter `any` - utiliser `unknown` si nécessaire
- Utiliser les enums pour les valeurs constantes
- Exporter les types avec `export type`

### React/Next.js

```tsx
// ✅ Composant fonctionnel avec hooks
export function UserCard({ user }: UserCardProps) {
  const [isLoading, setIsLoading] = useState(false);
  
  return (
    <div className="user-card">
      <h2>{user.name}</h2>
    </div>
  );
}

// ✅ Props typées
interface UserCardProps {
  user: User;
  onSelect?: (userId: string) => void;
}
```

**Règles React :**
- Composants fonctionnels uniquement (pas de classes)
- Hooks pour la logique d'état
- Props déstructurées et typées
- Éviter les props drilling - utiliser Context
- Nommage : PascalCase pour les composants

### CSS/Tailwind

```css
/* ✅ Utiliser les design tokens */
.card {
  background: var(--color-card);
  padding: var(--space-4);
  border-radius: var(--radius-lg);
}

/* ❌ Éviter les valeurs hardcodées */
.card {
  background: #ffffff;
  padding: 16px;
  border-radius: 8px;
}
```

**Règles CSS :**
- Utiliser les CSS variables pour les tokens
- BEM pour la nomenclature si CSS classique
- Tailwind de préférence pour les nouveaux projets
- Responsive : mobile-first
- Éviter `!important`

## Gestion des Erreurs

```typescript
// ✅ Gestion structurée des erreurs
class AppError extends Error {
  constructor(
    message: string,
    public code: string,
    public statusCode: number = 500
  ) {
    super(message);
    this.name = 'AppError';
  }
}

// ✅ Toujours catcher les erreurs
async function fetchData(url: string): Promise<Data> {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new AppError('Failed to fetch', 'FETCH_ERROR', response.status);
    }
    return await response.json();
  } catch (error) {
    if (error instanceof AppError) {
      throw error;
    }
    throw new AppError('Unexpected error', 'UNKNOWN_ERROR');
  }
}
```

## Nommage

| Élément | Convention | Exemple |
|---------|------------|---------|
| Variables | camelCase | `userName`, `isActive` |
| Fonctions | camelCase | `getUserById`, `formatDate` |
| Composants | PascalCase | `UserCard`, `NavigationBar` |
| Classes | PascalCase | `UserService`, `HttpClient` |
| Constantes | UPPER_SNAKE_CASE | `API_BASE_URL`, `MAX_RETRIES` |
| Fichiers | kebab-case | `user-service.ts`, `http-client.ts` |
| Dossiers | kebab-case | `user-management/`, `api-services/` |

## Documentation

```typescript
/**
 * Récupère un utilisateur par son ID
 * @param userId - L'identifiant unique de l'utilisateur
 * @returns L'utilisateur trouvé ou null si non trouvé
 * @throws {AppError} Si l'ID est invalide ou si l'API échoue
 * @example
 * ```ts
 * const user = await getUserById('123');
 * if (user) console.log(user.name);
 * ```
 */
async function getUserById(userId: string): Promise<User | null> {
  // Implementation
}
```

## Code Review Checklist

- [ ] Le code est-il lisible et compréhensible ?
- [ ] Les noms révèlent-ils l'intention ?
- [ ] Les fonctions font-elles une seule chose ?
- [ ] Y a-t-il de la duplication ?
- [ ] Les erreurs sont-elles gérées ?
- [ ] Les types sont-ils explicites ?
- [ ] Le code est-il testé ?
- [ ] La documentation est-elle à jour ?

## Anti-Patterns à Éviter

| Pattern | Problème | Solution |
|---------|----------|----------|
| God Object | Objet trop gros, trop de responsabilités | Décomposer en objets plus petits |
| Spaghetti Code | Logique mélangée, pas de structure | Séparer les couches (API, UI, Data) |
| Magic Numbers | Valeurs hardcodées incompréhensibles | Utiliser des constantes nommées |
| Premature Optimization | Optimiser sans mesurer | Mesurer d'abord, optimiser après |
| Copy-Paste | Duplication de code | Extraire en fonctions/réutilisable |

## Outils Recommandés

| Catégorie | Outil | Usage |
|-----------|-------|-------|
| Linting | ESLint | Analyse statique du code |
| Formatting | Prettier | Formatage automatique |
| Types | TypeScript | Typage statique |
| Tests | Jest/Vitest | Tests unitaires |
| E2E | Playwright/Cypress | Tests d'intégration |
| CI | GitHub Actions | Intégration continue |

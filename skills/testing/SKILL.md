---
name: testing
description: "Stratégies de test, TDD, tests unitaires, d'intégration, E2E, et couverture de code. Use when writing or fixing unit, integration or E2E tests, choosing a test strategy, setting up TDD, mocks, fixtures or coverage, or when the user says test, tester, couverture, TDD, jest, vitest ou playwright."
version: 1.0.0
tags: [testing, tdd, vitest, jest, playwright, e2e]
---

# Testing Strategy

Guide complet pour les tests : unitaires, d'intégration, E2E, et TDD.

## Pyramide des Tests

```
         /\
        / E2E \        ← 10% - Tests de bout en bout
       /--------\
      / Integration\   ← 20% - Tests d'intégration
     /--------------\
    /   Unit Tests    \ ← 70% - Tests unitaires
   /------------------\
```

## Vitest Configuration

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'node',
    include: ['src/**/*.test.ts'],
    exclude: ['node_modules', 'dist'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: ['node_modules', 'dist', '**/*.test.ts'],
      thresholds: {
        branches: 80,
        functions: 80,
        lines: 80,
        statements: 80
      }
    },
    setupFiles: ['./tests/setup.ts']
  }
});
```

## Tests Unitaires

### Structure AAA (Arrange, Act, Assert)

```typescript
import { describe, it, expect, vi } from 'vitest';
import { UserService } from './user.service';

describe('UserService', () => {
  describe('createUser', () => {
    it('should create a user with valid data', async () => {
      // Arrange
      const mockRepo = {
        save: vi.fn().mockResolvedValue({ id: '1', name: 'John' })
      };
      const service = new UserService(mockRepo);
      const input = { name: 'John', email: 'john@example.com' };
      
      // Act
      const result = await service.createUser(input);
      
      // Assert
      expect(result).toEqual({ id: '1', name: 'John' });
      expect(mockRepo.save).toHaveBeenCalledWith(
        expect.objectContaining({ name: 'John', email: 'john@example.com' })
      );
    });

    it('should throw error for invalid email', async () => {
      // Arrange
      const mockRepo = { save: vi.fn() };
      const service = new UserService(mockRepo);
      const input = { name: 'John', email: 'invalid' };
      
      // Act & Assert
      await expect(service.createUser(input)).rejects.toThrow('Invalid email');
      expect(mockRepo.save).not.toHaveBeenCalled();
    });
  });
});
```

### Mocking

```typescript
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { EmailService } from './email.service';

// Mock du module
vi.mock('./email.client', () => ({
  sendEmail: vi.fn()
}));

describe('EmailService', () => {
  let service: EmailService;
  let mockSendEmail: ReturnType<typeof vi.fn>;

  beforeEach(() => {
    vi.clearAllMocks();
    mockSendEmail = vi.fn().mockResolvedValue({ success: true });
    service = new EmailService(mockSendEmail);
  });

  it('should send welcome email', async () => {
    // Arrange
    const user = { name: 'John', email: 'john@example.com' };
    
    // Act
    await service.sendWelcome(user);
    
    // Assert
    expect(mockSendEmail).toHaveBeenCalledWith({
      to: 'john@example.com',
      subject: 'Welcome!',
      body: expect.stringContaining('John')
    });
  });
});
```

## Tests d'Intégration

```typescript
import { describe, it, expect, beforeAll, afterAll, beforeEach } from 'vitest';
import { createClient } from '@supabase/supabase-js';
import { UserService } from './user.service';

describe('UserService Integration', () => {
  let supabase: ReturnType<typeof createClient>;
  let service: UserService;

  beforeAll(() => {
    supabase = createClient(
      process.env.SUPABASE_URL!,
      process.env.SUPABASE_KEY!
    );
    service = new UserService(supabase);
  });

  beforeEach(async () => {
    // Nettoyer la base avant chaque test
    await supabase.from('users').delete().neq('id', '');
  });

  afterAll(async () => {
    // Nettoyer après tous les tests
    await supabase.from('users').delete().neq('id', '');
  });

  it('should persist user to database', async () => {
    // Arrange
    const input = { name: 'John', email: 'john@test.com' };
    
    // Act
    const created = await service.createUser(input);
    
    // Assert - Vérifier en base
    const { data } = await supabase
      .from('users')
      .select('*')
      .eq('id', created.id)
      .single();
    
    expect(data).toMatchObject(input);
  });
});
```

## Tests E2E (Playwright)

```typescript
import { test, expect } from '@playwright/test';

test.describe('Authentication Flow', () => {
  test('should login successfully', async ({ page }) => {
    // Arrange
    await page.goto('/login');
    
    // Act
    await page.fill('[data-testid="email"]', 'user@example.com');
    await page.fill('[data-testid="password"]', 'password123');
    await page.click('[data-testid="submit"]');
    
    // Assert
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('[data-testid="user-menu"]')).toBeVisible();
  });

  test('should show error for invalid credentials', async ({ page }) => {
    // Arrange
    await page.goto('/login');
    
    // Act
    await page.fill('[data-testid="email"]', 'wrong@example.com');
    await page.fill('[data-testid="password"]', 'wrongpass');
    await page.click('[data-testid="submit"]');
    
    // Assert
    await expect(page.locator('[data-testid="error-message"]'))
      .toHaveText('Invalid credentials');
  });
});

test.describe('API Tests', () => {
  test('should return user by id', async ({ request }) => {
    // Act
    const response = await request.get('/api/users/1');
    
    // Assert
    expect(response.ok()).toBeTruthy();
    const data = await response.json();
    expect(data).toHaveProperty('id');
    expect(data).toHaveProperty('name');
  });
});
```

## TDD (Test-Driven Development)

### Cycle Red-Green-Refactor

```
1. RED    → Écrire un test qui échoue
2. GREEN  → Écrire le minimum de code pour que le test passe
3. REFACTOR → Améliorer le code sans casser les tests
```

### Exemple TDD

```typescript
// 1. RED - Test échoue
describe('calculateDiscount', () => {
  it('should apply 10% discount for orders over 100', () => {
    const result = calculateDiscount(150);
    expect(result).toBe(135); // 150 - 15 (10%)
  });
});

// 2. GREEN - Code minimal
function calculateDiscount(amount: number): number {
  if (amount > 100) {
    return amount * 0.9;
  }
  return amount;
}

// 3. REFACTOR - Améliorer
function calculateDiscount(amount: number, discountPercent: number = 10): number {
  if (amount > 100) {
    return amount * (1 - discountPercent / 100);
  }
  return amount;
}
```

## Test Patterns

### Parameterized Tests

```typescript
describe('validateEmail', () => {
  const testCases = [
    { input: 'test@example.com', expected: true },
    { input: 'invalid', expected: false },
    { input: '@example.com', expected: false },
    { input: 'test@', expected: false },
    { input: '', expected: false }
  ];

  testCases.forEach(({ input, expected }) => {
    it(`should return ${expected} for "${input}"`, () => {
      expect(validateEmail(input)).toBe(expected);
    });
  });
});
```

### Snapshot Testing

```typescript
it('should render correctly', () => {
  const { container } = render(<UserCard user={mockUser} });
  expect(container).toMatchSnapshot();
});
```

## Coverage Configuration

```typescript
// vitest.config.ts
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'dist/',
        '**/*.test.ts',
        '**/*.spec.ts',
        '**/types.ts',
        '**/index.ts'
      ],
      thresholds: {
        branches: 80,
        functions: 80,
        lines: 80,
        statements: 80
      }
    }
  }
});
```

## Checklist Testing

### Unit Tests
- [ ] Chaque fonction a-t-elle au moins un test ?
- [ ] Les cas limites sont-ils testés ?
- [ ] Les erreurs sont-elles testées ?
- [ ] Les tests sont-ils indépendants ?
- [ ] Les mocks sont-ils bien nettoyés ?

### Integration Tests
- [ ] Les flux principaux sont-ils testés ?
- [ ] La base de données est-elle testée ?
- [ ] Les API externes sont-elles mockées ?
- [ ] Les tests sont-ils isolés ?

### E2E Tests
- [ ] Les parcours utilisateurs critiques sont-ils testés ?
- [ ] Les formulaires sont-ils testés ?
- [ ] La navigation est-elle testée ?
- [ ] Le responsive est-il testé ?

## Outils

| Catégorie | Outil | Usage |
|-----------|-------|-------|
| Unit | Vitest / Jest | Tests unitaires |
| Integration | Vitest + MSW | Tests d'intégration |
| E2E | Playwright | Tests bout en bout |
| Coverage | V8 / Istanbul | Couverture de code |
| Mocking | Vitest mocks | Mocking natif |
| API | MSW | Mocking d'API |
| Visual | Chromatic | Tests visuels |

---
name: architecture
description: "Principes d'architecture logicielle, design patterns, Clean Architecture, SOLID, et architecture microservices. Use when designing or reviewing software architecture, choosing a design pattern, refactoring toward Clean Architecture or SOLID, splitting a monolith, or when the user asks about architecture, patterns, microservices, couplage, couches ou separation des responsabilites."
version: 1.0.0
tags: [architecture, design-patterns, solid, clean-architecture, microservices]
---

# Software Architecture

Principes et patterns d'architecture pour des systèmes maintenables, scalables et robustes.

## Principes Fondamentaux

### SOLID

| Principe | Description | Exemple |
|----------|-------------|---------|
| **S** - Single Responsibility | Une classe = une responsabilité | `UserService` gère uniquement les utilisateurs |
| **O** - Open/Closed | Ouvert à l'extension, fermé à la modification | Utiliser des interfaces et des plugins |
| **L** - Liskov Substitution | Les sous-types doivent être substituables | `Admin` extends `User` sans casser le code |
| **I** - Interface Segregation | Petites interfaces spécifiques | `IReadable`, `IWritable` séparés |
| **D** - Dependency Inversion | Dépendre des abstractions, pas des concrets | Injecter `IUserRepository` pas `UserRepository` |

### Clean Architecture

```
┌─────────────────────────────────────────┐
│           Frameworks & Drivers          │  ← Outer Layer
├─────────────────────────────────────────┤
│         Interface Adapters              │
├─────────────────────────────────────────┤
│           Use Cases                     │
├─────────────────────────────────────────┤
│           Entities                      │  ← Inner Layer
└─────────────────────────────────────────┘
```

**Règles :**
- Les dépendances ne vont que vers l'intérieur
- Les entités ne dépendent de rien
- Les use cases dépendent uniquement des entités
- Les adapters dépendent des use cases

### Architecture en Couches

```
┌─────────────────────────────────────┐
│     Presentation Layer (UI)         │
├─────────────────────────────────────┤
│     Business Logic Layer            │
├─────────────────────────────────────┤
│     Data Access Layer               │
├─────────────────────────────────────┤
│     Database / External Services    │
└─────────────────────────────────────┘
```

## Design Patterns

### Création

| Pattern | Usage | Exemple |
|---------|-------|---------|
| **Singleton** | Instance unique | Database Connection |
| **Factory** | Créer des objets | `UserFactory.create('admin')` |
| **Builder** | Construction progressive | `new QueryBuilder().select().from().build()` |
| **Prototype** | Cloner des objets | `template.clone()` |

### Structure

| Pattern | Usage | Exemple |
|---------|-------|---------|
| **Adapter** | Interface incompatible | `ApiAdapter` adapte une API externe |
| **Decorator** | Ajouter des comportements | `@Cache`, `@Log` |
| **Facade** | Simplifier une interface | `UserService` cache la complexité |
| **Proxy** | Contrôle d'accès | `ProxyUser` avec cache |

### Comportement

| Pattern | Usage | Exemple |
|---------|-------|---------|
| **Strategy** | Algorithmes interchangeables | `PaymentStrategy` (Stripe, PayPal) |
| **Observer** | Notifications | `EventEmitter`, `EventBus` |
| **Command** | Encapsuler les actions | `CommandHandler` |
| **State** | États multiples | `OrderState` (pending, shipped, delivered) |

## Architecture Microservices

### Principes

1. **Bounded Contexts** : Chaque service a son domaine
2. **Database per Service** : Pas de base partagée
3. **API Gateway** : Point d'entrée unique
4. **Event-Driven** : Communication asynchrone
5. **Circuit Breaker** : Tolérance aux pannes

### Structure Type

```
services/
├── user-service/
│   ├── src/
│   │   ├── domain/        # Entités métier
│   │   ├── application/   # Use cases
│   │   ├── infrastructure/# Adapters externes
│   │   └── interfaces/    # API REST/GraphQL
│   ├── tests/
│   └── Dockerfile
├── order-service/
│   └── ...
└── api-gateway/
    └── ...
```

### Communication

| Méthode | Usage | Avantages |
|---------|-------|-----------|
| **REST** | CRUD simple | Standard, facile à tester |
| **GraphQL** | Données complexes | Flexibilité,少 over-fetching |
| **gRPC** | Performance | Protocole binaire, streaming |
| **Message Queue** | Événements | Découplage, resilience |

## Code Examples

### Repository Pattern

```typescript
// Interface (Domain Layer)
interface UserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  save(user: User): Promise<void>;
  delete(id: string): Promise<void>;
}

// Implémentation (Infrastructure Layer)
class PostgresUserRepository implements UserRepository {
  constructor(private db: Database) {}
  
  async findById(id: string): Promise<User | null> {
    const result = await this.db.query('SELECT * FROM users WHERE id = $1', [id]);
    return result.rows[0] ? this.toDomain(result.rows[0]) : null;
  }
  
  private toDomain(row: any): User {
    return { id: row.id, name: row.name, email: row.email };
  }
}
```

### Use Case Pattern

```typescript
// Use Case
class CreateUserUseCase {
  constructor(
    private userRepository: UserRepository,
    private emailService: EmailService
  ) {}
  
  async execute(input: CreateUserInput): Promise<CreateUserOutput> {
    // Validation
    if (await this.userRepository.findByEmail(input.email)) {
      throw new AppError('Email already exists', 'USER_EXISTS');
    }
    
    // Création
    const user = User.create(input);
    await this.userRepository.save(user);
    
    // Side effects
    await this.emailService.sendWelcome(user.email);
    
    return { userId: user.id };
  }
}

// Input/Output types
interface CreateUserInput {
  name: string;
  email: string;
  password: string;
}

interface CreateUserOutput {
  userId: string;
}
```

### Dependency Injection

```typescript
// Container (Composition Root)
const container = {
  // Repositories
  userRepository: new PostgresUserRepository(database),
  
  // Services
  emailService: new SendGridEmailService(config),
  
  // Use Cases
  createUserUseCase: new CreateUserUseCase(
    container.userRepository,
    container.emailService
  ),
};

// Usage
const result = await container.createUserUseCase.execute({
  name: 'John',
  email: 'john@example.com',
  password: 'securePassword'
});
```

## Checklist Architecture

- [ ] Les dépendances vont-elles dans le bon sens ?
- [ ] Chaque service a-t-il sa propre base de données ?
- [ ] Les interfaces sont-elles petites et précises ?
- [ ] La logique métier est-elle isolée des frameworks ?
- [ ] Les erreurs sont-elles gérées aux bonnes couches ?
- [ ] Le code est-il testable (DI, interfaces) ?
- [ ] La documentation d'architecture est-elle à jour ?

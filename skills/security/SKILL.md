---
name: security
description: "Sécurité applicative, OWASP Top 10, gestion des secrets, authentification, et bonnes pratiques de sécurité. Use when reviewing code for vulnerabilities, applying OWASP Top 10, handling authentication or authorization, managing secrets and tokens, auditing dependencies, or when the user mentions securite, vulnerabilite, XSS, injection, CSRF ou mot de passe."
version: 1.0.0
tags: [security, owasp, authentication, secrets, hardening]
---

# Application Security

Guide de sécurité applicative basé sur les standards OWASP et les bonnes pratiques de l'industrie.

## OWASP Top 10 (2021)

| # | Vulnérabilité | Prévention |
|---|---------------|------------|
| A01 | Broken Access Control | Vérifier les permissions côté serveur |
| A02 | Cryptographic Failures | Chiffrer les données sensibles |
| A03 | Injection | Utiliser des requêtes paramétrées |
| A04 | Insecure Design | Threat modeling dès la conception |
| A05 | Security Misconfiguration | Hardening par défaut |
| A06 | Vulnerable Components | Scanner les dépendances |
| A07 | Auth Failures | MFA, rate limiting |
| A08 | Data Integrity Failures | Vérifier les signatures |
| A09 | Logging Failures | Journaliser les événements sécurité |
| A10 | SSRF | Valider les URLs entrantes |

## Gestion des Secrets

### Mauvaise Pratique

```typescript
// ❌ JAMAIS de secrets en dur
const API_KEY = "sk-1234567890abcdef";
const DATABASE_URL = "postgresql://user:password@localhost:5432/db";
```

### Bonne Pratique

```typescript
// ✅ Variables d'environnement
const API_KEY = process.env.API_KEY;
const DATABASE_URL = process.env.DATABASE_URL;

// ✅ Validation au démarrage
function validateEnv(): void {
  const required = ['API_KEY', 'DATABASE_URL', 'JWT_SECRET'];
  for (const key of required) {
    if (!process.env[key]) {
      throw new Error(`Missing required environment variable: ${key}`);
    }
  }
}

// ✅ .env.example (commit sans les vraies valeurs)
// API_KEY=your-api-key-here
// DATABASE_URL=postgresql://user:password@localhost:5432/db
// JWT_SECRET=your-jwt-secret-here
```

### .gitignore

```gitignore
# Secrets
.env
.env.local
.env.production
*.pem
*.key

# Ne jamais committer
secrets/
credentials/
```

## Authentification

### JWT Sécurisé

```typescript
import jwt from 'jsonwebtoken';

// Génération
function generateTokens(user: User): { accessToken: string; refreshToken: string } {
  const accessToken = jwt.sign(
    { userId: user.id, role: user.role },
    process.env.JWT_SECRET!,
    { expiresIn: '15m' }  // Court délai
  );
  
  const refreshToken = jwt.sign(
    { userId: user.id },
    process.env.JWT_REFRESH_SECRET!,
    { expiresIn: '7d' }
  );
  
  return { accessToken, refreshToken };
}

// Vérification
function verifyToken(token: string): JwtPayload {
  try {
    return jwt.verify(token, process.env.JWT_SECRET!) as JwtPayload;
  } catch (error) {
    throw new AppError('Invalid token', 'AUTH_ERROR', 401);
  }
}
```

### Hashage des Mots de Passe

```typescript
import bcrypt from 'bcrypt';

const SALT_ROUNDS = 12;

async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}
```

### Rate Limiting

```typescript
import rateLimit from 'express-rate-limit';

// Global
const globalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requêtes par fenêtre
  message: 'Too many requests'
});

// Login
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5, // 5 tentatives par fenêtre
  message: 'Too many login attempts'
});
```

## Validation des Entrées

```typescript
import { z } from 'zod';

// Schéma de validation
const UserSchema = z.object({
  name: z.string().min(2).max(100),
  email: z.string().email(),
  password: z.string().min(8).regex(
    /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/,
    'Password must contain uppercase, lowercase, number and special character'
  ),
  age: z.number().int().min(18).max(120).optional()
});

// Validation
function validateUser(input: unknown) {
  return UserSchema.parse(input);
}
```

## Injection Prevention

### SQL Injection

```typescript
// ❌ Vulnerable
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✅ Parametrized
const query = 'SELECT * FROM users WHERE id = $1';
const result = await db.query(query, [userId]);
```

### XSS Prevention

```typescript
// ❌ Vulnerable
element.innerHTML = userInput;

// ✅ Safe
element.textContent = userInput;

// ✅ Si HTML nécessaire, sanitizer
import DOMPurify from 'dompurify';
element.innerHTML = DOMPurify.sanitize(userInput);
```

### CSRF Protection

```typescript
import csurf from 'csurf';

const csrfProtection = csurf({ cookie: true });

// Ajouter le token CSRF aux formulaires
app.get('/form', csrfProtection, (req, res) => {
  res.render('form', { csrfToken: req.csrfToken() });
});

// Vérifier le token
app.post('/process', csrfProtection, (req, res) => {
  // Le token est vérifié automatiquement
});
```

## HTTPS & Headers de Sécurité

```typescript
import helmet from 'helmet';
import express from 'express';

const app = express();

// Headers de sécurité
app.use(helmet());

// Configuration CSP
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'unsafe-inline'"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", "data:", "https:"],
    connectSrc: ["'self'", "https://api.example.com"]
  }
}));

// HTTPS strict
app.use((req, res, next) => {
  if (req.header('x-forwarded-proto') !== 'https') {
    return res.redirect(301, `https://${req.header('host')}${req.url}`);
  }
  next();
});
```

## Audit de Sécurité

### Checklist

- [ ] Tous les secrets sont-ils dans des variables d'environnement ?
- [ ] Les dépendances sont-elles à jour (npm audit) ?
- [ ] Le code est-il validé ( 输入 validation) ?
- [ ] Les requêtes SQL sont-elles paramétrées ?
- [ ] Le HTTPS est-il forcé ?
- [ ] Les headers de sécurité sont-ils configurés ?
- [ ] Le rate limiting est-il en place ?
- [ ] Les logs de sécurité sont-ils configurés ?
- [ ] Le CORS est-il restreint ?
- [ ] Les fichiers sont-ils protégés contre l'accès non autorisé ?

### Outils

```bash
# Scanner les dépendances
npm audit
npm audit fix

# Scanner le code
npx eslint-plugin-security
npx semgrep --config auto

# Scanner Docker
trivy image myapp:latest
```

## Encryption

```typescript
import crypto from 'crypto';

const ALGORITHM = 'aes-256-gcm';
const IV_LENGTH = 16;
const TAG_LENGTH = 16;

function encrypt(text: string, secret: string): string {
  const iv = crypto.randomBytes(IV_LENGTH);
  const cipher = crypto.createCipher(ALGORITHM, secret);
  
  let encrypted = cipher.update(text, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  
  const tag = cipher.getAuthTag();
  
  return `${iv.toString('hex')}:${tag.toString('hex')}:${encrypted}`;
}

function decrypt(encryptedData: string, secret: string): string {
  const [ivHex, tagHex, encrypted] = encryptedData.split(':');
  
  const iv = Buffer.from(ivHex, 'hex');
  const tag = Buffer.from(tagHex, 'hex');
  const decipher = crypto.createDecipher(ALGORITHM, secret);
  
  decipher.setAuthTag(tag);
  
  let decrypted = decipher.update(encrypted, 'hex', 'utf8');
  decrypted += decipher.final('utf8');
  
  return decrypted;
}
```

## Response Security

```typescript
// Pas d'informations sensibles dans les erreurs
function handleError(error: Error, res: Response) {
  console.error(error); // Log complet côté serveur
  
  // Réponse générique côté client
  res.status(500).json({
    error: 'Internal server error',
    // ❌ Ne pas exposer
    // stack: error.stack,
    // message: error.message
  });
}

// Pas de détails sur l'existence d'un utilisateur
async function login(email: string, password: string) {
  const user = await findByEmail(email);
  
  if (!user || !await verifyPassword(password, user.passwordHash)) {
    // Même message pour les deux cas
    throw new AppError('Invalid credentials', 'AUTH_ERROR', 401);
  }
  
  return generateTokens(user);
}
```

## Checklist Déploiement Sécurisé

- [ ] Variables d'environnement configurées
- [ ] Secrets rotatés régulièrement
- [ ] HTTPS forcé
- [ ] Headers de sécurité actifs
- [ ] Rate limiting configuré
- [ ] Logs de sécurité centralisés
- [ ] Alertes de sécurité configurées
- [ ] Backup des données chiffré
- [ ] Accès restreint aux ressources
- [ ] Audit de sécurité planifié

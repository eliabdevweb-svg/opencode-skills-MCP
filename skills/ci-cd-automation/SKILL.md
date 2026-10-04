---
name: ci-cd-automation
description: "Intégration continue, déploiement continu, pipelines CI/CD, et automatisation des workflows DevOps. Use when creating or debugging a CI/CD pipeline, a GitHub Actions workflow, automated build, test or deployment, release automation, or when the user says pipeline, CI/CD, deploiement automatique ou integration continue."
version: 1.0.0
tags: [ci-cd, automation, devops, github-actions, deployment]
---

# CI/CD Automation

Guide complet pour l'intégration continue, le déploiement continu et l'automatisation DevOps.

## Pipeline CI/CD Standard

```
Code Push → Lint → Test → Build → Security Scan → Deploy Staging → E2E Tests → Deploy Production
```

## GitHub Actions

### Configuration de Base

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '20'
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  # Phase 1: Qualité du code
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Type check
        run: npm run typecheck
      
      - name: Format check
        run: npm run format:check

  # Phase 2: Tests
  test:
    runs-on: ubuntu-latest
    needs: quality
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Unit tests
        run: npm run test:unit -- --coverage
      
      - name: Integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/test
      
      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          files: ./coverage/lcov.info

  # Phase 3: Build
  build:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
      
      - name: Upload build artifact
        uses: actions/upload-artifact@v4
        with:
          name: build
          path: dist/

  # Phase 4: Security Scan
  security:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/checkout@v4
      
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'table'
          exit-code: '1'
          severity: 'CRITICAL,HIGH'

  # Phase 5: Deploy
  deploy:
    runs-on: ubuntu-latest
    needs: security
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy to production
        run: |
          echo "Deploying to production..."
          # Votre script de déploiement ici
```

### Workflows Utiles

#### Auto-Label PR

```yaml
name: Auto Label PR

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/labeler@v5
        with:
          configuration-path: .github/labeler.yml
```

#### Auto-Assign Reviewers

```yaml
name: Auto Assign Reviewers

on:
  pull_request:
    types: [opened]

jobs:
  assign:
    runs-on: ubuntu-latest
    steps:
      - uses: kentaro-m/auto-assign-action@v2
        with:
          assignees: ${{ github.actor }}
          reviewers: team-leads
```

#### Stale Issues

```yaml
name: Stale Issues

on:
  schedule:
    - cron: '0 0 * * *'

jobs:
  stale:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/stale@v9
        with:
          stale-issue-message: 'This issue is stale'
          stale-pr-message: 'This PR is stale'
          days-before-stale: 30
          days-before-close: 7
```

## Docker

### Dockerfile Optimisé

```dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:20-alpine AS production
WORKDIR /app
RUN addgroup -g 1001 -S nodejs && adduser -S nextjs -u 1001
COPY --from=builder --chown=nextjs:nodejs /app/dist ./dist
COPY --from=builder --chown=nextjs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nextjs:nodejs /app/package.json ./package.json
USER nextjs
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

### Docker Compose

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://postgres:password@db:5432/myapp
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

## Deployment Strategies

### Blue-Green Deployment

```yaml
# Déploiement Blue-Green
deploy-blue:
  runs-on: ubuntu-latest
  steps:
    - name: Deploy to blue environment
      run: |
        # Déployer la nouvelle version sur blue
        kubectl set image deployment/app-blue app=myapp:${{ github.sha }}
        
    - name: Run smoke tests
      run: |
        # Tests sur blue
        curl -f https://blue.example.com/health
        
    - name: Switch traffic to blue
      run: |
        # Basculer le traffic
        kubectl patch service/app -p '{"spec":{"selector":{"version":"blue"}}}'
```

### Canary Deployment

```yaml
# Déploiement Canary
deploy-canary:
  runs-on: ubuntu-latest
  steps:
    - name: Deploy canary (10% traffic)
      run: |
        # Déployer 10% du traffic
        kubectl apply -f canary-deployment.yaml
        
    - name: Monitor metrics
      run: |
        # Surveiller les métriques
        sleep 300
        # Vérifier les erreurs
```

## Monitoring & Alerting

### Prometheus + Grafana

```yaml
# docker-compose.monitoring.yml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"

  grafana:
    image: grafana/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    ports:
      - "3001:3000"
    volumes:
      - grafana_data:/var/lib/grafana

  alertmanager:
    image: prom/alertmanager
    volumes:
      - ./alertmanager.yml:/etc/alertmanager/alertmanager.yml
    ports:
      - "9093:9093"

volumes:
  grafana_data:
```

## Checklist CI/CD

- [ ] Le pipeline se déclenche-t-il sur chaque PR ?
- [ ] Les tests sont-ils exécutés avant le merge ?
- [ ] Le code est-il linté et formaté ?
- [ ] La sécurité est-elle vérifiée (SAST, dependency scan) ?
- [ ] Le build est-il déployé en staging ?
- [ ] Les tests E2E sont-ils exécutés ?
- [ ] Le déploiement en production est-il automatisé ?
- [ ] Les alertes sont-elles configurées ?
- [ ] Le rollback est-il possible ?
- [ ] Les logs sont-ils centralisés ?

## Outils

| Catégorie | Outil | Usage |
|-----------|-------|-------|
| CI/CD | GitHub Actions | Pipeline d'intégration |
| Container | Docker | Conteneurisation |
| Orchestration | Kubernetes | Orchestration containers |
| IaC | Terraform | Infrastructure as Code |
| Monitoring | Prometheus + Grafana | Métriques et dashboards |
| Alerting | PagerDuty / Slack | Notifications |
| Secrets | Vault / AWS Secrets | Gestion des secrets |

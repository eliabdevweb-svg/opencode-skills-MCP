---
name: saas-system-design
description: "System design et architecture SaaS modernes en 2026. Patterns AI-Native, Serverless, Data Mesh, Agentic Architecture, multi-tenancy, scalabilité, FinOps. Use when designing, building, or reviewing SaaS architecture, scalability patterns, tenant isolation, or cloud-native systems."
version: 1.0.0
tags: [saas, architecture, system-design, cloud, multi-tenant, ai-native, serverless]
---

# SaaS System Design

Architecture et system design pour les plateformes SaaS modernes (2026). Couvre les piliers AI-Native, Serverless, Data Mesh, et Agentic Architecture.

## When to Use

| Scenario | Trigger Examples |
|----------|-----------------|
| **Architecture SaaS** | "Design a SaaS platform", "Architecture multi-tenant" |
| **Scalabilité** | "How to scale SaaS", "Handle 10x users" |
| **Multi-tenancy** | "Tenant isolation", "Shared vs dedicated resources" |
| **AI Integration** | "AI-native architecture", "Agentic workflows" |
| **Cloud Design** | "Serverless SaaS", "Event-driven architecture" |
| **Cost Optimization** | "FinOps SaaS", "Cost-efficient scaling" |
| **Data Architecture** | "Data mesh SaaS", "Tenant data partitioning" |

---

## Les 4 Piliers du SaaS en 2026

### Pillar 1: AI-Native First

L'IA n'est plus une application séparée — c'est une couche intégrée dans l'infrastructure.

```
┌─────────────────────────────────────────────────┐
│                AI-Native Layer                   │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────┐ │
│  │ LLM Router  │  │ Agent Exec  │  │ RAG     │ │
│  └─────────────┘  └─────────────┘  └─────────┘ │
├─────────────────────────────────────────────────┤
│              Core Services                      │
├─────────────────────────────────────────────────┤
│              Infrastructure                     │
└─────────────────────────────────────────────────┘
```

**Principes :**
- Embed AI dans chaque service, pas comme un feature séparé
- Utiliser des LLMs comme composants d'infrastructure
- Design pour l'incertitude (outputs non-déterministes)
- Gérer les coûts GPU/token comme un paramètre architectural

**Patterns :**
| Pattern | Usage | Complexe |
|---------|-------|----------|
| LLM Router | Diriger les requêtes vers le bon modèle | Faible |
| Agent Pipeline | Workflows multi-étapes autonomes | Moyenne |
| RAG Layer | Retrieval-Augmented Generation | Moyenne |
| Vector Store | Stockage de embeddings sémantiques | Faible |

### Pillar 2: Serverless & FinOps

Architecture pay-per-use alignée avec les objectifs business.

**Avantages :**
- Zero infrastructure management
- Scale automatique (y compris zéro)
- Coûts proportionnels à l'usage
- Déploiement simplifié

**Patterns Serverless :**
```
Request → API Gateway → Lambda/Function → DynamoDB/Neon
                    ↓
              Step Functions (orchestration)
                    ↓
              SQS/SNS (async)
```

**FinOps Rules :**
| Règle | Action |
|-------|--------|
| Cost-per-tenant | Tracker les coûts par tenant |
| Auto-scaling guards | Limiter les costs spikes |
| Reserved capacity | Prévoir pour les tenants stables |
| Spot instances | Workloads non-critiques |

### Pillar 3: Data Mesh

Décentralisation des données avec ownership par domaine.

```
┌─────────────────────────────────────────────────┐
│                  Data Mesh                       │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │ Domain A │  │ Domain B │  │ Domain C │      │
│  │ (Users)  │  │ (Orders) │  │ (Billing)│      │
│  │ Own Data │  │ Own Data │  │ Own Data │      │
│  └──────────┘  └──────────┘  └──────────┘      │
│        ↓              ↓              ↓          │
│  ┌─────────────────────────────────────────┐    │
│  │     Self-Serve Data Platform            │    │
│  └─────────────────────────────────────────┘    │
└─────────────────────────────────────────────────┘
```

**Principes :**
1. **Domain Ownership** : Chaque équipe possède ses données
2. **Data as Product** : Les données sont traitées comme des produits
3. **Self-Serve** : Plateforme de données autoservice
4. **Federated Governance** : Gouvernance décentralisée

**Pour le SaaS :**
- Séparation des données par tenant au niveau domaine
- Chaque service gère sa propre stratégie de partitionnement
- Les données partagées passent par des APIs, pas des bases communes

### Pillar 4: Agentic Architecture

Agents autonomes exécutant des workflows multi-étapes.

```
┌─────────────────────────────────────────────────┐
│              Agentic SaaS Architecture           │
│                                                  │
│  ┌────────────┐     ┌────────────────────┐      │
│  │  User      │────▶│  Agent Orchestrator│      │
│  │  Request   │     │  (LLM + Tools)     │      │
│  └────────────┘     └────────────────────┘      │
│                              │                   │
│              ┌───────────────┼───────────────┐   │
│              ▼               ▼               ▼   │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────┐│
│  │ Tool A       │ │ Tool B       │ │ Tool C   ││
│  │ (API Call)   │ │ (DB Query)   │ │ (Email)  ││
│  └──────────────┘ └──────────────┘ └──────────┘│
└─────────────────────────────────────────────────┘
```

**Caractéristiques :**
- Workflows autonomes sans intervention humaine
- Self-correction et retry logic
- Tool-calling pipelines
- Context preservation across sessions

**Architecture Requirements :**
| Composant | Purpose |
|-----------|---------|
| Event Bus | Communication asynchrone entre agents |
| Vector Store | Mémoire sémantique des conversations |
| Tool Registry | Catalogue des outils disponibles |
| Execution Sandbox | Isolation par tenant |
| State Manager | Persistance d'état des workflows |

---

## Multi-Tenancy Patterns

### Modèle Silo vs Pooled

```
SILO (Dédié)                    POOLED (Partagé)
┌─────────────────────┐        ┌─────────────────────┐
│ Tenant A            │        │   Shared Resources   │
│ ┌─────┐ ┌─────┐    │        │  ┌─────┐ ┌─────┐    │
│ │ DB  │ │ API │    │        │  │ DB  │ │ API │    │
│ └─────┘ └─────┘    │        │  └─────┘ └─────┘    │
├─────────────────────┤        │  ┌─────┐ ┌─────┐    │
│ Tenant B            │        │  │ DB  │ │ API │    │
│ ┌─────┐ ┌─────┐    │        │  └─────┘ └─────┘    │
│ │ DB  │ │ API │    │        └─────────────────────┘
│ └─────┘ └─────┘    │        Tenant isolation via
└─────────────────────┘        row-level security
Resources dédiées
```

### Choix du Pattern

| Critère | Silo | Pooled | Hybrid |
|---------|------|--------|--------|
| **Coût** | Élevé | Faible | Moyen |
| **Isolation** | Forte | Standard | Configurable |
| **Scalabilité** | Limitée | Excellente | Bonne |
| **Compliance** | Facile | Complexe | Variable |
| **Complexité** | Faible | Moyenne | Élevée |

### Isolation Strategy Stack

```
┌─────────────────────────────────────────┐
│         Application Layer               │
│    (Feature flags, Tenant context)      │
├─────────────────────────────────────────┤
│         Service Layer                   │
│    (Per-tenant routing, Rate limiting)  │
├─────────────────────────────────────────┤
│         Data Layer                      │
│    (Row-level security, Encryption)     │
├─────────────────────────────────────────┤
│         Infrastructure Layer            │
│    (VPC, Subnets, Resource isolation)   │
└─────────────────────────────────────────┘
```

---

## Core SaaS Services

### Control Plane vs Application Plane

```
┌─────────────────────────────────────────────────┐
│               CONTROL PLANE                     │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐        │
│  │ Tenant   │ │ Identity │ │ Billing  │        │
│  │ Manager  │ │ Service  │ │ Service  │        │
│  └──────────┘ └──────────┘ └──────────┘        │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐        │
│  │ Metrics  │ │ Onboard  │ │ Admin    │        │
│  │ Service  │ │ Service  │ │ Console  │        │
│  └──────────┘ └──────────┘ └──────────┘        │
├─────────────────────────────────────────────────┤
│               APPLICATION PLANE                 │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐        │
│  │ Feature  │ │ Feature  │ │ Feature  │        │
│  │ Service A│ │ Service B│ │ Service C│        │
│  └──────────┘ └──────────┘ └──────────┘        │
└─────────────────────────────────────────────────┘
```

### Service Decomposition Guide

| Service | Responsabilité | Pattern |
|---------|---------------|---------|
| **Tenant Service** | Policies, attributes, state | Centralized |
| **Identity Service** | AuthN/AuthZ, tenant context | Per-request |
| **Billing Service** | Usage tracking, invoicing | Event-driven |
| **Metrics Service** | Tenant analytics, monitoring | Async |
| **Onboarding Service** | Tenant provisioning | Workflow |

---

## Scalability Patterns

### Horizontal Scaling

```
                    ┌─────────────┐
                    │   Load      │
                    │  Balancer   │
                    └──────┬──────┘
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
      ┌──────────┐  ┌──────────┐  ┌──────────┐
      │ Instance │  │ Instance │  │ Instance │
      │    1     │  │    2     │  │    N     │
      └──────────┘  └──────────┘  └──────────┘
            │              │              │
      ┌─────────────────────────────────────────┐
      │         Shared State (Redis/DDB)        │
      └─────────────────────────────────────────┘
```

**Key Principles:**
- Stateless services (session dans Redis/DDB)
- Database read replicas
- Connection pooling
- Circuit breaker pattern

### Auto-Scaling Strategy

| Métrique | Threshold | Action |
|----------|-----------|--------|
| CPU | > 70% | Scale up |
| Memory | > 80% | Scale up |
| Queue Depth | > 100 | Add workers |
| Latency P99 | > 500ms | Scale up |
| Error Rate | > 5% | Alert + Scale |

---

## Data Partitioning

### Strategies

| Strategy | Description | Use Case |
|----------|-------------|----------|
| **Database per Tenant** | Isolation totale | Enterprise, Compliance |
| **Schema per Tenant** | Schema dédié | Multi-tenant standard |
| **Row-level Security** | Filtrage par tenant_id | Cost-effective |
| **Hybrid** | Combinaison | Tiered tenants |

### Row-Level Security Example (PostgreSQL)

```sql
-- Enable RLS
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- Policy: tenant isolation
CREATE POLICY tenant_isolation ON documents
  USING (tenant_id = current_setting('app.current_tenant')::uuid);

-- Set tenant context per request
SET app.current_tenant = 'tenant-uuid-here';
```

---

## Event-Driven Architecture

### Event Bus Pattern

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│ Service A│────▶│  Event   │────▶│ Service B│
│ (Producer│     │   Bus    │     │(Consumer)│
└──────────┘     │(Kafka/   │     └──────────┘
                 │ SQS/     │
┌──────────┐     │ EventBridge)    ┌──────────┐
│ Service C│◀────│          │────▶│ Service D│
│(Consumer)│     └──────────┘     │(Consumer)│
└──────────┘                      └──────────┘
```

**Event Types:**
| Type | Example | Guarantee |
|------|---------|-----------|
| Domain Event | `user.created` | At-least-once |
| Integration | `payment.processed` | Exactly-once |
| Command | `send.email` | At-most-once |

---

## Observability Stack

### Three Pillars

```
┌─────────────────────────────────────────────────┐
│              OBSERVABILITY                       │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │  Logs    │  │ Metrics  │  │ Traces   │      │
│  │(What     │  │(How much)│  │(Where    │      │
│  │ happened)│  │          │  │ it went) │      │
│  └──────────┘  └──────────┘  └──────────┘      │
└─────────────────────────────────────────────────┘
```

**Tenant-Aware Metrics:**
| Metric | Purpose |
|--------|---------|
| `requests_per_tenant` | Usage tracking |
| `latency_p99_tenant` | Per-tenant performance |
| `cost_per_tenant` | FinOps |
| `error_rate_tenant` | Reliability |
| `feature_usage_tenant` | Product analytics |

---

## Security Checklist

- [ ] Tenant isolation testé (cross-tenant access attempts)
- [ ] Encryption at rest et in transit
- [ ] RBAC avec tenant context
- [ ] Rate limiting par tenant
- [ ] Audit logging pour chaque action
- [ ] Secret management (Vault/AWS Secrets Manager)
- [ ] Network isolation (VPC, Security Groups)
- [ ] Compliance checks (SOC2, GDPR, HIPAA)

---

## Architecture Decision Records Template

```markdown
# ADR-001: [Title]

## Status
Accepted | Proposed | Deprecated

## Context
What is the issue?

## Decision
What was decided?

## Consequences
### Positive
- ...

### Negative
- ...

## Alternatives Considered
- ...
```

---

## References

| Resource | URL |
|----------|-----|
| AWS SaaS Lens | https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/ |
| AWS SaaS Architecture Fundamentals | https://docs.aws.amazon.com/whitepapers/latest/saas-architecture-fundamentals/ |
| Data Mesh Principles | https://datamesh-principles.com/ |
| Agentic SaaS Architecture (2026) | https://medium.com/@emmaschmidt304/why-2026-is-the-year-of-agentic-saas |
| System Design Guide 2026 | https://dev.to/devin-rosario/the-complete-guide-to-system-design-in-2026 |

---

## Quick Reference: SaaS Architecture Checklist

- [ ] Multi-tenancy strategy defined (silo/pooled/hybrid)
- [ ] Tenant isolation at every layer
- [ ] Event-driven communication between services
- [ ] Auto-scaling configured with cost guards
- [ ] Observability with tenant-aware metrics
- [ ] FinOps: cost-per-tenant tracking
- [ ] AI-native integration (not bolted on)
- [ ] Data partitioning strategy documented
- [ ] Security: encryption, RBAC, audit logging
- [ ] Disaster recovery plan per tenant tier

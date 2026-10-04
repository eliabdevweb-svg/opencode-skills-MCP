---
name: documentation
description: "Redaction de documentation technique: README, docs API, guides, changelogs. Use when writing or improving technical documentation, README files, or developer guides."
---

# Documentation Skill

## Overview
Expert in creating comprehensive, maintainable documentation using docs-as-code principles, API reference generation, and technical writing best practices.

## Key Principles

### 1. Documentation Structure
```
docs/
â”œâ”€â”€ getting-started/
â”‚   â”œâ”€â”€ quickstart.md
â”‚   â”œâ”€â”€ installation.md
â”‚   â””â”€â”€ configuration.md
â”œâ”€â”€ guides/
â”‚   â”œâ”€â”€ tutorials/
â”‚   â”œâ”€â”€ how-to/
â”‚   â””â”€â”€ concepts/
â”œâ”€â”€ api-reference/
â”‚   â”œâ”€â”€ endpoints/
â”‚   â””â”€â”€ schemas/
â”œâ”€â”€ examples/
â””â”€â”€ changelog.md
```

### 2. README.md Best Practices
```markdown
# Project Name

[![Version](https://img.shields.io/badge/version-1.0.0-blue)]()
[![License](https://img.shields.io/badge/license-MIT-green)]()

> One-line description of the project

## Features
- Feature 1
- Feature 2

## Quick Start

### Prerequisites
- Node.js >= 18
- PostgreSQL 14+

### Installation
\```bash
npm install
\```

### Development
\```bash
npm run dev
\```

## Documentation
- [Full Documentation](https://docs.example.com)
- [API Reference](https://docs.example.com/api)

## Contributing
See [CONTRIBUTING.md](CONTRIBUTING.md)

## License
MIT
```

### 3. API Documentation
- Auto-generate from OpenAPI specs (Redoc, Swagger UI)
- Include request/response examples for every endpoint
- Document error codes and their meanings
- Provide SDK/code examples in multiple languages
- Interactive "Try It" functionality

### 4. Docs-as-Code Principles
- **Version Control**: All docs in Git alongside code
- **Single Source of Truth**: Avoid duplication
- **Automated**: CI/CD builds and deploys docs
- **Reviewable**: PR process for doc changes
- **Testable**: Code examples should work

### 5. Writing Style
- **Concise**: Short sentences, active voice
- **Structured**: Use headings, lists, code blocks
- **Action-Oriented**: "Run `npm install`" not "You should run..."
- **Consistent**: Follow a style guide (Google Developer Documentation Style Guide)

### 6. Tutorials vs How-To vs Reference vs Explanation
| Type | Purpose | Example |
|------|---------|---------|
| **Tutorial** | Learning-oriented, step-by-step | "Build a blog in 30 minutes" |
| **How-To** | Task-oriented, solve a problem | "How to deploy to AWS" |
| **Reference** | Information-oriented, complete | "API endpoint documentation" |
| **Explanation** | Understanding-oriented, context | "Why we chose PostgreSQL" |

### 7. Changelog Management
```markdown
# Changelog

## [1.2.0] - 2026-09-08

### Added
- New dashboard analytics endpoint
- Batch import feature

### Changed
- Improved query performance by 40%

### Fixed
- Fixed pagination bug in user list

### Removed
- Deprecated legacy API v1 endpoints

## [1.1.0] - 2026-08-15
...
```

### 8. Documentation Tools
- **Static Site Generators**: Mintlify, Docusaurus, VitePress, Nextra
- **API Docs**: Redoc, Stoplight Elements, Swagger UI
- **Diagrams**: Mermaid, PlantUML, Excalidraw
- **Spell Check**: Vale, markdownlint

### 9. Comments & Code Documentation
```typescript
/**
 * Calculates the total price including tax
 * @param items - Array of cart items
 * @param taxRate - Tax rate as decimal (e.g., 0.1 for 10%)
 * @returns Total price in cents
 */
function calculateTotal(items: CartItem[], taxRate: number): number {
  const subtotal = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
  return Math.round(subtotal * (1 + taxRate));
}
```

## Documentation Checklist
- [ ] README with quick start
- [ ] API reference with examples
- [ ] At least one getting-started tutorial
- [ ] Changelog following Keep a Changelog format
- [ ] Contributing guidelines
- [ ] Code of conduct
- [ ] License file

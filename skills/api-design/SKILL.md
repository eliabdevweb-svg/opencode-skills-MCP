---
name: api-design
description: "Design d'APIs REST/GraphQL: conventions de routes, versioning, pagination, gestion d'erreurs, contrats et documentation. Use when designing or reviewing API endpoints, REST conventions, GraphQL schemas, or API docs."
---

# API Design Skill

## Overview
Expert in designing clean, consistent, and scalable APIs using REST, GraphQL, and OpenAPI 3.1 specifications. Applies industry best practices for versioning, pagination, error handling, and documentation.

## Key Principles

### 1. REST API Design
- **Resource Naming**: Use nouns, not verbs (`/users`, not `/getUsers`)
- **HTTP Methods**: GET (read), POST (create), PUT/PATCH (update), DELETE (remove)
- **Status Codes**: 200 (OK), 201 (Created), 204 (No Content), 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found), 409 (Conflict), 422 (Unprocessable Entity), 500 (Internal Server Error)

### 2. API Versioning
- **URL Path Versioning**: `/api/v1/users` (simple, explicit)
- **Header Versioning**: `Accept: application/vnd.api+json;version=1` (cleaner URLs)
- **Query Parameter**: `/users?version=1` (easy to test)

### 3. Request/Response Patterns
```json
// Success Response
{
  "data": { ... },
  "meta": { "timestamp": "2026-09-08T12:00:00Z" }
}

// Error Response
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": [
      { "field": "email", "message": "Must be a valid email address" }
    ]
  }
}
```

### 4. Pagination
```json
// Cursor-based (preferred for large datasets)
{
  "data": [...],
  "pagination": {
    "cursor": "eyJpZCI6MTAwfQ==",
    "hasMore": true,
    "limit": 20
  }
}

// Offset-based
{
  "data": [...],
  "pagination": {
    "total": 150,
    "page": 1,
    "perPage": 20,
    "totalPages": 8
  }
}
```

### 5. OpenAPI 3.1 Specification
```yaml
openapi: 3.1.0
info:
  title: User API
  version: 1.0.0
paths:
  /users:
    get:
      summary: List users
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserList'
```

### 6. GraphQL Best Practices
- Use **Queries** for reads, **Mutations** for writes
- Implement **N+1 query prevention** with DataLoader
- Use **Connection** pattern for pagination (edges/nodes/cursors)
- Define **Input Types** for mutations
- Use **Fragments** for reusable field sets

### 7. Authentication & Security
- Use **JWT tokens** with short expiration
- Implement **OAuth 2.0** for third-party access
- Rate limiting per API key/IP
- CORS configuration for browser clients
- Input validation and sanitization

### 8. Documentation
- Auto-generate from OpenAPI specs (Redoc, Swagger UI)
- Include code examples in multiple languages
- Document rate limits and quotas
- Provide SDK/client libraries when possible

## Response Format Standards

### JSON:API Specification
```json
{
  "data": {
    "type": "users",
    "id": "1",
    "attributes": {
      "name": "John Doe",
      "email": "john@example.com"
    },
    "relationships": {
      "posts": {
        "data": [{ "type": "posts", "id": "1" }]
      }
    }
  }
}
```

## Rate Limiting
```http
HTTP/1.1 429 Too Many Requests
Retry-After: 60
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1694198400
```

## Tools & Libraries
- **OpenAPI**: Swagger Editor, Redocly, Stoplight
- **Testing**: Postman, Insomnia, curl
- **Mocking**: Prism (OpenAPI mock server)
- **Validation**: Zod (TypeScript), Joi, Yup

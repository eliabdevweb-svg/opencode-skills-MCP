---
name: database
description: "Conception et optimisation de bases de donnees: modelisation, index, migrations, ORM, SQL et NoSQL. Use when designing schemas, writing queries, choosing indexes, or working with SQL/NoSQL databases."
---

# Database Skill

## Overview
Expert in database design, optimization, indexing, migrations, and ORM best practices for SQL (PostgreSQL, MySQL) and NoSQL (MongoDB, Redis) databases.

## Key Principles

### 1. Schema Design
- **Normalization**: 3NF for most use cases (reduce redundancy)
- **Denormalization**: Strategic for read-heavy workloads
- **Primary Keys**: Use UUIDs or ULIDs for distributed systems, auto-increment for single DB
- **Foreign Keys**: Always enforce referential integrity
- **Timestamps**: created_at, updated_at, deleted_at (soft deletes)

### 2. Indexing Strategy
```sql
-- B-tree index for equality/range queries
CREATE INDEX idx_users_email ON users(email);

-- Composite index (column order matters)
CREATE INDEX idx_orders_user_status ON orders(user_id, status);

-- Partial index (PostgreSQL)
CREATE INDEX idx_active_users ON users(email) WHERE deleted_at IS NULL;

-- Covering index (includes all needed columns)
CREATE INDEX idx_products_category ON products(category_id) INCLUDE (name, price);
```

**Index Rules:**
- Index columns used in WHERE, JOIN, ORDER BY
- Avoid over-indexing (slows writes)
- Monitor slow queries to identify missing indexes
- Use EXPLAIN ANALYZE to verify index usage

### 3. Query Optimization
```sql
-- Bad: SELECT * (fetches unnecessary data)
SELECT * FROM users WHERE status = 'active';

-- Good: Select specific columns
SELECT id, name, email FROM users WHERE status = 'active';

-- Bad: N+1 queries
SELECT * FROM posts;
-- Then for each post: SELECT * FROM users WHERE id = post.user_id;

-- Good: JOIN
SELECT p.*, u.name as author_name
FROM posts p
JOIN users u ON u.id = p.user_id;

-- Pagination with cursor (avoid OFFSET for large datasets)
SELECT * FROM posts
WHERE id > :last_id
ORDER BY id ASC
LIMIT 20;
```

### 4. Migrations Best Practices
- **Version Control**: Every schema change is a migration file
- **Reversible**: Every migration should have an `up` and `down`
- **Backward Compatible**: Don't break existing queries
- **Tested**: Run migrations on staging before production
- **Zero Downtime**: Use expand/contract pattern

```sql
-- Expand phase: Add new column, keep old
ALTER TABLE users ADD COLUMN display_name VARCHAR(255);
UPDATE users SET display_name = name;

-- Contract phase: Remove old column (after deploy)
ALTER TABLE users DROP COLUMN name;
```

### 5. Connection Pooling
- Use connection pools (PgBouncer, HikariCP)
- Pool size = (CPU cores * 2) + disk spindles for writes
- Monitor connection usage and leaks
- Set appropriate timeouts

### 6. ORM Best Practices (Prisma, Drizzle, TypeORM)
```typescript
// Prisma: Avoid N+1 with include
const users = await prisma.user.findMany({
  include: {
    posts: true,
    profile: true
  }
});

// Use transactions for multi-table operations
await prisma.$transaction([
  prisma.post.update({ where: { id }, data: { published: true } }),
  prisma.activity.create({ data: { action: 'publish', postId: id } })
]);
```

### 7. Caching Strategies
- **Cache-Aside**: App checks cache first, loads from DB on miss
- **Write-Through**: Write to cache and DB simultaneously
- **Write-Behind**: Write to cache, async write to DB
- **TTL**: Set appropriate expiration times

### 8. NoSQL Patterns (MongoDB)
```javascript
// Embedded documents for 1:1 or 1:few relationships
{
  _id: ObjectId("..."),
  name: "John",
  address: { street: "123 Main", city: "NYC" }
}

// Referenced documents for 1:many or many:many
{
  _id: ObjectId("..."),
  userId: ObjectId("..."),
  postId: ObjectId("...")
}
```

### 9. Backup & Recovery
- Automated daily backups with point-in-time recovery
- Test restore procedures regularly
- Off-site backup storage
- Document recovery procedures

## Tools
- **Migrations**: Prisma Migrate, Knex, Flyway, Alembic
- **Monitoring**: pg_stat_statements, slow query logs
- **GUI**: pgAdmin, DBeaver, TablePlus
- **Profiling**: EXPLAIN ANALYZE, SHOW PROFILE

---
name: db-migrator
description: Database migration patterns, schema conventions, naming standards, and migration workflows for this project. Trigger for "migration", "schema", "database", "table", "column", "index", "foreign key", "migrate", "rollback", "seed".
---

# Database Migrator

Defines **this project's database migration conventions**. Use when creating migrations, modifying schema, or answering "how do we handle DB changes?"

## Migration Tool

| Tool | Command |
|------|---------|
| **Prisma** | `npx prisma migrate dev --name <name>` |
| **Laravel** | `php artisan make:migration <name>` |
| **Drizzle** | `npx drizzle-kit generate` |
| **SQLAlchemy/Alembic** | `alembic revision --autogenerate -m "<name>"` |
| **Node-pg-migrate** | `npx node-pg-migrate <name> --create` |

## Naming Conventions

### Migration Files
```
<timestamp>_<action>_<table>_<description>
# Examples:
20240115_143000_create_users_table
20240115_143500_add_email_verified_at_to_users
20240115_144000_create_index_on_users_email
20240115_144500_drop_obsolete_columns_from_orders
```

### Tables
- **Plural, snake_case**: `users`, `order_items`, `user_profiles`
- **Pivot tables**: `alphabetical_singular` → `role_user`, `post_tag`

### Columns
- **snake_case**: `first_name`, `created_at`, `is_active`
- **Foreign keys**: `<singular>_id` → `user_id`, `order_id`
- **Booleans**: `is_<condition>`, `has_<relation>`, `should_<action>`
- **Timestamps**: `created_at`, `updated_at`, `deleted_at` (soft deletes)
- **Enums**: `<table>_<column>_enum` → `order_status_enum`

### Indexes
- **Format**: `idx_<table>_<column(s)>`
- **Unique**: `uq_<table>_<column(s)>`
- **Foreign keys**: `fk_<table>_<referenced_table>`

## Schema Patterns

### Standard Columns (every table)
```sql
id              BIGSERIAL PRIMARY KEY,  -- or UUID
created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
deleted_at      TIMESTAMPTZ,            -- if soft deletes
```

### Common Column Types
| Purpose | Type | Example |
|---------|------|---------|
| Primary Key | `BIGSERIAL` / `UUID` | `id BIGSERIAL` |
| Foreign Key | `BIGINT` / `UUID` | `user_id BIGINT REFERENCES users(id)` |
| Email | `VARCHAR(255) UNIQUE` | `email VARCHAR(255) UNIQUE` |
| Slug | `VARCHAR(255) UNIQUE` | `slug VARCHAR(255) UNIQUE` |
| Status | `VARCHAR(50)` + CHECK | `status VARCHAR(50) CHECK (status IN (...))` |
| JSON | `JSONB` | `metadata JSONB DEFAULT '{}'` |
| Array | `TEXT[]` / `INT[]` | `tags TEXT[]` |

### Indexes to Add by Default
```sql
-- Foreign keys (auto in some ORMs)
CREATE INDEX idx_posts_user_id ON posts(user_id);

-- Common query columns
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_status_created ON orders(status, created_at DESC);

-- Partial indexes for soft deletes
CREATE INDEX idx_users_active ON users(email) WHERE deleted_at IS NULL;
```

## Migration Workflow

### 1. Create Migration
```bash
# Prisma
npx prisma migrate dev --name add_user_preferences

# Laravel
php artisan make:migration add_preferences_to_users_table --table=users
```

### 2. Write Migration (Up/Down)
```sql
-- Up
ALTER TABLE users ADD COLUMN preferences JSONB DEFAULT '{}';
CREATE INDEX idx_users_preferences_notifications ON users USING GIN ((preferences->'notifications'));

-- Down
DROP INDEX idx_users_preferences_notifications;
ALTER TABLE users DROP COLUMN preferences;
```

### 3. Test Migration
```bash
# Run migration
npx prisma migrate dev
# or
php artisan migrate

# Test rollback
npx prisma migrate reset  # careful - destroys data
# or
php artisan migrate:rollback
```

### 4. Deploy
```bash
# Production (Prisma)
npx prisma migrate deploy

# Production (Laravel)
php artisan migrate --force
```

## Seeding

### Seed Files
```
prisma/
├── seed.ts                 # Main seed entry
└── seeds/
    ├── users.ts
    ├── roles.ts
    └── permissions.ts
```

```typescript
// prisma/seed.ts
async function main() {
  await seedRoles()
  await seedPermissions()
  await seedAdminUser()
}

main().catch(e => { console.error(e); process.exit(1) })
```

### Run Seeds
```bash
# Prisma
npx prisma db seed

# Laravel
php artisan db:seed
```

## Safety Rules

| Rule | Enforcement |
|------|-------------|
| **Never edit production DB directly** | CI checks for migration files |
| **Always provide down migration** | Code review requirement |
| **No destructive changes without backup** | `DROP COLUMN`, `DROP TABLE` require approval |
| **Test rollback locally** | Required before merge |
| **Large tables: use concurrent/online migrations** | `pg_repack`, `pt-online-schema-change` |

## Common Patterns

### Adding a Column (Safe)
```sql
-- 1. Add nullable column
ALTER TABLE users ADD COLUMN phone VARCHAR(20);

-- 2. Backfill (separate migration or script)
UPDATE users SET phone = '+15550000000' WHERE phone IS NULL;

-- 3. Add NOT NULL constraint (separate migration)
ALTER TABLE users ALTER COLUMN phone SET NOT NULL;
```

### Renaming a Column (Safe)
```sql
-- 1. Add new column
ALTER TABLE users ADD COLUMN full_name VARCHAR(255);

-- 2. Backfill
UPDATE users SET full_name = first_name || ' ' || last_name;

-- 3. Switch reads (code deploy)

-- 4. Drop old (later migration)
ALTER TABLE users DROP COLUMN first_name, DROP COLUMN last_name;
```

### Enum Changes
```sql
-- Add value (safe)
ALTER TYPE order_status_enum ADD VALUE 'refunded';

-- Remove value (unsafe - must migrate data first)
-- 1. Migrate rows using old value
-- 2. ALTER TYPE ... DROP VALUE (requires PG 12+)
```

## CI/CD Checks

```yaml
# .github/workflows/db-migrations.yml
- name: Check migration reversibility
  run: |
    npx prisma migrate dev --name test_reversibility
    npx prisma migrate reset --force
    npx prisma migrate dev
```

---

**Customize**: Update the migration tool, naming patterns, and column types to match your ORM/database. Remove sections that don't apply (e.g., if you don't use soft deletes, remove `deleted_at` references).
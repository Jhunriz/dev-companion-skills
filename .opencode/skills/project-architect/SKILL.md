---
name: project-architect
description: Architectural guidance for this project's structure, conventions, and patterns. Trigger for "architecture", "structure", "how should I organize", "project conventions", "where does this go", "folder structure".
---

# Project Architect

Provides architectural guidance specific to **this project's stack and conventions**. Use when users ask about file organization, naming patterns, or "how we do things here".

## Stack Overview

| Layer | Technology | Version |
|-------|-----------|---------|
| **Framework** | [Your Framework] | [Version] |
| **Language** | [TypeScript/PHP/Python/etc] | [Version] |
| **Database** | [PostgreSQL/MySQL/MongoDB/etc] | [Version] |
| **ORM/Query** | [Prisma/Eloquent/Drizzle/SQLAlchemy/etc] | [Version] |
| **Auth** | [Clerk/NextAuth/Sanctum/etc] | [Version] |
| **Styling** | [Tailwind/CSS Modules/Styled Components] | [Version] |
| **Testing** | [Vitest/Jest/Pest/Pytest] | [Version] |

## Project Structure

```
project-root/
├── src/
│   ├── app/           # Routes/Pages (Next.js App Router, Laravel controllers, etc.)
│   ├── components/    # Shared UI components
│   ├── features/      # Feature-based modules (colocated: types, hooks, components, utils)
│   ├── lib/           # Shared utilities, configurations, clients
│   ├── server/        # Server-only code (API routes, tRPC routers, background jobs)
│   ├── types/         # Shared TypeScript types / PHP interfaces
│   └── hooks/         # Shared React hooks / composables
├── tests/             # Test files (mirror src structure)
├── prisma/            # Prisma schema & migrations (or migrations/ for Laravel)
└── public/            # Static assets
```

## Conventions

### File Naming
- **Components**: `PascalCase.tsx` / `PascalCase.php`
- **Utilities**: `camelCase.ts` / `snake_case.php`
- **Types**: `PascalCase.types.ts` or `types.ts` per feature
- **Tests**: `*.test.ts` / `*Test.php` alongside source or in `tests/`

### Import Patterns
```typescript
// Alias imports (configure in tsconfig/paths)
import { Button } from '@/components/ui/button'
import { useAuth } from '@/features/auth/hooks/useAuth'
import { db } from '@/lib/db'
```

### Feature Organization
Each feature in `src/features/<feature-name>/` contains:
```
features/<feature>/
├── components/    # Feature-specific UI
├── hooks/         # Feature-specific hooks
├── types.ts       # Feature types
├── utils.ts       # Feature utilities
├── api.ts         # API calls / tRPC procedures
└── index.ts       # Public exports
```

## Decision Guide

| Question | Answer |
|----------|--------|
| Where do new API routes go? | `src/server/api/routers/` or `src/app/api/` |
| Where do database models live? | `prisma/schema.prisma` or `src/models/` |
| How to add a new page? | `src/app/<route>/page.tsx` or Inertia page in `resources/js/Pages/` |
| Where are shared types? | `src/types/` or feature-specific `types.ts` |
| How to handle forms? | React Hook Form + Zod / Laravel Form Requests |
| Where do background jobs go? | `src/server/jobs/` or `app/Jobs/` |

## Quick Reference Commands

```bash
# Add new feature
mkdir -p src/features/new-feature/{components,hooks,types,utils}

# Generate migration (adapt to your stack)
npx prisma migrate dev --name add_new_table
# or
php artisan make:migration create_new_table

# Run tests
npm run test
# or
./vendor/bin/pest
```

---

**Customize this skill**: Replace the stack table, structure, and conventions with your project's actual setup. Delete sections that don't apply.
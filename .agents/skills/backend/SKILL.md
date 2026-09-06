---
name: backend
description: Backend development patterns, API design, database schema, server architecture, and authentication for this project. Trigger for "backend", "server", "API route", "database", "schema", "migration", "auth", "controller", "middleware", "server logic", "REST endpoint".
---

# Backend

Defines **this project's backend conventions**. Use for designing server-side logic, database models, API endpoints, or answering "how do we handle the server layer?"

## Server & Stack

| Aspect | Convention |
|--------|-----------|
| **Runtime** | Node.js / Next.js Server Components |
| **Language** | TypeScript |
| **Database** | PostgreSQL / MySQL |
| **ORM** | Prisma / Drizzle |
| **API** | Next.js App Router Routes / tRPC |
| **Auth** | NextAuth.js / Clerk |
| **Caching** | Redis (optional) |
| **Queue** | BullMQ / Resque |

## Database Conventions

### Schema Organization
```prisma
// prisma/schema.prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  role      Role     @default(USER)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Post {
  id        String   @id @default(cuid())
  title     String
  content   String?
  author    User     @relation(fields: [authorId], references: [id])
  authorId  String
  published Boolean  @default(false)
  createdAt DateTime @default(now())
}

enum Role {
  USER
  ADMIN
}
```

### Migration Rules
- **Always name migrations** descriptively: `create_user_table`, `add_post_published_column`
- **Never manually edit** the migration file after running
- **Test rollbacks** before committing
- **Seed data** in `prisma/seed.ts` or equivalent

## API Route Conventions

### Next.js App Router Routes
```
src/app/api/
├── users/
│   ├── route.ts          # GET /api/users, POST /api/users
│   ├── [id]/
│   │   ├── route.ts      # GET /api/users/:id, PATCH /api/users/:id, DELETE /api/users/:id
│   │   └── posts/
│   │       └── route.ts  # GET /api/users/:id/posts
```

### Route Handler Pattern
```typescript
// src/app/api/<resource>/route.ts
import { NextResponse } from 'next/server'
import { prisma } from '@/lib/prisma'
import { getServerSession } from 'next-auth'

export async function GET(request: Request) {
  const session = await getServerSession()
  if (!session) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })

  const resources = await prisma.resource.findMany({
    where: { userId: session.user.id },
    include: { related: true },
  })

  return NextResponse.json({ data: resources })
}

export async function POST(request: Request) {
  const body = await request.json()
  // Validate, create, return
}
```

### tRPC Routers (if applicable)
```typescript
// src/server/api/routers/<resource>.ts
export const resourceRouter = createTRPCRouter({
  list: protectedProcedure
    .input(listSchema)
    .query(async ({ ctx, input }) => { ... }),

  getById: protectedProcedure
    .input(z.object({ id: z.string() }))
    .query(async ({ ctx, input }) => { ... }),

  create: protectedProcedure
    .input(createSchema)
    .mutation(async ({ ctx, input }) => { ... }),
})
```

## Authentication & Authorization

| Layer | Method |
|-------|--------|
| **Authentication** | NextAuth.js / Clerk — session or JWT |
| **Authorization** | Role-based middleware in route handlers |
| **Session** | Server-side via cookies or JWT |

### Middleware Pattern
```typescript
// src/middleware.ts or middleware function in route.ts
import { auth } from '@/lib/auth'

export const middleware = auth((req) => {
  if (!req.auth && req.nextUrl.pathname.startsWith('/api/protected')) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })
  }
})
```

## Service Layer Pattern

```typescript
// src/lib/services/<service>.ts
export class <Service>Service {
  constructor(private readonly repository: <Resource>Repository) {}

  async create(data: CreateDTO): Promise<Entity> {
    const validated = validateInput(data)
    return this.repository.create(validated)
  }

  async getById(id: string): Promise<Entity> {
    const entity = await this.repository.findById(id)
    if (!entity) throw new NotFoundError('<Resource>', id)
    return entity
  }
}
```

- **Separate business logic** from route handlers into service classes
- **Repository pattern** for database access
- **Validation at the boundary** — never trust incoming data

## Error Handling

```typescript
// src/lib/errors.ts
export class AppError extends Error {
  constructor(public statusCode: number, message: string) {
    super(message)
  }
}

export class ValidationError extends AppError {
  constructor(message: string) { super(400, message) }
}
export class NotFoundError extends AppError {
  constructor(entity: string, id: string) { super(404, `${entity} ${id} not found`) }
}
export class UnauthorizedError extends AppError {
  constructor() { super(401, 'Authentication required') }
}
```

## File Organization

```
src/
├── app/                    # App Router pages and API routes
├── server/                 # Server-only code
│   ├── api/               # tRPC routers
│   ├── auth/              # Auth configuration
│   └── middleware.ts      # Route middleware
├── lib/
│   ├── prisma/            # Prisma client and helpers
│   ├── errors.ts          # Error classes
│   ├── services/          # Business logic services
│   └── validators/        # Input validation schemas
├── types/                 # Shared type definitions
└── utils/                 # Shared utilities
```

## Security Guidelines

- **Never expose secrets** in client-side code
- **Validate all inputs** with Zod schemas at route boundaries
- **Sanitize database queries** — use ORM parameterized queries, never string interpolation
- **Rate limit** API endpoints
- **CORS** configured properly for frontend origins
- **HTTPS** enforced in production

## Quick Decisions

| Question | Answer |
|----------|--------|
| Where to add an API route? | `src/app/api/<resource>/route.ts` |
| Where to put business logic? | `src/lib/services/` |
| Where to define database models? | `prisma/schema.prisma` |
| How to handle auth? | NextAuth/Clerk in `src/server/auth/` |
| Where to put validation schemas? | `src/lib/validators/` or co-located with routes |
| How to handle errors? | `src/lib/errors.ts` custom error classes |
| Where to put server utilities? | `src/lib/` |

## Testing Checklist

- [ ] API routes return correct status codes
- [ ] Input validation rejects invalid data
- [ ] Auth middleware protects protected routes
- [ ] Database transactions roll back on failure
- [ ] Error responses have consistent format
- [ ] Rate limiting works on public endpoints

---

**Customize**: Replace Prisma with your ORM, adjust the API style (REST/tRPC), and update the auth provider to match your project.

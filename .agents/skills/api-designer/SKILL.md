---
name: api-designer
description: API design patterns, REST/tRPC/GraphQL conventions, endpoint structure, request/response formats, versioning, and error handling for this project. Trigger for "API", "endpoint", "route", "REST", "tRPC", "GraphQL", "API design", "request format", "response format".
---

# API Designer

Defines **this project's API conventions**. Use for designing new endpoints, reviewing API changes, or answering "how do we structure our API?"

## API Style

| Aspect | Convention |
|--------|-----------|
| **Protocol** | REST / tRPC / GraphQL |
| **Base Path** | `/api/v1` or `/trpc` |
| **Versioning** | URL path (`/v1/`) / Header / None |
| **Auth** | Bearer token / Session cookie / API key |
| **Content-Type** | `application/json` |

## REST Conventions (if applicable)

### Endpoint Naming
```
GET    /api/v1/resources           # List (with pagination, filtering)
POST   /api/v1/resources           # Create
GET    /api/v1/resources/{id}      # Read single
PATCH  /api/v1/resources/{id}      # Partial update
PUT    /api/v1/resources/{id}      # Full replace
DELETE /api/v1/resources/{id}      # Delete

# Nested resources
GET    /api/v1/users/{userId}/posts
POST   /api/v1/users/{userId}/posts
```

### Request/Response Format

**Success Response:**
```json
{
  "data": { ... } | [ ... ],
  "meta": { "page": 1, "perPage": 20, "total": 100 }
}
```

**Error Response:**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": { "field": ["error message"] }
  }
}
```

### Query Parameters
| Parameter | Purpose | Example |
|-----------|---------|---------|
| `page` | Pagination page | `?page=2` |
| `perPage` | Items per page | `?perPage=50` |
| `sort` | Sort field + direction | `?sort=-createdAt,name` |
| `filter[field]` | Filter by field | `?filter[status]=active` |
| `include` | Eager load relations | `?include=author,comments` |

### HTTP Status Codes
- `200` - Success (GET, PATCH, PUT)
- `201` - Created (POST)
- `204` - No Content (DELETE)
- `400` - Bad Request (validation)
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `422` - Unprocessable Entity (semantic validation)
- `500` - Server Error

## tRPC Conventions (if applicable)

### Router Structure
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
  
  update: protectedProcedure
    .input(updateSchema)
    .mutation(async ({ ctx, input }) => { ... }),
  
  delete: protectedProcedure
    .input(z.object({ id: z.string() }))
    .mutation(async ({ ctx, input }) => { ... }),
})
```

### Procedure Naming
- Queries: `list`, `getById`, `getBySlug`, `search`
- Mutations: `create`, `update`, `delete`, `archive`, `restore`

### Input Validation
- Use **Zod** schemas for all inputs
- Define schemas in `src/features/<feature>/schemas.ts`
- Reuse between client/server

## Error Handling

### Standard Error Codes
| Code | HTTP | Meaning |
|------|------|---------|
| `VALIDATION_ERROR` | 400/422 | Input validation failed |
| `UNAUTHENTICATED` | 401 | Missing/invalid auth |
| `FORBIDDEN` | 403 | Authenticated but not authorized |
| `NOT_FOUND` | 404 | Resource doesn't exist |
| `CONFLICT` | 409 | Resource conflict (duplicate, etc.) |
| `RATE_LIMITED` | 429 | Too many requests |
| `INTERNAL_ERROR` | 500 | Unexpected server error |

### Error Response Helper
```typescript
// src/lib/api/errors.ts
export function apiError(code: ErrorCode, message: string, details?: Record<string, string[]>) {
  return { error: { code, message, details } }
}
```

## Security

- **Rate limiting**: Apply to all public endpoints
- **Input sanitization**: Validate at boundary (Zod/Form Requests)
- **Authorization**: Check permissions in procedure/middleware
- **PII**: Never log sensitive data; redact in error responses

## Testing Checklist

- [ ] Happy path returns `200`/`201` with correct shape
- [ ] Validation errors return `400`/`422` with field details
- [ ] Unauthorized returns `401`
- [ ] Forbidden returns `403`
- [ ] Not found returns `404`
- [ ] Pagination works correctly
- [ ] Filtering/sorting works
- [ ] Rate limiting enforced

---

**Customize**: Delete REST or tRPC section based on your stack. Add GraphQL section if needed. Update base paths, auth method, and error codes to match your project.
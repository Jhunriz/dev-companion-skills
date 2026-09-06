---
name: frontend
description: Frontend development patterns, React component architecture, state management, routing, and performance optimization for this project. Trigger for "frontend", "component", "React", "UI layer", "state management", "routing", "frontend architecture", "hook", "client-side", "SPA".
---

# Frontend

Defines **this project's frontend conventions**. Use for designing components, structuring frontend architecture, or answering "how do we build the UI layer?"

## Framework & Stack

| Aspect | Convention |
|--------|-----------|
| **Framework** | React / Next.js |
| **Language** | TypeScript |
| **Styling** | Tailwind CSS |
| **Component Library** | shadcn/ui |
| **State Management** | Zustand / React Context / TanStack Query |
| **Routing** | App Router / File-based routing |
| **Forms** | React Hook Form + Zod |
| **Build Tool** | Vite / Next.js |

## Component Architecture

### Component Types
- **UI Components** (`components/ui/`): Reusable primitives (Button, Input, Dialog)
- **Feature Components** (`src/features/<feature>/components/`): Domain-specific UI
- **Layout Components** (`components/layout/`): Pages shells, headers, sidebars
- **Shared Components** (`components/shared/`): Cross-feature reusable UI

### Component Rules
- **PascalCase** for file names and component names
- **Colocate** related files (types, hooks, utils) within the feature folder
- **"use client"** directive for any component using browser APIs, hooks, or state
- **Avoid prop drilling** — use Context, Zustand, or server actions
- **Compose over inheritance** — build complex UIs from simple primitives

### File Structure per Feature
```
src/features/<feature>/
├── components/    # Feature-specific UI components
├── hooks/         # Custom hooks for this feature
├── api.ts         # API calls / data fetching
├── types.ts       # TypeScript types and interfaces
├── utils.ts       # Feature utilities
├── constants.ts   # Feature constants
└── index.ts       # Public exports
```

## State Management

| Pattern | Use When |
|---------|----------|
| **Server State** (TanStack Query) | Fetching from APIs, caching, mutations |
| **Client State** (Zustand) | UI state, forms, global non-server data |
| **React Context** | Theme, auth, locale — low-frequency updates |
| **URL State** | Filters, pagination, tab selection |

### Server State Pattern
```typescript
// src/features/<feature>/api.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

export function useResources() {
  return useQuery({
    queryKey: ['resources'],
    queryFn: async () => { /* fetch */ },
  })
}

export function useCreateResource() {
  const queryClient = useQueryClient()
  return useMutation({
    mutationFn: async (data: CreateResourceDTO) => { /* create */ },
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['resources'] }),
  })
}
```

### Client State Pattern
```typescript
// src/lib/store.ts
import { create } from 'zustand'

interface AppState {
  sidebarOpen: boolean
  setSidebarOpen: (open: boolean) => void
}

export const useAppStore = create<AppState>((set) => ({
  sidebarOpen: false,
  setSidebarOpen: (open) => set({ sidebarOpen: open }),
}))
```

## Routing Conventions

```
src/app/
├── layout.tsx         # Root layout
├── page.tsx           # Home page
├── (auth)/            # Route group
│   ├── login/page.tsx
│   └── register/page.tsx
├── (dashboard)/       # Protected routes
│   ├── dashboard/page.tsx
│   └── settings/page.tsx
└── api/               # API routes
```

- **Route groups** `()` for organizational separation without URL impact
- **Loading UI** — `loading.tsx` per route segment
- **Error handling** — `error.tsx` per route segment
- **Not found** — `not-found.tsx` per route segment

## Hooks Pattern

```typescript
// src/hooks/use-<feature>.ts
import { useState, useCallback, useEffect } from 'react'

export function use<Feature>(initialValue?: Type) {
  const [state, setState] = useState<Type>(initialValue)

  const action = useCallback(() => { /* ... */ }, [])

  useEffect(() => { /* side effects */ }, [])

  return { state, action }
}
```

- **Prefix hooks with `use`** and the feature name: `useAuth`, `useProjects`
- **Keep hooks small** — one hook per concern
- **Custom hooks** live in `src/hooks/` or feature-specific `hooks/` directories

## Performance Guidelines

| Strategy | When |
|----------|------|
| `React.memo` | Prevent re-renders of stable prop components |
| `useMemo` | Expensive computations |
| `useCallback` | Stable callback references for child components |
| Dynamic imports | Heavy components not above the fold |
| Image optimization | Next.js `<Image>` for all images |
| Code splitting | Route-level and feature-level lazy loading |

### Code Splitting
```typescript
const HeavyComponent = React.lazy(() => import('@/features/heavy/HeavyComponent'))

<Suspense fallback={<Skeleton />}>
  <HeavyComponent />
</Suspense>
```

## Access Patterns

```typescript
// Alias imports
import { Button } from '@/components/ui/button'
import { useAuth } from '@/features/auth/hooks/useAuth'
import { api } from '@/lib/api'
import { cn } from '@/lib/utils'
```

## Quick Decisions

| Question | Answer |
|----------|--------|
| Where to add a new page? | `src/app/<route>/page.tsx` |
| Where to add a new component? | `components/ui/` (shared) or `src/features/<name>/components/` (feature) |
| How to fetch data? | TanStack Query hooks in `api.ts` |
| How to manage global UI state? | Zustand store in `src/lib/store.ts` |
| Where to put custom hooks? | `src/hooks/` or feature-level `hooks/` |
| How to handle forms? | React Hook Form + Zod in `components/<form>.tsx` |
| How to style? | Tailwind utility classes, never custom CSS unless necessary |

## Testing Checklist

- [ ] Components render without errors
- [ ] User interactions work correctly
- [ ] Loading and error states handled
- [ ] Accessibility attributes present
- [ ] Responsive at breakpoints
- [ ] Server state mutations invalidate correctly

---

**Customize**: Replace stack details with your project's actual framework, libraries, and tooling. Adjust file structure to match your conventions.

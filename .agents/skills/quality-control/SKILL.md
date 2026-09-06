---
name: quality-control
description: Code quality standards, linting rules, code review practices, formatting, and technical debt management for this project. Trigger for "quality", "lint", "code review", "refactor", "code quality", "clean up", "technical debt", "best practices", "coding standards", "prettier".
---

# Quality Control

Defines **this project's code quality standards**. Use for linting, code reviews, refactoring, or answering "how do we maintain code quality?"

## Linting & Formatting

| Tool | Purpose |
|------|---------|
| **ESLint** | Code linting and error detection |
| **Prettier** | Code formatting |
| **TypeScript** | Type checking |
| **Husky** | Git hooks enforcement |

### ESLint Configuration
```javascript
// .eslintrc.json or eslint.config.js
export default [
  'eslint:recommended',
  'plugin:@typescript-eslint/recommended',
  'plugin:@typescript-eslint/recommended-requiring-type-checking',
  'plugin:react/recommended',
  'plugin:react-hooks/recommended',
  'prettier',  // Must be last
]
```

### Prettier Configuration
```javascript
// .prettierrc
module.exports = {
  semi: true,
  singleQuote: true,
  tabWidth: 2,
  trailingComma: 'es5',
  printWidth: 100,
  jsxSingleQuote: false,
  bracketSpacing: true,
  arrowParens: 'always',
}
```

### Pre-commit Hooks (Husky)
```json
// package.json scripts or .husky/pre-commit
npx lint-staged
```

```javascript
// lint-staged.config.js
export default {
  '*.{ts,tsx}': ['eslint --fix', 'prettier --write'],
  '*.{json,md,css}': ['prettier --write'],
}
```

## Code Review Standards

### Review Checklist
- [ ] **Type safety**: No `any`, proper types defined
- [ ] **Error handling**: All async operations have try/catch or error boundaries
- [ ] **Security**: No hardcoded secrets, input validated, SQL safe
- [ ] **Performance**: No unnecessary re-renders, efficient queries
- [ ] **Accessibility**: ARIA attributes, semantic HTML, keyboard navigable
- [ ] **Tests**: New code has accompanying tests
- [ ] **Documentation**: Complex logic is commented, APIs documented
- [ ] **Format**: Code passes lint and format checks
- [ ] **Imports**: No unused imports, proper aliasing

### Review Severity Levels
| Severity | Meaning | Action |
|----------|---------|--------|
| **Blocker** | Security, type errors, broken functionality | Must fix before merge |
| **Critical** | Performance issue, missing error handling | Should fix before merge |
| **Major** | Code smell, missing tests, unclear logic | Fix in follow-up |
| **Minor** | Style preference, naming | Nice to have |

## Refactoring Patterns

### Safe Refactoring Process
1. **Write characterization tests** before changing code
2. **Make small, atomic commits** — one refactor per PR
3. **Run full test suite** before and after
4. **Update documentation** if behavior changes
5. **Measure performance** impact if applicable

### Common Refactorings

#### Extract to Hook
```typescript
// Before: inline logic in component
function MyComponent() {
  const [data, setData] = useState(null)
  useEffect(() => { fetchData().then(setData) }, [])
  // ... more logic
}

// After: extracted hook
function useMyData() {
  const [data, setData] = useState(null)
  useEffect(() => { fetchData().then(setData) }, [])
  return data
}
```

#### Extract Service
```typescript
// Before: logic in route handler
app.post('/api/users', async (req, res) => {
  // validation, business logic, DB calls mixed
})

// After: service layer
const userService = new UserService(userRepo)
app.post('/api/users', async (req, res) => {
  const result = await userService.create(req.body)
})
```

#### Extract Validation Schema
```typescript
// Before: inline validation
if (!email || !email.includes('@')) return error

// After: Zod schema
const emailSchema = z.object({ email: z.string().email() })
const result = emailSchema.safeParse(req.body)
```

## TypeScript Guidelines

| Rule | Standard |
|------|----------|
| **No `any`** | Use `unknown` with type guards |
| **Strict mode** | Enable all strict TypeScript options |
| **Explicit returns** | Type function returns |
| **Interfaces over types** | For object shapes, prefer `interface` |
| **Type imports** | `import type { Foo } from '...'` |
| **Never `as`** | Use type guards or assertions only when safe |

### Type Safety Patterns
```typescript
// Good: explicit types
interface User {
  id: string
  email: string
}

// Good: type guard
function isUser(value: unknown): value is User {
  return typeof value === 'object' && value !== null && 'email' in value
}

// Bad: any
const data: any = fetchData()
```

## Technical Debt Management

| Priority | Action |
|----------|--------|
| **P0** | Security vulnerabilities — fix immediately |
| **P1** | Breaking changes, broken tests — fix this sprint |
| **P2** | Performance issues, code duplication — fix next sprint |
| **P3** | Naming, minor refactors — fix when convenient |

### Debt Tracking
- Comment `// TODO:` or `// TECHDEBT:` with context and tracking issue
- Maintain a `TECHDEBT.md` at project root
- Allocate 20% of sprint capacity to debt reduction

## Naming Conventions

| Element | Convention |
|---------|-----------|
| **Files** | `camelCase.ts` / `kebab-case.ts` |
| **Components** | `PascalCase` |
| **Functions** | `camelCase` |
| **Constants** | `UPPER_SNAKE_CASE` |
| **Types/Interfaces** | `PascalCase` |
| **Variables** | `camelCase` |
| **Tests** | `*.test.ts` |

## Quick Decisions

| Question | Answer |
|----------|--------|
| Where to configure linting? | Root `.eslintrc` / `eslint.config.js` |
| Where to configure formatting? | Root `.prettierrc` |
| How to enforce pre-commit? | Husky + lint-staged |
| How to handle a tech debt item? | Add to `TECHDEBT.md` with priority |
| When to refactor? | Never during feature development — separate refactor PR |
| What counts as a blocker? | Type errors, security issues, test failures |

---

**Customize**: Update tool configurations, add/remove rules based on your project's ESLint/Prettier setup, and adjust the review checklist to match your team's standards.

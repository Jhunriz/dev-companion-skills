---
name: tester
description: Testing strategies, QA practices, test automation, and quality assurance standards for this project. Trigger for "test", "testing", "QA", "quality assurance", "unit test", "integration test", "e2e", "test coverage", "test suite", "write tests", "bug", "regression", "automation".
---

# Tester

Defines **this project's testing strategy and QA standards**. Use for writing tests, designing test plans, reviewing test coverage, or answering "how do we ensure quality?"

## Testing Strategy

### Testing Pyramid
| Level | Scope | Tool | When to Write |
|-------|-------|------|---------------|
| **Unit** | Individual functions/components | Vitest / Jest | With every feature |
| **Integration** | Component interactions, API + DB | Vitest / Jest + MSW | For complex flows |
| **E2E** | Full user flows | Playwright / Cypress | Critical user journeys |
| **Visual** | UI snapshots | Storybook / Chromatic | Design system changes |

### Test Coverage Goals
| Type | Target |
|------|--------|
| **Unit tests** | 80%+ coverage per file |
| **Integration tests** | All critical paths |
| **E2E tests** | Core user flows |
| **Overall** | 70%+ project-wide |

## Unit Testing

### Test File Convention
```
src/features/<feature>/
├── components/
│   ├── MyComponent.tsx
│   └── MyComponent.test.tsx      # Co-located test
```

### Unit Test Structure (AAA Pattern)
```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { describe, it, expect, vi } from 'vitest'
import { MyComponent } from './MyComponent'

describe('MyComponent', () => {
  it('renders correctly with default props', () => {
    // Arrange
    const props = { title: 'Test', items: [] }

    // Act
    render(<MyComponent {...props} />)

    // Assert
    expect(screen.getByText('Test')).toBeInTheDocument()
  })

  it('handles user interaction', () => {
    // Arrange
    const onAction = vi.fn()
    render(<MyComponent onAction={onAction} />)

    // Act
    fireEvent.click(screen.getByRole('button'))

    // Assert
    expect(onAction).toHaveBeenCalledTimes(1)
  })
})
```

### Testing Guidelines
- **Arrange-Act-Assert** structure for every test
- **One assertion per logical test** — multiple `it` blocks for different scenarios
- **Describe blocks** mirror the component/function being tested
- **Mock external dependencies** — APIs, stores, services
- **No implementation details** in tests — test behavior, not internals
- **Use `screen`** over `container` for queries

## Integration Testing

### API Integration Tests
```typescript
import { describe, it, expect, beforeEach } from 'vitest'
import { rest } from 'msw'
import { setupServer } from 'msw/node'
import { render, screen, waitFor } from '@testing-library/react'

const server = setupServer(
  rest.get('/api/users', (req, res, ctx) => {
    return res(ctx.json({ users: [{ id: '1', name: 'Test' }] }))
  })
)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

describe('UserList', () => {
  it('fetches and displays users', async () => {
    render(<UserList />)
    await waitFor(() => {
      expect(screen.getByText('Test')).toBeInTheDocument()
    })
  })
})
```

### Component Integration Tests
```typescript
describe('AuthForm', () => {
  it('validates all fields before submitting', async () => {
    render(<AuthForm />)
    fireEvent.click(screen.getByRole('button', { name: 'Submit' }))
    await waitFor(() => {
      expect(screen.getByText('Email is required')).toBeInTheDocument()
    })
  })

  it('submits valid data and calls onSuccess', async () => {
    const onSuccess = vi.fn()
    render(<AuthForm onSuccess={onSuccess} />)
    fireEvent.change(screen.getByLabelText(/email/i), { target: { value: 'test@test.com' } })
    fireEvent.click(screen.getByRole('button', { name: 'Submit' }))
    await waitFor(() => {
      expect(onSuccess).toHaveBeenCalledWith({ email: 'test@test.com' })
    })
  })
})
```

## E2E Testing

### E2E Test Structure
```
tests/e2e/
├── auth/
│   ├── login.spec.ts
│   └── register.spec.ts
├── dashboard/
│   └── overview.spec.ts
└── setup.ts
```

### E2E Test Pattern
```typescript
import { test, expect } from '@playwright/test'

test.describe('Authentication', () => {
  test('login with valid credentials', async ({ page }) => {
    await page.goto('/login')
    await page.fill('[data-testid="email"]', 'user@test.com')
    await page.fill('[data-testid="password"]', 'password123')
    await page.click('[data-testid="submit"]')
    await expect(page).toHaveURL('/dashboard')
  })

  test('shows error with invalid credentials', async ({ page }) => {
    await page.goto('/login')
    await page.fill('[data-testid="email"]', 'wrong@test.com')
    await page.fill('[data-testid="password"]', 'wrongpassword')
    await page.click('[data-testid="submit"]')
    await expect(page.getByText('Invalid credentials')).toBeVisible()
  })
})
```

### E2E Best Practices
- **Use `data-testid`** attributes for selectors, never CSS classes
- **Test critical user journeys** — login, checkout, onboarding
- **Parallelize** tests where possible
- **Mock external APIs** for reliable, fast tests
- **Clean up** after each test (database resets)

## Test Data Management

### Factories
```typescript
// tests/factories/user.factory.ts
export function createUser(overrides = {}): User {
  return {
    id: 'user-1',
    email: 'test@test.com',
    name: 'Test User',
    role: 'USER',
    ...overrides,
  }
}
```

### Fixtures
```typescript
// tests/fixtures/auth.ts
export const authFixture = {
  validToken: 'eyJ...',
  expiredToken: 'eyJ...',
  invalidToken: 'not-a-token',
}
```

## Bug Reporting & Triage

### Bug Report Format
```
**Title**: [Component] Brief description of the bug
**Steps**: 1. Go to X 2. Click Y 3. Observe Z
**Expected**: What should happen
**Actual**: What happens
**Environment**: Browser, OS, viewport
**Severity**: Blocker / Critical / Major / Minor
**Screenshots**: Attach if visual
**Logs**: Console errors, network failures
```

### Severity Classification
| Severity | Description | Resolution SLA |
|----------|-------------|----------------|
| **Blocker** | System unusable | Immediate |
| **Critical** | Major feature broken | Same day |
| **Major** | Significant feature impaired | Next sprint |
| **Minor** | Minor cosmetic/functional | Next release |
| **Enhancement** | New feature / improvement | Backlog |

## QA Checklist

### Pre-Deploy Checklist
- [ ] All unit tests pass (`npm run test`)
- [ ] Integration tests pass
- [ ] E2E tests pass (`npm run test:e2e`)
- [ ] Test coverage meets threshold (80%+)
- [ ] No linting errors
- [ ] TypeScript compiles without errors
- [ ] Accessibility audit passes (axe, Lighthouse)
- [ ] Cross-browser tested (Chrome, Firefox, Safari, Edge)
- [ ] Responsive on all breakpoints
- [ ] Performance budget met (LCP < 2.5s, FID < 100ms)

### Code Review QA Items
- [ ] Tests added for new features
- [ ] Edge cases handled and tested
- [ ] Error states covered in tests
- [ ] Loading states tested
- [ ] Mocks are realistic and reset properly
- [ ] No `any` types in new code
- [ ] Security concerns addressed
- [ ] Performance impact evaluated

## Test Commands

```bash
# Run all tests
npm run test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:coverage

# Run E2E tests
npm run test:e2e

# Run E2E tests with UI
npm run test:e2e:ui

# Run linting
npm run lint

# Run typecheck
npm run typecheck
```

## Quick Decisions

| Question | Answer |
|----------|--------|
| Where to put unit tests? | Co-located with source (`*.test.tsx` / `*.test.ts`) |
| Where to put E2E tests? | `tests/e2e/` directory |
| How to mock API calls? | MSW (Mock Service Worker) |
| How to generate test data? | Factory functions in `tests/factories/` |
| What to test first? | Critical user journeys, then edge cases |
| How to handle flaky tests? | Investigate immediately, quarantine if needed |
| What coverage target? | 80% unit, critical paths for integration/E2E |

---

**Customize**: Update test tools (Vitest/Jest, Playwright/Cypress) to match your project's stack. Adjust coverage targets and test structure to your team's preferences.

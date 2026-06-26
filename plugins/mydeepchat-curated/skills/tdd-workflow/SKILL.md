---
name: tdd-workflow
description: Use this skill when writing new features, fixing bugs, or refactoring code. Enforces test-driven development with 80%+ coverage including unit, integration, and E2E tests. Works with Jest/Vitest (TypeScript/JS) and pytest (Python).
---
<!-- FORK ADAPTADO: Agregar pytest como equivalente Python; aclarar que los ejemplos npm/jest son cross-platform; scope genérico (no PiskeSoul-specific) · origen: affaan-m-everything-claude-code-agents-skills-tdd-workflow-skill-md · 2026-06-26 -->

# Test-Driven Development Workflow

This skill ensures all code development follows TDD principles with comprehensive test coverage.

## When to Activate

- Writing new features or functionality
- Fixing bugs or issues
- Refactoring existing code
- Adding API endpoints
- Creating new components

## Core Principles

### 1. Tests BEFORE Code

ALWAYS write tests first, then implement code to make tests pass.

### 2. Coverage Requirements

- Minimum 80% coverage (unit + integration + E2E)
- All edge cases covered
- Error scenarios tested
- Boundary conditions verified

### 3. Test Types

#### Unit Tests

- Individual functions and utilities
- Component logic
- Pure functions
- Helpers and utilities

#### Integration Tests

- API endpoints
- Database operations
- Service interactions
- External API calls

#### E2E Tests (Playwright)

- Critical user flows
- Complete workflows
- Browser automation
- UI interactions

## TDD Workflow Steps

### Step 1: Write User Journeys

```
As a [role], I want to [action], so that [benefit]

Example:
As a user, I want to search for products semantically,
so that I can find relevant items even without exact keywords.
```

### Step 2: Generate Test Cases

**TypeScript / JavaScript (Jest/Vitest)**:

```typescript
describe('Semantic Search', () => {
  it('returns relevant results for query', async () => {
    // Test implementation
  })

  it('handles empty query gracefully', async () => {
    // Test edge case
  })

  it('falls back when vector store unavailable', async () => {
    // Test fallback behavior
  })

  it('sorts results by similarity score', async () => {
    // Test sorting logic
  })
})
```

**Python (pytest)**:

```python
def test_returns_relevant_results():
    results = search_products("organic pisco")
    assert len(results) > 0

def test_handles_empty_query():
    results = search_products("")
    assert results == []

def test_fallback_when_vector_store_unavailable(mocker):
    mocker.patch("app.services.vector_store.search", side_effect=Exception)
    results = search_products("pisco")
    assert results is not None  # falls back to keyword search
```

### Step 3: Run Tests (They Should Fail)

```powershell
# JavaScript
npm test

# Python
pytest
```

### Step 4: Implement Code

Write minimal code to make tests pass.

### Step 5: Run Tests Again

```powershell
# JavaScript
npm test

# Python
pytest
```

### Step 6: Refactor

Improve code quality while keeping tests green:

- Remove duplication
- Improve naming
- Optimize performance
- Enhance readability

### Step 7: Verify Coverage

```powershell
# JavaScript
npm run test:coverage

# Python
pytest --cov=app --cov-report=term-missing
```

## Testing Patterns

### Unit Test Pattern (Jest/Vitest)

```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { Button } from './Button'

describe('Button Component', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByText('Click me')).toBeInTheDocument()
  })

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click</Button>)
    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
  })
})
```

### API Integration Test Pattern

```typescript
describe('GET /api/products', () => {
  it('returns products successfully', async () => {
    const request = new NextRequest('http://localhost/api/products')
    const response = await GET(request)
    const data = await response.json()
    expect(response.status).toBe(200)
    expect(data.success).toBe(true)
  })

  it('validates query parameters', async () => {
    const request = new NextRequest('http://localhost/api/products?limit=invalid')
    const response = await GET(request)
    expect(response.status).toBe(400)
  })
})
```

### FastAPI Integration Test Pattern (Python)

```python
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_get_products():
    response = client.get("/api/products")
    assert response.status_code == 200
    data = response.json()
    assert "items" in data

def test_create_product():
    payload = {"name": "Pisco Reservado", "category": "spirits"}
    response = client.post("/api/products", json=payload)
    assert response.status_code == 201
```

### E2E Test Pattern (Playwright)

```typescript
import { test, expect } from '@playwright/test'

test('user can search and filter products', async ({ page }) => {
  await page.goto('/')
  await page.fill('input[placeholder="Search"]', 'pisco')
  await page.waitForTimeout(600)
  const results = page.locator('[data-testid="product-card"]')
  await expect(results).toHaveCount(5, { timeout: 5000 })
})
```

## Test File Organization

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   └── Button.test.tsx
├── app/api/
│   └── products/
│       ├── route.ts
│       └── route.test.ts
└── e2e/
    └── products.spec.ts

# Python (FastAPI)
app/
├── tests/
│   ├── unit/
│   │   └── test_product_service.py
│   ├── integration/
│   │   └── test_api_products.py
│   └── e2e/
│       └── test_flows.py
```

## Coverage Configuration

**JavaScript (Jest)**:

```json
{
  "jest": {
    "coverageThresholds": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      }
    }
  }
}
```

**Python (pytest.ini / pyproject.toml)**:

```toml
[tool.pytest.ini_options]
addopts = "--cov=app --cov-fail-under=80"
```

## CI/CD Integration

```yaml
# GitHub Actions
- name: Run Tests
  run: npm test -- --coverage  # or: pytest --cov=app
- name: Upload Coverage
  uses: codecov/codecov-action@v3
```

## Common Testing Mistakes to Avoid

```typescript
// WRONG: Testing implementation details
expect(component.state.count).toBe(5)

// CORRECT: Test user-visible behavior
expect(screen.getByText('Count: 5')).toBeInTheDocument()
```

## Success Metrics

- 80%+ code coverage achieved
- All tests passing (green)
- No skipped or disabled tests
- Fast test execution (< 30s for unit tests)
- E2E tests cover critical user flows

---

**Remember**: Tests are not optional. They are the safety net that enables confident refactoring, rapid development, and production reliability.

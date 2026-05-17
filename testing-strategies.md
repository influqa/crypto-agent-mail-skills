# 🧪 Testing Strategies for Web Developers

> Based on testing best practices + enterprise testing patterns
> This is how $1M/year developers build reliable, maintainable applications.

## 🎯 Testing Mindset

### What Elite Developers Know:
1. **Test behavior, not implementation** - Tests should survive refactoring
2. **Write tests first** - TDD catches design issues early
3. **Fast feedback** - Tests should run in seconds, not minutes
4. **Test the important stuff** - 100% coverage is a vanity metric
5. **Tests are documentation** - They show how code should work
6. **Test in production** - Some things can only be tested live

## 📊 Testing Pyramid

```
        ┌─────────────┐
        │   E2E (10%) │  ← Slow, expensive, comprehensive
        ├─────────────┤
        │Integration  │  ← Medium speed, medium cost
        │   (20%)     │
        ├─────────────┤
        │   Unit      │  ← Fast, cheap, focused
        │   (70%)     │
        └─────────────┘
```

### Unit Tests (70%)
- **What**: Test individual functions/components in isolation
- **Speed**: < 1 second per test
- **Cost**: Cheap to write and maintain
- **Tools**: Jest, Vitest, React Testing Library

### Integration Tests (20%)
- **What**: Test how components work together
- **Speed**: 1-10 seconds per test
- **Cost**: Medium
- **Tools**: Supertest, Testing Library, MSW

### E2E Tests (10%)
- **What**: Test full user workflows
- **Speed**: 10-60 seconds per test
- **Cost**: Expensive to write and maintain
- **Tools**: Playwright, Cypress, Selenium

## 🛠️ Unit Testing

### Best Practices:
```typescript
// ✅ Good: Test behavior, not implementation
test('calculates total price with tax', () => {
  const cart = { items: [{ price: 100, quantity: 2 }] };
  const total = calculateTotal(cart, 0.08);
  expect(total).toBe(216); // 200 + 8% tax
});

// ❌ Bad: Testing implementation details
test('calls tax function with correct rate', () => {
  const spy = jest.spyOn(taxModule, 'calculateTax');
  calculateTotal(cart, 0.08);
  expect(spy).toHaveBeenCalledWith(0.08);
});
```

### AAA Pattern:
```typescript
test('user can login with valid credentials', () => {
  // Arrange
  const mockUser = { email: 'test@example.com', password: 'password123' };
  const mockDb = { findUser: jest.fn().mockResolvedValue(mockUser) };
  
  // Act
  const result = await login(mockUser.email, mockUser.password, mockDb);
  
  // Assert
  expect(result).toEqual({ success: true, user: mockUser });
  expect(mockDb.findUser).toHaveBeenCalledWith(mockUser.email);
});
```

### Mocking Strategies:
```typescript
// Mock external dependencies
jest.mock('axios');
const mockedAxios = axios as jest.Mocked<typeof axios>;

mockedAxios.get.mockResolvedValue({ data: { users: [] } });

// Mock database
jest.mock('./db', () => ({
  query: jest.fn().mockResolvedValue({ rows: [] })
}));

// Mock time
jest.useFakeTimers();
jest.setSystemTime(new Date('2026-01-01'));
```

## 🔗 Integration Testing

### API Testing:
```typescript
import request from 'supertest';
import app from '../app';

describe('GET /api/users', () => {
  test('returns list of users', async () => {
    const response = await request(app)
      .get('/api/users')
      .set('Authorization', 'Bearer valid-token');
    
    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('users');
    expect(Array.isArray(response.body.users)).toBe(true);
  });
  
  test('requires authentication', async () => {
    const response = await request(app)
      .get('/api/users');
    
    expect(response.status).toBe(401);
  });
});
```

### Database Testing:
```typescript
describe('User repository', () => {
  let db: TestDatabase;
  
  beforeEach(async () => {
    db = await setupTestDatabase();
    await db.migrate();
  });
  
  afterEach(async () => {
    await db.teardown();
  });
  
  test('creates user with valid data', async () => {
    const userData = { email: 'test@example.com', name: 'Test User' };
    const user = await db.users.create(userData);
    
    expect(user).toMatchObject(userData);
    expect(user.id).toBeDefined();
    expect(user.createdAt).toBeDefined();
  });
});
```

## 🌐 E2E Testing

### Playwright Example:
```typescript
import { test, expect } from '@playwright/test';

test('user can complete purchase flow', async ({ page }) => {
  // Navigate to app
  await page.goto('https://app.example.com');
  
  // Login
  await page.fill('[data-testid="email"]', 'test@example.com');
  await page.fill('[data-testid="password"]', 'password123');
  await page.click('[data-testid="login-button"]');
  
  // Wait for dashboard
  await expect(page.locator('[data-testid="dashboard"]')).toBeVisible();
  
  // Add item to cart
  await page.click('[data-testid="add-to-cart"]');
  await expect(page.locator('[data-testid="cart-count"]')).toHaveText('1');
  
  // Checkout
  await page.click('[data-testid="checkout"]');
  await page.fill('[data-testid="card-number"]', '4242424242424242');
  await page.click('[data-testid="pay"]');
  
  // Verify success
  await expect(page.locator('[data-testid="success-message"]')).toBeVisible();
});
```

## 🎯 Testing Strategies by Feature

### Authentication:
```typescript
describe('Authentication', () => {
  test('login with valid credentials', () => { /* ... */ });
  test('login with invalid credentials', () => { /* ... */ });
  test('password reset flow', () => { /* ... */ });
  test('session expiration', () => { /* ... */ });
  test('concurrent sessions', () => { /* ... */ });
});
```

### API Endpoints:
```typescript
describe('API Endpoints', () => {
  test('GET /api/users returns 200', () => { /* ... */ });
  test('POST /api/users creates user', () => { /* ... */ });
  test('PUT /api/users/:id updates user', () => { /* ... */ });
  test('DELETE /api/users/:id deletes user', () => { /* ... */ });
  test('Pagination works correctly', () => { /* ... */ });
  test('Rate limiting works', () => { /* ... */ });
});
```

### Database Operations:
```typescript
describe('Database Operations', () => {
  test('CRUD operations work', () => { /* ... */ });
  test('Transactions rollback on error', () => { /* ... */ });
  test('Indexes improve query performance', () => { /* ... */ });
  test('Migrations run successfully', () => { /* ... */ });
  test('Data constraints enforced', () => { /* ... */ });
});
```

## 📊 Test Coverage

### What to Cover:
- **Critical paths**: Login, checkout, payment, data saving
- **Error handling**: Network errors, validation failures, edge cases
- **Business logic**: Calculations, state transitions, workflows
- **Security**: Authentication, authorization, input validation
- **Performance**: Database queries, API response times

### What NOT to Test:
- Framework code (React, Express, etc.)
- Third-party libraries (they should have their own tests)
- Getters/setters (unless they contain logic)
- Trivial code (constants, simple assignments)
- UI styling (unless it affects functionality)

## 🛠️ Testing Tools

### JavaScript/TypeScript:
- **Jest**: Full-featured testing framework
- **Vitest**: Fast, Vite-native testing
- **React Testing Library**: Component testing
- **Supertest**: API testing
- **Playwright**: E2E testing
- **MSW**: API mocking
- **Testing Library**: DOM testing

### Database:
- **Testcontainers**: Real database in Docker
- **SQLite in-memory**: Fast, isolated testing
- **Mock Prisma/Sequelize**: ORM mocking

### CI/CD:
- **GitHub Actions**: Run tests on PR/merge
- **Codecov**: Coverage reporting
- **SonarQube**: Code quality analysis

## 📋 Testing Checklist

### Pre-Commit:
- [ ] Unit tests pass locally
- [ ] Code coverage meets threshold (80%+)
- [ ] No linting errors
- [ ] Type checking passes

### Pre-Deploy:
- [ ] All tests pass in CI
- [ ] Integration tests pass
- [ ] E2E tests pass (critical paths)
- [ ] Performance tests pass
- [ ] Security tests pass

### Post-Deploy:
- [ ] Smoke tests pass in production
- [ ] Monitoring alerts configured
- [ ] Error tracking configured
- [ ] Performance baselines recorded

## 🎯 Testing Anti-Patterns

### ❌ Don't Do This:
```typescript
// Testing implementation details
test('calls setState with correct value', () => { /* ... */ });

// Brittle tests that break on refactoring
test('component renders with exact class names', () => { /* ... */ });

// Testing third-party code
test('React renders components correctly', () => { /* ... */ });

// Flaky tests (timing dependent)
test('data loads within 100ms', () => { /* ... */ });

// Tests that don't assert anything
test('user can login', () => {
  login(user);
  // No assertions!
});
```

### ✅ Do This:
```typescript
// Test behavior
test('displays error message on login failure', () => { /* ... */ });

// Use data-testid for stable selectors
await page.click('[data-testid="login-button"]');

// Test your code, not libraries
test('login function handles network errors', () => { /* ... */ });

// Use fake timers for timing tests
jest.useFakeTimers();
jest.advanceTimersByTime(1000);

// Always assert something
test('user can login', () => {
  const result = login(user);
  expect(result.success).toBe(true);
});
```

---

*Last updated: 2026-05-16*
*Source: Testing best practices + enterprise testing patterns*

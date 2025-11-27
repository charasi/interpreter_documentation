## 1. Install Vitest
Navigate to the interpreter package directory and install the dependency:

```bash
npm install -D vitest
# or
pnpm add -D vitest
```

## 2. Configure Vitest
Create a `vitest.config.ts` file in the package root.

**File:** `vitest.config.ts`
```typescript
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    // Crucial for interpreters: strictly Node environment.
    // Prevents accidental reliance on browser APIs (like window/document).
    environment: 'node', 
    
    poolOptions: {
      threads: {
        // Optional: Set to false if the parser uses massive recursion.
        // This isolates tests so a stack overflow doesn't crash the whole suite.
        isolate: false
      }
    } 
  },
})
```

## 3. Update Scripts
Add the test scripts to your `package.json`.

**File:** `package.json`
```json
{
  "scripts": {
    "test": "vitest",
    "test:watch": "vitest",
    "test:run": "vitest run" 
  }
}
```

## 4. Create a Sample Test
Create a test file to verify token output or AST structure.

**File:** `src/lexer.test.ts`
```typescript
import { describe, it, expect } from 'vitest'
import { tokenize } from './lexer' // Assuming tokenize function exists

describe('Lexer', () => {
  it('should tokenize basic operators', () => {
    const input = '1 + 2'
    const tokens = tokenize(input)
    
    // Vitest supports deep object matching
    expect(tokens).toEqual([
      { type: 'NUMBER', value: 1 },
      { type: 'PLUS', value: '+' },
      { type: 'NUMBER', value: 2 },
      { type: 'EOF', value: '' }
    ])
  })
})
```

## 5. Run Tests
Execute the runner from the directory:

```bash
npm run test
```

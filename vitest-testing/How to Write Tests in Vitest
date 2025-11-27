This guide explains how to structure, write, and organize test cases using **Vitest**.

## 1. File Naming
Vitest automatically detects files with the following extensions. Place them next to the file you are testing or in a `__tests__` folder.
* `*.test.ts` (Recommended)
* `*.spec.ts`

## 2. Basic Anatomy of a Test
A standard test file consists of three main parts: **Imports**, **Suites** (`describe`), and **Test Cases** (`it` or `test`).

```typescript
import { describe, it, expect } from 'vitest';
import { add } from './math'; // The function you are testing

// 1. Describe Suite: Groups related tests together
describe('Math Functions', () => {

  // 2. Test Case: Defines a specific scenario
  it('should add two numbers correctly', () => {
    
    // 3. Execution: Run the code
    const result = add(2, 3);

    // 4. Assertion: Verify the result
    expect(result).toBe(5);
  });
});
```

## 3. Common Assertions (`expect`)
Vitest matches Jest's API almost exactly. Here are the most critical matchers for interpreter development.

| Matcher | Use Case | Example |
| :--- | :--- | :--- |
| `.toBe(value)` | Primitives (numbers, booleans, strings) | `expect(status).toBe(true)` |
| `.toEqual(obj)` | **Objects & Arrays** (Deep equality) | `expect(ast).toEqual({ type: 'Program' })` |
| `.toThrow(err)` | Errors/Exceptions | `expect(() => parse('bad code')).toThrow()` |
| `.toContain(item)` | Arrays or Strings | `expect(tokens).toContain('EOF')` |
| `.toBeTruthy()` | Checks if value casts to true | `expect(result).toBeTruthy()` |

## 4. Testing Objects (AST & Tokens)
When building an interpreter, you deal with nested objects. Use `.toEqual()` instead of `.toBe()`.

```typescript
it('should produce the correct AST node', () => {
  const node = createLiteral(5);
  
  // ✅ Correct: Checks structure
  expect(node).toEqual({ type: 'Literal', value: 5 });

  // ❌ Incorrect: Checks memory reference (will fail)
  // expect(node).toBe({ type: 'Literal', value: 5 }); 
});
```

## 5. Parameterized Tests (`it.each`)
Avoid writing duplicate code for repetitive logic (like testing different math operators). Use `it.each`.

```typescript
describe('Lexer Scenarios', () => {
  it.each([
    ['+', 'PLUS'],
    ['-', 'MINUS'],
    ['*', 'STAR'],
    ['/', 'SLASH'],
  ])('should tokenize operator "%s" as %s', (input, expectedType) => {
    
    const token = tokenize(input);
    expect(token.type).toBe(expectedType);
    
  });
});
```

## 6. Testing Errors
To test if your interpreter throws a syntax error correctly, you must wrap the call in an arrow function.

```typescript
it('should throw an error for unterminated strings', () => {
  const badInput = '"Hello world'; // Missing closing quote

  // Wrap execution in a function () => ...
  expect(() => tokenize(badInput)).toThrowError('Unterminated string');
});
```

## 7. Setup and Teardown
If you need to reset state (like a symbol table) before every test:

```typescript
import { beforeEach, describe, it } from 'vitest';

describe('Environment', () => {
  let env;

  beforeEach(() => {
    // Runs before every 'it' block in this suite
    env = new Environment();
  });

  it('should define variables', () => {
    env.define('x', 10);
    expect(env.get('x')).toBe(10);
  });
});
```

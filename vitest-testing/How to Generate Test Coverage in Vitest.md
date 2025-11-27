Test coverage measures how much of your code is executed when your tests run. For an interpreter project, **Branch Coverage** is particularly important to ensure every `if`, `else`, and `switch` case in your parser or evaluator is tested.

## 1. Install the Coverage Provider
Vitest does not include the coverage tool by default to keep the binary small. You need to install the `v8` provider (recommended for Node.js environments).

```bash
# Install coverage provider
npm install -D @vitest/coverage-v8
# or
pnpm add -D @vitest/coverage-v8
```

## 2. Update Configuration
Modify your `vitest.config.ts` to configure how coverage is collected and reported.

**File:** `vitest.config.ts`
```typescript
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'node',
    coverage: {
      provider: 'v8', // or 'istanbul'
      reporter: ['text', 'html'], // 'text' for console, 'html' for visual report
      
      // Optional: files to include/exclude
      include: ['src/**/*.ts'],
      exclude: ['src/**/*.test.ts', 'src/types.ts']
    }
  },
})
```

## 3. Update Scripts
Add a specific script to `package.json` to run coverage. We use `run` mode (instead of watch mode) because you usually only need to calculate coverage once per session or in CI.

**File:** `package.json`
```json
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest run --coverage"
  }
}
```

## 4. How to Interpret the Report
When you run `npm run test:coverage`, you will see a table in your terminal.

| Metric | Meaning | Relevance to Interpreters |
| :--- | :--- | :--- |
| **% Stmts** | Statements executed. | Did the code run at all? |
| **% Branch** | Decisions taken (if/else/switch). | **Critical.** Did you test both the `if (error)` and the success path? |
| **% Funcs** | Functions called. | Did you test every helper function? |
| **% Lines** | Lines of code executed. | General health check. |

## 5. Visualizing Coverage (HTML Report)
If you included `'html'` in your reporter config (Step 2), Vitest generates a folder named `coverage/`.

1. Open `coverage/index.html` in your browser.
2. You can click on individual files (e.g., `parser.ts`).
3. Code highlighted in **Red** was never executed during your tests. This tells you exactly what logic is missing a test case.

## 6. Enforcing Thresholds (Optional)
You can force the test command to fail if coverage drops below a certain percentage. This is useful for CI/CD pipelines to prevent code quality regression.

**File:** `vitest.config.ts`
```typescript
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      
      // Fail if coverage is below 80%
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80
      }
    }
```

## 7. Ignoring Specific Code
Sometimes you have code that is impossible or unnecessary to test (like a debug logger). You can tell Vitest to ignore it using comments.

```typescript
/* v8 ignore next 3 */
if (process.env.NODE_ENV === 'development') {
  console.log('Debug mode enabled');
}
```

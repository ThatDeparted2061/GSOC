## Raw Testing file Template
```ts
// 1. ✅ Imports
import { describe, it, expect, beforeAll, beforeEach, afterAll, afterEach } from 'vitest';
import { myFunction } from './myModule'; // your code to test

// 2. 🧹 Global setup (if needed)
beforeAll(() => {
  // Runs once before all tests
});

afterAll(() => {
  // Runs once after all tests
});

beforeEach(() => {
  // Runs before each test
});

afterEach(() => {
  // Runs after each test
});

// 3. 🧪 Tests grouped by `describe`
describe('myFunction()', () => {
  it('should return correct result for input A', () => {
    const result = myFunction('A');
    expect(result).toBe('ExpectedValue');
  });

  it('should handle invalid input gracefully', () => {
    const result = myFunction(null);
    expect(result).toBe('FallbackValue');
  });
});
```

## Raw Testing file Format with Tauri Mocking
```ts
import { describe, test, expect, beforeAll, beforeEach } from 'vitest'
import { mockIPC } from '@tauri-apps/api/mocks'
import { invoke } from '@tauri-apps/api/core'
import { randomFillSync } from 'crypto'

beforeAll(() => {
  Object.defineProperty(window, 'crypto', {
    value: {
      getRandomValues: (buffer: ArrayBuffer) => randomFillSync(buffer),
    },
  });
});

beforeEach(() => {
  mockIPC.clear();
});

test('invoke mocked command', async () => {
  mockIPC((cmd, args) => {
    if (cmd === 'greet') {
      return `Hello, ${args.name}`
    }
  });

  const result = await invoke('greet', { name: 'Harsh' });
  expect(result).toBe('Hello, Harsh');
});
```

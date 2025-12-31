# Test Coverage Analysis

## Current Status

**Test Coverage: 0%**

The pulse project currently has no automated tests, no test framework configured, and no testing infrastructure in place.

### Project Structure
```
pulse/
├── README.md
├── .gitignore
└── patterns/
    ├── blue-monday.strudel      # Blue Monday - New Order cover
    ├── im-summer.strudel        # Original composition
    └── stranger-things.strudel  # Stranger Things theme
```

### Source Files Analyzed
| File | Lines | Purpose |
|------|-------|---------|
| `patterns/blue-monday.strudel` | 25 | Iconic bass line, drums, synth stabs at 130 BPM |
| `patterns/im-summer.strudel` | 27 | G# major progression, bouncy bass, melody at 94 BPM |
| `patterns/stranger-things.strudel` | 23 | Ambient supersaw synths with perlin modulation |

---

## Proposed Testing Strategy

### 1. Syntax Validation Tests (High Priority)

**What to test:** Ensure all `.strudel` patterns are syntactically valid JavaScript/Strudel code.

**Why:** Catch syntax errors before manually loading patterns into strudel.cc editor.

**Implementation:**
```javascript
// tests/syntax.test.js
import { parse } from '@strudel/core';

describe('Pattern Syntax Validation', () => {
  test('blue-monday.strudel is valid', async () => {
    const content = fs.readFileSync('patterns/blue-monday.strudel', 'utf8');
    expect(() => parse(content)).not.toThrow();
  });
});
```

**Coverage impact:** Would cover all 3 pattern files for syntax correctness.

---

### 2. Pattern Structure Tests (High Priority)

**What to test:** Verify each pattern has required musical components.

**Why:** Ensure patterns maintain expected structure (tempo, instruments, etc.) after edits.

**Test cases:**
- [ ] Each pattern sets a tempo via `setcpm()` or `setbpm()`
- [ ] Each pattern has at least one sound source (`$:` pattern)
- [ ] Patterns use valid note notation
- [ ] Patterns use valid synthesizer names

**Implementation:**
```javascript
describe('Pattern Structure', () => {
  test('blue-monday has tempo setting', () => {
    const content = fs.readFileSync('patterns/blue-monday.strudel', 'utf8');
    expect(content).toMatch(/setcpm\(\d+\.?\d*\)|setbpm\(\d+\.?\d*\)/);
  });

  test('blue-monday has bass line', () => {
    const content = fs.readFileSync('patterns/blue-monday.strudel', 'utf8');
    expect(content).toMatch(/\$:.*note\(/);
  });
});
```

---

### 3. Static Analysis Tests (Medium Priority)

**What to test:** Code quality and common issues in patterns.

**Test cases:**
- [ ] No undefined variables
- [ ] Gain values are within reasonable range (0-1)
- [ ] Filter frequencies are within audible range
- [ ] No duplicate pattern definitions
- [ ] Comments are present for major sections

**Implementation:**
```javascript
describe('Static Analysis', () => {
  test('gain values are reasonable', () => {
    const content = fs.readFileSync('patterns/blue-monday.strudel', 'utf8');
    const gains = content.match(/\.gain\(([\d.]+)\)/g);
    gains?.forEach(g => {
      const value = parseFloat(g.match(/[\d.]+/)[0]);
      expect(value).toBeGreaterThanOrEqual(0);
      expect(value).toBeLessThanOrEqual(1);
    });
  });
});
```

---

### 4. Pattern Metadata Tests (Medium Priority)

**What to test:** Musical metadata extraction and validation.

**Test cases per pattern:**
| Pattern | Expected BPM | Key | Time Signature |
|---------|-------------|-----|----------------|
| blue-monday.strudel | 130 | F minor | 4/4 |
| im-summer.strudel | 94 | G# major | 4/4 |
| stranger-things.strudel | 84 | C major | 4/4 |

**Implementation:**
```javascript
describe('Pattern Metadata', () => {
  test('blue-monday tempo is approximately 130 BPM', () => {
    const content = fs.readFileSync('patterns/blue-monday.strudel', 'utf8');
    const cpmMatch = content.match(/setcpm\(([\d.]+)\)/);
    const cpm = parseFloat(cpmMatch[1]);
    const bpm = cpm * 4; // Convert cycles per minute to BPM
    expect(bpm).toBe(130);
  });
});
```

---

### 5. Integration/Snapshot Tests (Low Priority)

**What to test:** Pattern output consistency over time.

**Why:** Detect unintended changes to pattern output.

**Implementation approach:**
- Use Strudel's headless evaluation mode
- Generate pattern data for first N cycles
- Compare against stored snapshots

**Note:** This requires more complex setup with `@strudel/core` running in Node.js.

---

### 6. Linting Tests (Low Priority)

**What to test:** Code style consistency across patterns.

**Rules to enforce:**
- [ ] Consistent comment style
- [ ] Consistent spacing around operators
- [ ] Pattern sections in logical order (tempo, melody, bass, drums)
- [ ] No trailing whitespace

---

## Recommended Test Infrastructure

### Required Setup

1. **Initialize npm project:**
```bash
npm init -y
```

2. **Install dependencies:**
```bash
npm install --save-dev jest @strudel/core
```

3. **Create Jest configuration:**
```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'node',
  testMatch: ['**/tests/**/*.test.js'],
  collectCoverageFrom: ['patterns/**/*.strudel'],
};
```

4. **Add test script to package.json:**
```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

5. **Create test directory structure:**
```
pulse/
├── tests/
│   ├── syntax.test.js
│   ├── structure.test.js
│   ├── metadata.test.js
│   └── snapshots/
```

---

## Priority Ranking

| Priority | Test Type | Effort | Value |
|----------|-----------|--------|-------|
| 1 | Syntax Validation | Low | High |
| 2 | Pattern Structure | Low | High |
| 3 | Pattern Metadata | Medium | Medium |
| 4 | Static Analysis | Medium | Medium |
| 5 | Snapshot Tests | High | Medium |
| 6 | Linting | Low | Low |

---

## Implementation Roadmap

### Phase 1: Foundation (Recommended First)
- [ ] Initialize npm project
- [ ] Install Jest and Strudel dependencies
- [ ] Create basic test structure
- [ ] Implement syntax validation tests for all patterns

### Phase 2: Structure Validation
- [ ] Add pattern structure tests
- [ ] Add metadata extraction tests
- [ ] Create test utilities for common assertions

### Phase 3: Advanced Testing
- [ ] Add static analysis tests
- [ ] Consider snapshot testing for regression detection
- [ ] Add CI/CD integration (GitHub Actions)

---

## Files That Need Tests

| File | Recommended Tests |
|------|-------------------|
| `patterns/blue-monday.strudel` | Syntax, Structure, Metadata (130 BPM, F minor) |
| `patterns/im-summer.strudel` | Syntax, Structure, Metadata (94 BPM, G# major) |
| `patterns/stranger-things.strudel` | Syntax, Structure, Metadata (84 BPM, C major) |

---

## Summary

The pulse project currently has **zero test coverage**. Given the nature of live coding patterns, the most valuable tests would be:

1. **Syntax validation** - Ensures patterns can be parsed without errors
2. **Structure validation** - Ensures patterns have required musical components
3. **Metadata validation** - Ensures musical properties (tempo, key) are correct

Implementing these tests would provide confidence when editing patterns and catch common mistakes before manual testing in the browser.

# Next.js 15 Setup Instructions (2024-2025)

This guide provides a step-by-step process to set up a production-ready Next.js 15 project, following the latest best practices for code quality, testing, styling, database, internationalization, and CI/CD.

## Persona

You are a senior fronted developer in Next.js expert.

## 1. Initialize Your Project

* Accept all prompts: **TypeScript**, **ESLint**, **Tailwind CSS**, **src/** directory, **App Router**.
* Use Turbopack for `next dev`

```bash
echo "Y" | npx create-next-app@latest \<your-project-name\> --typescript --eslint --tailwind --src-dir
      --app --import-alias "@/*"
```

* Change directory to the project:

```bash
cd \<your-project-name\>
```

## 2. Initialize Git and create a GitIgnore file

* Initialize Git:

```bash
git init
```

* Create a `.gitignore` file targetted for Next.js, React, Javascript project.

```bash
touch .gitignore
```

* Add content to the `.gitignore` file:

```text
# See https://help.github.com/articles/ignoring-files/ for more about ignoring files.

# dependencies
/node_modules
/.pnp
.pnp.*
.yarn/*
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/versions

# testing
/coverage

# next.js
/.next/
/out/

# production
/build

# misc
.DS_Store
*.pem

# debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*

# local env files
.env*.local

# env files (can opt-in for committing if needed)
.env*

# vercel
.vercel

# typescript
*.tsbuildinfo
next-env.d.ts

# vitest
/coverage
*.log

# playwright
/test-results/
/playwright-report/
/blob-report/
/playwright/.cache/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Temporary files
*.tmp
*.temp
.cache/
```

## 3. TypeScript Type Checks

* Add a type-check script to your `package.json`:

```json
"type-check": "tsc -b"
```

## 4. Code Formatting and Linting

### Prettier

* Install Prettier and its Tailwind plugin:

```bash
npm install --save-dev prettier prettier-plugin-tailwindcss
```

* Create `prettier.config.js`:

```js
module.exports = {
  arrowParens: 'avoid',
  bracketSameLine: false,
  bracketSpacing: true,
  htmlWhitespaceSensitivity: 'css',
  insertPragma: false,
  jsxSingleQuote: false,
  plugins: ['prettier-plugin-tailwindcss'],
  printWidth: 80,
  proseWrap: 'always',
  quoteProps: 'as-needed',
  requirePragma: false,
  semi: true,
  singleQuote: true,
  tabWidth: 2,
  trailingComma: 'all',
  useTabs: false,
};
```

* Add to `package.json`:

```json
"format": "prettier --write ."
```

* Run:

```bash
npm run format
```

### ESLint

* Manually install ESLint and initialize it with the recommended config:

```bash
npm install --save-dev eslint@latest @eslint/js@latest

```

* Install recommended plugins:

```bash
npm install --save-dev @eslint/eslintrc @typescript-eslint/parser @typescript-eslint/eslint-plugin typescript-eslint eslint-config-next eslint-plugin-react eslint-plugin-unicorn eslint-plugin-import eslint-plugin-playwright eslint-config-prettier eslint-plugin-prettier eslint-plugin-simple-import-sort
```

* Update `eslint.config.mjs`:

```js
// eslint.config.mjs
import globals from 'globals';
import pluginJs from '@eslint/js';
import tseslint from 'typescript-eslint';

export default [
  {
    files: ['**/*.{js,mjs,cjs,ts,jsx,tsx}'],
    languageOptions: {
      globals: {
        ...globals.browser,
        ...globals.node,
        React: 'readonly',
      },
    },
  },
  pluginJs.configs.recommended,
  ...tseslint.configs.recommended,
  {
    rules: {
      'no-unused-vars': 'warn',
      'no-console': 'warn',
      'no-undef': 'warn',
    },
  },
];
```

* I* Add to `package.json`:

```json
"lint:fix": "next lint --fix"
```

* Run:

```bash
npm run lint:fix
```

## 5. Commitlint & Husky (Conventional Commits)

* Install:

```bash
npm install --save-dev @commitlint/cli@latest @commitlint/config-conventional@latest husky@latest
npx husky-init && npm install
```

* Add hooks:

```bash
npx husky add .husky/pre-commit 'npm run lint && npm run type-check'
npx husky add .husky/prepare-commit-msg 'exec < /dev/tty && npx cz --hook || true'
```

* Install Commitizen:

```bash
npm install --save-dev commitizen cz-conventional-changelog
```

* Add to `package.json`:

```json
"config": {
  "commitizen": { "path": "cz-conventional-changelog" }
}
```

* Create `commitlint.config.cjs`:

```js
const config = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'references-empty': [1, 'never'],
    'footer-max-line-length': [0, 'always'],
    'body-max-line-length': [0, 'always'],
  },
};
module.exports = config;
```

## 6. Folder Structure

```text
public/                   # Static assets
src/                      # Source code
├── app/                  # Application Routes (App Router implementation. File-system based routing, layouts, and pages)
│   ├── api/              # API Route Handlers
│   ├── (group)/          # Grouped API Route Handlers (Organizes routes logically (e.g., (auth), (marketing)) without affecting the URL path.)
│   ├── \[route\]/\_      # Private Co-located Folders (Folders prefixed with _ (e.g., _components, _actions) are not routed. Used for co-locating route-specific logic.)
│   ├── layout.tsx        # Root Layout with metadata
│   └── page.tsx          # Main page component
├── components/           # Universal components
│   ├── ui/               # Stateless, highly reusable UI primitives (e.g., Button, Card, Input)
│   ├── features/         # Shared Feature Components (e.g., ProductGrid, CheckoutForm)
│   ├── layout/           # Layout components (Major structural elements like Header, Footer, Sidebar.)
│   └── ClientWrapper.tsx # Client-side wrapper
├── lib/                  # Core Libraries & Services
│   ├── db/               # Database connection clients and ORM instances (e.g., Prisma Client, Drizzle instance).
│   ├── auth/             # Authentication helpers, session management, and integrations with services like NextAuth.js or Lucia.
│   ├── api/              # Clients for interacting with third-party APIs.
│   ├── actions/          # Global Server Actions ('use server' functions for mutations, accessible across the application).
│   ├── store/            # Global State Management (Location for the Zustand state store and related hooks).
│   └── constants.ts      # Global constants
├── hooks/                # Custom React Hooks (e.g., useMediaQuery, useLocalStorage)
├── utils/                # Utility Functions
│   ├── formatters.ts     # Functions for formatting dates, currency, etc.
│   ├── validators.ts     # Simple data validation functions
│   └── utils.ts          # Simple, pure, stateless helper functions (e.g., formatters, validators)
├── styles/               # Global & Theming Styles
│   └── globals.css       # Contains globals.css, theme variables, etc.
├── types/                # Global TypeScript Types
│   └── types.ts          # Shared type definitions and interfaces used across the application
├── tests/                # test code
│   ├── unit/             # Unit & Integration Tests (tests for individual components, functions, and hooks. Mirrors the /src structure)
│   └── e2e/              # End-to-End Tests (Playwright tests simulating full user journeys across the application)
```

## 7. Unit Testing

### Vitest (Unit/Integration)

* Install:

```bash
npm install -D vitest
```

* Update `vitest.config.ts`:

```ts
import react from '@vitejs/plugin-react';
import { defineConfig } from 'vitest/config';
import tsconfigPaths from 'vite-tsconfig-paths';

export default defineConfig({
  plugins: [react(), tsconfigPaths()],
  test: {
    environment: 'happy-dom',
    globals: true,
    setupFiles: './src/tests/setup-test-environment.ts',
    exclude: ['node_modules', 'dist', '.next', './src/tests/e2e/**'],
  },
});
```

* Add to `package.json`:

```json
"test": "vitest --reporter=verbose"
```

* Create `src/tests/unit/example.test.ts`:

```ts
import { describe, expect, test } from 'vitest';
describe('example', () => {
  test('given a passing test: passes', () => {
    expect(1).toStrictEqual(1);
  });
});
```

### React Testing Library

* Install:

```bash
npm install --save-dev @testing-library/react @testing-library/dom @testing-library/jest-dom @testing-library/user-event @types/react @types/react-dom happy-dom @vitejs/plugin-react vite-tsconfig-paths
```

* Create `vitest.config.ts` and `src/tests/setup-test-environment.ts` as per [React Testing Library + Vitest setup](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025).

## 8. End-to-End Testing (Playwright)

* Install Playwright

```bash
npm init playwright@latest
```

* Add to `package.json`:

```json
"test:e2e": "npx playwright test",
"test:e2e:ui": "npx playwright test --ui"
```

* Create a `src/tests/e2e/example.spec.ts` file.

```ts
import { test, expect } from '@playwright/test';

test('example', async ({ page }) => {
  await page.goto('http://localhost:3000');
  await expect(page.getByText('Get started by editing')).toBeVisible();
});
```

* Install Playwright new browser

```bash
npx playwright install 
```

* Run the tests

```bash
npm run test:e2e
```

## 9. Styling

### Tailwind CSS

* Install Tailwind CSS, postcss, and autoprefixer:

```bash
npm install -D tailwindcss postcss autoprefixer
```

* Initialize Tailwind CSS:

```bash
npx tailwindcss init -p
```

### Shadcn UI

* Install Shadcn UI:

```bash
npx shadcn@latest init --defaults
```

* Add a card component:

```bash
npx shadcn@latest add card
```

## 10. Continuous Integration (GitHub Actions)

* Create `.github/workflows/pull-request.yml` for CI with lint, type-check, unit/integration/E2E tests, and DB setup. See [example config](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025#github-actions).

## 11. Run all quality checks

* Run all quality checks including linting, type-checking, formatting, unit tests, and E2E tests.

```bash
npm run lint:fix && npm run type-check && npm run format && npm test
```

## 12. Run the Development Server

* Start the development server using Turbopack.

```bash
npm run dev
```

* Visit [http://localhost:3000](http://localhost:3000) to verify your app is running.

```bash
curl http://localhost:3000
```

## 13. Deployment

* Deploy to [Vercel](https://vercel.com/) for best Next.js support.

## References

* [How To Set Up Next.js 15 For Production In 2025](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025)
* [Next.js Official Docs](https://nextjs.org/docs)

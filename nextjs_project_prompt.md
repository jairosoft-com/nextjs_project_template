# Next.js 15 Setup Instructions (2024-2025)

This guide provides a step-by-step process to set up a production-ready Next.js 15 project, following the latest best practices for code quality, testing, styling, database, internationalization, and CI/CD.

## Persona

You are a senior fronted developer in Next.js expert.

## 1. Initialize Your Project

```bash
npx create-next-app@latest
```

* Accept all prompts: **TypeScript**, **ESLint**, **Tailwind CSS**, **src/** directory, **App Router** (recommended).

```bash
cd <your-project-name>
```

## 2. Run the Development Server

```bash
npm run dev
```

Visit [http://localhost:3000](http://localhost:3000) to verify your app is running.

## 3. TypeScript Type Checks

Add a type-check script to your `package.json`:

```json
"type-check": "tsc -b"
```

## 4. Code Formatting and Linting

### Prettier

Install Prettier and its Tailwind plugin:

```bash
npm install --save-dev prettier prettier-plugin-tailwindcss
```

Create `prettier.config.js`:

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

Add to `package.json`:

```json
"format": "prettier --write ."
```

Run:

```bash
npm run format
```

### ESLint

Install recommended plugins:

```bash
npm install --save-dev @eslint/eslintrc @typescript-eslint/parser @typescript-eslint/eslint-plugin typescript-eslint eslint-config-next eslint-plugin-react eslint-plugin-unicorn eslint-plugin-import eslint-plugin-playwright eslint-config-prettier eslint-plugin-prettier eslint-plugin-simple-import-sort
```

Update `.eslintrc.json`:

```json
{
  "extends": [
    "next/core-web-vitals",
    "plugin:unicorn/recommended",
    "plugin:import/recommended",
    "plugin:playwright/recommended",
    "plugin:prettier/recommended"
  ],
  "plugins": ["simple-import-sort"],
  "rules": {
    "simple-import-sort/exports": "error",
    "simple-import-sort/imports": "error",
    "unicorn/no-array-callback-reference": "off",
    "unicorn/no-array-for-each": "off",
    "unicorn/no-array-reduce": "off",
    "unicorn/prevent-abbreviations": [
      "error",
      {
        "allowList": { "e2e": true },
        "replacements": { "props": false, "ref": false, "params": false }
      }
    ]
  },
  "overrides": [
    {
      "files": ["*.js"],
      "rules": { "unicorn/prefer-module": "off" }
    }
  ]
}
```

Add to `package.json`:

```json
"lint:fix": "next lint --fix"
```

Run:

```bash
npm run lint:fix
```

## 5. Commitlint & Husky (Conventional Commits)

Install:

```bash
npm install --save-dev @commitlint/cli@latest @commitlint/config-conventional@latest husky@latest
npx husky-init && npm install
```

Add hooks:

```bash
npx husky add .husky/pre-commit 'npm run lint && npm run type-check'
npx husky add .husky/prepare-commit-msg 'exec < /dev/tty && npx cz --hook || true'
```

Install Commitizen:

```bash
npm install --save-dev commitizen cz-conventional-changelog
```

Add to `package.json`:

```json
"config": {
  "commitizen": { "path": "cz-conventional-changelog" }
}
```

Create `commitlint.config.cjs`:

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

Install:

```bash
npm install -D vitest
```

Add to `package.json`:

```json
"test": "vitest --reporter=verbose"
```

Create `src/test/unit/example.test.ts`:

```ts
import { describe, expect, test } from 'vitest';
describe('example', () => {
  test('given a passing test: passes', () => {
    expect(1).toStrictEqual(1);
  });
});
```

### React Testing Library

Install:

```bash
npm install --save-dev @testing-library/react @testing-library/dom @testing-library/jest-dom @testing-library/user-event @types/react @types/react-dom happy-dom @vitejs/plugin-react vite-tsconfig-paths
```

Create `vitest.config.ts` and `src/tests/setup-test-environment.ts` as per [React Testing Library + Vitest setup](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025).

## 8. Styling

### Tailwind CSS

Already included if you selected it during setup.

### Shadcn UI

```bash
npx shadcn@latest init
npx shadcn@latest add card
```

## 9. Internationalization (i18n)

Install:

```bash
npm install negotiator @formatjs/intl-localematcher
npm install --save-dev @types/negotiator
```

Set up i18n config and middleware as described in [this guide](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025#internationalization).

## 10. Database (Postgres + Prisma)

Install:

```bash
npm install prisma --save-dev
npm install @prisma/client
npx prisma init
```

Edit `prisma/schema.prisma` and `.env.local` for your DB connection.
Add helper scripts to `package.json`:

```json
"prisma:deploy": "npx prisma migrate deploy && npx prisma generate",
"prisma:migrate": "npx prisma migrate dev --name",
"prisma:push": "npx prisma db push && npx prisma generate",
"prisma:reset-dev": "run-s prisma:wipe prisma:seed dev",
"prisma:seed": "tsx ./prisma/seed.ts",
"prisma:setup": "prisma generate && prisma migrate deploy && prisma db push",
"prisma:studio": "npx prisma studio",
"prisma:wipe": "npx prisma migrate reset --force && npx prisma db push"
```

Install supporting tools:

```bash
npm install --save-dev npm-run-all tsx dotenv
```

## 11. End-to-End Testing (Playwright)

```bash
npm init playwright@latest
```

Add to `package.json`:

```json
"test:e2e": "npx playwright test",
"test:e2e:ui": "npx playwright test --ui"
```

## 12. Continuous Integration (GitHub Actions)

Create `.github/workflows/pull-request.yml` for CI with lint, type-check, unit/integration/E2E tests, and DB setup. See [example config](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025#github-actions).

## 13. Deployment

* Deploy to [Vercel](https://vercel.com/) for best Next.js support.

## 14. GitIgnore file

* Create a .gitignore file targetted for Next.js, React, Javascript project.

## 15. Final Instructions

* No do not initialize git on the new folder

## References

* [How To Set Up Next.js 15 For Production In 2025](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025)
* [Next.js Official Docs](https://nextjs.org/docs)

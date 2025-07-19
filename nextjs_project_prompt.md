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

* Update next.config.mjs to include the following:

```js
// /next.config.ts

import type { NextConfig } from 'next';

/**  
 * @type {import('next').NextConfig}  
 *  
 * The configuration for the Next.js application.  
 * Next.js 15 supports using a TypeScript file for configuration, providing type safety.  
 * @see https://nextjs.org/docs/app/api-reference/config/next-config-js  
 */  
const nextConfig: NextConfig = {
 // React's Strict Mode is a development-only feature that helps identify potential problems  
 // in an application. It activates additional checks and warnings for its descendants.  
 // It is highly recommended to keep this enabled.
reactStrictMode: true,

// Configuration for the ESLint linter.  
eslint: {
 // By default, Next.js lints the `pages`, `app`, `components`, `lib`, and `src` directories.  
 // This configuration extends linting to our custom `/tests` directory during `next build`.
dirs: ['src', 'tests'],  
 },

// Experimental features that can be opted into. Use with caution as APIs may change.  
 experimental: {
 // Enables the `after()` API, which allows scheduling work to be processed *after* a response  
 // has finished streaming. Useful for tasks like logging or analytics that shouldn't block  
 // the main response.


    // The React Compiler is an experimental compiler from Meta that automatically optimizes
    // React code, reducing the need for manual memoization with `useMemo` and `useCallback`.
    // Enabling this can lead to performance improvements and simpler code.
    // compiler: true, // Uncomment when the compiler is stable and widely adopted.

},

// In Next.js 15, GET Route Handlers are uncached by default. This configuration is an example  
 // of how you could opt back into the previous caching behavior if needed, though the  
 // recommended approach is to be explicit with `fetch` caching options.
// experimental: {  
 // staleTimes: {  
 // dynamic: 30, // Cache dynamic pages for 30 seconds  
 // static: 180, // Cache static pages for 180 seconds  
 // },  
 // },  
};

export default nextConfig;
```

* Update tsconfig.json to include the following:

```json
// /tsconfig.json  
{
  "compilerOptions": {
    /* Type Checking */
    "strict": true, // Enables all strict type-checking options. This is the cornerstone of type safety.
    "noImplicitAny": true, // Raise error on expressions and declarations with an implied 'any' type.
    "strictNullChecks": true, // When true, `null` and `undefined` have their own distinct types.
    "noUnusedLocals": true, // Report errors on unused local variables.
    "noUnusedParameters": true, // Report errors on unused parameters.
    "noFallthroughCasesInSwitch": true, // Report errors for fallthrough cases in switch statement.
    /* Modules */
    "module": "esnext", // Specify what module code is generated. 'esnext' allows for modern module features.
    "moduleResolution": "bundler", // The recommended mode for modern bundlers like Webpack/Turbopack. It aligns with how Node.js resolves modules.
    "resolveJsonModule": true, // Allows importing `.json` files.
    "allowImportingTsExtensions": true, // Required for `moduleResolution: "bundler"` when using TypeScript files with extensions.
    /* JavaScript Support */
    "allowJs": true, // Allow JavaScript files to be compiled.
    "checkJs": true, // Report errors in.js files.
    /* Emit */
    "noEmit": true, // Do not emit output files (transpilation is handled by Next.js's compiler).
    "incremental": true, // Enable incremental compilation, speeding up subsequent builds.
    /* Interop Constraints */
    "esModuleInterop": true, // Enables compatibility with CommonJS modules.
    "forceConsistentCasingInFileNames": true, // Ensure that casing is correct in imports.
    "isolatedModules": true, // Ensures that each file can be transpiled without relying on other imports.
    /* Language and Environment */
    "target": "es5", // The language version for emitted JavaScript. 'es5' ensures broad browser compatibility.
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ], // Specifies the library files to be included in the compilation.
    "jsx": "preserve", // Do not transform JSX. Next.js's compiler handles this.
    /* Path Aliases */
    "baseUrl": ".", // Base directory to resolve non-absolute module names.
    "paths": {
      "@/*": [
        "./src/*"
      ] // Sets up the '@/' alias to point to the 'src' directory for cleaner imports.
    },
    /* Plugins */
    "plugins": [
      {
        "name": "next"
      }
    ],
    "skipLibCheck": true
  },
  "include": [
    "next-env.d.ts",
    ".next/types/**/*.ts",
    "**/*.ts",
    "**/*.tsx",
    "**/*.cjs",
    "**/*.mjs"
  ],
  "exclude": [
    "node_modules"
  ]
}
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
npm install --save-dev @eslint/eslintrc @typescript-eslint/parser @typescript-eslint/eslint-plugin typescript-eslint eslint-config-next eslint-plugin-tailwindcss eslint-plugin-next eslint-plugin-react eslint-plugin-unicorn eslint-plugin-import eslint-plugin-playwright eslint-config-prettier eslint-plugin-prettier eslint-plugin-simple-import-sort
```

* Update `eslint.config.mjs`:

```js
import js from '@eslint/js';
import tsEslint from 'typescript-eslint';
import react from 'eslint-plugin-react';
import reactHooks from 'eslint-plugin-react-hooks';
import nextPlugin from '@next/eslint-plugin-next';
import globals from 'globals';

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
  {
    plugins: {
      '@next/next': nextPlugin,
    },
    rules: {
      ...nextPlugin.configs.recommended.rules,
      // Add any specific Next.js rule overrides here if needed
    },
  },
  js.configs.recommended,
  ...tsEslint.configs.recommended,
  {
    files: ['**/*.{ts,tsx}'],
    plugins: {
      react,
      'react-hooks': reactHooks,
    },
    rules: {
      ...react.configs.recommended.rules,
      ...reactHooks.configs.recommended.rules,
      'react/react-in-jsx-scope': 'off',
      'react/prop-types': 'off',
    },
    settings: {
      react: {
        version: 'detect',
      },
    },
  },
];

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
tests/                # test code
├── unit/             # Unit & Integration Tests (tests for individual components, functions, and hooks. Mirrors the /src structure)
└── e2e/              # End-to-End Tests (Playwright tests simulating full user journeys across the application)
```

### Moving files to the new project

* Move the globals.css to the new project, and update the path in files that use it.

```bash
mv src/app/global.css src/styles/globals.css
```

## 7. Unit Testing

### Vitest (Unit/Integration)

* Install:

```bash
npm install -D vitest
```

* Update `vitest.config.ts`:

```ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import tsconfigPaths from 'vite-tsconfig-paths';

export default defineConfig({
  plugins: [react(), tsconfigPaths()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: './tests/unit/setup.ts',
    exclude: [
      '**/node_modules/**',
      '**/tests/e2e/**',
      '**/.next/**',
      '**/*.spec.ts',
    ],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'tests/',
        '.next/',
        '**/*.js',
        '**/*.config.*',
        '**/main.ts',
        '**/types.ts',
      ],
    },
  },
});
```

* Add to `package.json`:

```json
"test": "vitest --reporter=verbose"
```

* Install JSDOM:

```bash
npm install --save-dev jsdom
```

* Create `./tests/unit/example.test.ts`:

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

* Create `vitest.config.ts` and `./tests/setup-test-environment.ts` as per [React Testing Library + Vitest setup](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025).

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

* Update `playwright.config.ts` to include the following:

```ts
// /playwright.config.ts

import { defineConfig, devices } from '@playwright/test';

/**  
 * @see https://playwright.dev/docs/test-configuration  
 */  
export default defineConfig({  
 testDir: './tests/e2e',  
 // The maximum time one test can run for.  
 timeout: 30 * 1000,  
 expect: {  
 // The maximum time `expect` should wait for a condition to be met.  
 timeout: 5000,  
 },  
 // Run tests in files in parallel.
fullyParallel: true,  
 // Fail the build on CI if you accidentally left `test.only` in the source code.  
 forbidOnly: !!process.env.CI,  
 // Retry on CI only.  
 retries: process.env.CI ? 2 : 0,  
 // Opt out of parallel tests on CI.  
 workers: process.env.CI ? 1 : undefined,  
 // Reporter to use.  
 reporter: 'html',

// Shared settings for all the projects below.  
 use: {  
 // Base URL to use in actions like `await page.goto('/')`.  
 baseURL: 'http://localhost:3000',

    // Collect trace when retrying the failed test.
    trace: 'on-first-retry',

},

// Configure projects for major browsers.
projects: [  
 {  
 name: 'chromium',  
 use: { ...devices['Desktop Chrome'] },  
 },  
 {  
 name: 'firefox',  
 use: { ...devices['Desktop Firefox'] },  
 },  
 {  
 name: 'webkit',  
 use: { ...devices['Desktop Safari'] },  
 },  
 ],

// Run a local dev server before starting the tests.  
 webServer: {  
 command: 'npm run dev',
 url: 'http://localhost:3000',
 reuseExistingServer: !process.env.CI,
 },  
});
```

* Create a `./tests/e2e/example.spec.ts` file.

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
npm install -D tailwindcss @tailwindcss/postcss autoprefixer
```

* Initialize Tailwind CSS:

```bash
npx tailwindcss init -p
```

* Add to `globals.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

* Update `tailwind.config.js`:

```js
import type { Config } from 'tailwindcss';

export default {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        background: 'var(--background)',
        foreground: 'var(--foreground)',
      },
    },
  },
  plugins: [],
} satisfies Config;
```

* Update `postcss.config.mjs`:

```js
const config = {
  plugins: {
    '@tailwindcss/postcss': {},
    autoprefixer: {},
  },
};

export default config;
```

### Shadcn UI

* Install Shadcn UI:

```bash
npx shadcn@canary init --defaults
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

## 13. File Content

* Create commont content for the following files:

- `./src/utils/utils.ts`
- `./src/utils/formatters.ts`
- `./src/utils/validators.ts`
- `./src/types/types.ts`

## 14. Deployment

* Deploy to [Vercel](https://vercel.com/) for best Next.js support.

## References

* [How To Set Up Next.js 15 For Production In 2025](https://janhesters.com/blog/how-to-set-up-nextjs-15-for-production-in-2025)
* [Next.js Official Docs](https://nextjs.org/docs)

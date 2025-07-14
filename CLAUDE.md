# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Core Philosophy: "Clarity as a Feature"

This project follows a semantic architecture where the purpose of any file or folder is immediately apparent from its location and name. Every architectural decision prioritizes clarity and predictability, benefiting both human developers and AI collaborators.

## Project Overview

This is a Next.js 15 project template that uses:
- React 19 with React Server Components (RSC)
- TypeScript v5 with strict mode
- Tailwind CSS v4 + shadcn/ui
- App Router architecture
- Turbopack for development
- Vitest for unit testing
- Playwright for E2E testing
- Zustand for global state management

## Common Commands

### Development
```bash
npm run dev        # Start development server with Turbopack
npm run build      # Build for production
npm run start      # Start production server
npm run lint       # Run ESLint
npm run format     # Run Prettier
npm run type-check # Run TypeScript type checking
```

### Testing
```bash
npm run test        # Run unit tests with Vitest
npm run test:e2e    # Run Playwright E2E tests
npm run test:watch  # Run tests in watch mode
npm run coverage    # Generate test coverage report
```

### Git Hooks
```bash
npm run prepare     # Set up Husky git hooks
```

## Architecture

### Foundational Structure

**Mandatory `/src` Directory**: All application source code must reside within `/src` for clean separation from root-level configuration files.

### Complete Directory Structure

| Path | Purpose | Key Principles |
|------|---------|----------------|
| `/` | Project Root | Configuration files, package.json, and src directory |
| `/public` | Static Assets | Files served directly (images, fonts, robots.txt) |
| `/src/app` | Application Routes | App Router implementation with file-system routing |
| `/src/app/(group)` | Route Groups | Logical organization without affecting URL paths |
| `/src/app/api` | API Route Handlers | Backend API endpoints (GET, POST handlers) |
| `/src/app/[route]/_*` | Private Co-located Folders | Route-specific components/actions (not routed) |
| `/src/components/ui` | Universal UI Components | Stateless, reusable primitives (Button, Card, Input) |
| `/src/components/features` | Shared Feature Components | Domain-specific components used across multiple routes |
| `/src/components/layout` | Layout Components | Major structural elements (Header, Footer, Sidebar) |
| `/src/lib` | Core Libraries & Services | DB clients, API clients, auth helpers, singleton instances |
| `/src/lib/actions` | Global Server Actions | 'use server' functions accessible across the application |
| `/src/lib/store` | Global State Management | Zustand store and related hooks |
| `/src/hooks` | Custom React Hooks | Reusable hooks (useMediaQuery, useLocalStorage) |
| `/src/utils` | Utility Functions | Simple, pure, stateless helper functions |
| `/src/styles` | Global & Theming Styles | globals.css, theme variables |
| `/src/types` | Global TypeScript Types | Shared type definitions and interfaces |
| `/tests/unit` | Unit & Integration Tests | Vitest tests mirroring /src structure |
| `/tests/e2e` | End-to-End Tests | Playwright tests for user journeys |

### The App Router: Routes as Self-Contained Features

Each route segment is treated as a self-contained micro-feature using **private folders** (prefixed with `_`):

```
src/app/dashboard/settings/
├── _actions/
│   └── update-profile.ts   # Server Action for this page
├── _components/
│   ├── ProfileForm.tsx     # Component used only on this page
│   └── BillingInfo.tsx     # Component used only on this page
├── _hooks/
│   └── useBillingData.ts   # Hook specific to this page
└── page.tsx                # The page component itself
```

This co-location pattern provides:
- Clear boundaries for route-specific code
- Confidence when modifying components
- Optimal context for AI assistants

### Component Hierarchy

1. **`/src/components/ui`**: Universal, stateless, application-agnostic UI primitives from shadcn/ui
2. **`/src/components/features`**: Complex, domain-specific components shared across multiple routes
3. **`/src/components/layout`**: Major structural components defining the application chrome

### The lib vs utils Distinction

- **`/src/lib`**: Complex, foundational, often stateful or singleton-based code
  - `/lib/db`: Database connection and ORM instances
  - `/lib/auth`: Authentication helpers and session management
  - `/lib/api`: Third-party API clients
  - `/lib/actions`: Globally reusable Server Actions
  - `/lib/store`: Zustand global state store

- **`/src/utils`**: Simple, pure, stateless helper functions
  - `/utils/formatters.ts`: Date, currency formatting functions
  - `/utils/validators.ts`: Simple data validation functions
  - `/utils/cn.ts`: Tailwind class merging utility

## Modern Data and State Patterns

### Data Fetching Philosophy: Server-First

1. **Default to Server Components** - Fetch data on the server for performance, security, and SEO
2. **Client-side fetching only when necessary** - For real-time updates or user-specific interactions
3. **Parallel data fetching** - Use Promise.all to avoid request waterfalls

### Next.js 15 Data Patterns

```typescript
// Server Component with data fetching
export default async function ProductPage({ params }: { params: { id: string } }) {
  // Parallel data fetching
  const [product, reviews] = await Promise.all([
    fetch(`/api/products/${params.id}`),
    fetch(`/api/products/${params.id}/reviews`)
  ]);
  
  return <ProductDetails product={product} reviews={reviews} />;
}
```

### Caching Strategy (Next.js 15)

GET requests are **uncached by default**. Be explicit about caching:

```typescript
// Cache indefinitely
fetch(url, { cache: 'force-cache' });

// Time-based revalidation (1 hour)
fetch(url, { next: { revalidate: 3600 } });

// Tag-based revalidation
fetch(url, { next: { tags: ['products'] } });
```

### Server Actions for Mutations

All data modifications use Server Actions with Zod validation:

```typescript
// src/app/products/_actions/update-product.ts
'use server';

import { z } from 'zod';
import { revalidatePath } from 'next/cache';

const updateProductSchema = z.object({
  id: z.string(),
  name: z.string().min(1),
  price: z.number().positive()
});

export async function updateProduct(formData: FormData) {
  const validated = updateProductSchema.parse({
    id: formData.get('id'),
    name: formData.get('name'),
    price: Number(formData.get('price'))
  });
  
  // Update in database
  await db.product.update(validated);
  
  // Revalidate the product page
  revalidatePath(`/products/${validated.id}`);
}
```

### State Management Hierarchy

Follow this order for choosing state solutions:

1. **URL State** - For shareable, bookmarkable state (filters, search queries)
2. **Local Component State** - Default for component-specific state
3. **React Context** - For small component subtrees (avoid high-frequency updates)
4. **Zustand (Global State)** - Only for truly global state (auth, cart)

### Zustand Configuration

```typescript
// src/lib/store/index.ts
import { create } from 'zustand';

interface StoreState {
  user: User | null;
  cart: CartItem[];
  setUser: (user: User | null) => void;
  addToCart: (item: CartItem) => void;
}

export const useStore = create<StoreState>((set) => ({
  user: null,
  cart: [],
  setUser: (user) => set({ user }),
  addToCart: (item) => set((state) => ({ 
    cart: [...state.cart, item] 
  }))
}));
```

## AI Collaboration Guidelines

### Core Principles

1. **Server Components by Default** - Only use 'use client' for interactivity
2. **Aggressive Co-location** - Place route-specific code in private `_` folders
3. **Data Mutations via Server Actions** - Always validate with Zod
4. **Path Aliases** - Always use `@/` for imports from `/src`

### Task-Based Instructions

#### Creating a New Page
```bash
# For /dashboard/analytics
1. Create: /src/app/dashboard/analytics/page.tsx
2. Make it an async Server Component by default
```

#### Adding Route-Specific Components
```bash
# Component only for analytics page
1. Create: /src/app/dashboard/analytics/_components/Chart.tsx
2. Import: import Chart from './_components/Chart';
```

#### Creating Reusable UI Components
```bash
# Global UI component
1. Create: /src/components/ui/avatar/Avatar.tsx
2. Must be stateless and include 'use client' if interactive
```

#### Implementing Server Actions
```bash
# Form submission handler
1. Create: /src/app/settings/_actions/update-profile.ts
2. Start with 'use server';
3. Validate inputs with Zod
4. Use in form: <form action={updateProfile}>
```

### Critical Guardrails

1. **NEVER** use browser APIs (window, document) in Server Components
2. **ALWAYS** validate Server Action inputs as untrusted
3. **NEVER** use long relative imports - use `@/` path alias
4. **ALWAYS** place tests in `/tests/unit` or `/tests/e2e`

## Testing Strategy

### Unit Testing with Vitest
- Place tests in `/tests/unit` mirroring `/src` structure
- Focus on utilities, hooks, and component logic
- Use React Testing Library for component tests

### E2E Testing with Playwright
- Place tests in `/tests/e2e`
- Test complete user journeys
- Run against all major browsers (Chromium, Firefox, WebKit)

## Tooling Choices

### Why Vitest over Jest
- Faster execution using Vite's engine
- Near-identical API for easy migration
- Minimal configuration required

### Why Playwright over Cypress
- **Free parallel execution** (vs paid in Cypress)
- **Better architecture** - external control via CDP
- **Full browser support** - Chromium, Firefox, WebKit
- **Native multi-tab support** - essential for OAuth flows

## Important Configuration Files

- `next.config.ts` - Next.js 15 configuration with strict mode
- `tsconfig.json` - TypeScript with strict settings and path aliases
- `eslint.config.mjs` - ESLint v9 flat config format
- `tailwind.config.ts` - Tailwind v4 with shadcn/ui setup
- `vitest.config.mts` - Vitest configuration for unit tests
- `playwright.config.ts` - Playwright configuration for E2E tests

## Code Quality Checks

Always run before completing any task:
```bash
npm run lint        # Must pass with no errors
npm run type-check  # Must pass with no errors
npm run test        # All tests must pass
npm run format      # Format code with Prettier
```
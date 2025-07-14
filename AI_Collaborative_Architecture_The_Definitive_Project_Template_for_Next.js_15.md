# **An AI-Collaborative Architecture: The Definitive Project Template for Next.js v15**

### **Introduction: Building for the Future with Next.js v15**

This report presents a definitive project template for building scalable, maintainable, and high-performance applications with Next.js version 15 and React 19\. The architecture detailed herein is engineered not only for human developer productivity but is also explicitly designed for seamless collaboration with advanced AI coding assistants. In an era where AI is becoming a core member of the development team, the structure of our projects must evolve to facilitate this new partnership.

The core philosophy of this template is **Clarity as a Feature**. Every decision, from directory structure to tooling choices, is made to foster a "semantic architecture"—a project where the purpose of any file or folder is immediately apparent from its location and name. This predictability is paramount. For human developers, it reduces cognitive overhead and onboarding time. For AI assistants, it provides the critical context needed to generate accurate, idiomatic, and safe code, transforming the AI from a generic tool into a context-aware contributor.

This document will guide you through every layer of the template. It begins with the foundational directory structure, establishing a logical map for the entire codebase. It then delves into the modern data and state management patterns that leverage the full power of Next.js's server-centric model. Following this, it prescribes a complete, production-ready toolchain for ensuring code quality, consistency, and robust testing. Finally, it culminates in a practical, actionable set of annotated configuration files and a comprehensive prompt designed to onboard an AI collaborator, such as Claude Cloud, Gemini, ChatGPT, Grok, onto any project built with this template.

## **Section 1: The Foundational Architecture: A Blueprint for Clarity and Scale**

The foundation of any scalable application is a logical and predictable structure. This section establishes the non-negotiable architectural elements that create a clear "map" of the codebase, eliminating ambiguity and providing a solid framework for growth.

### **1.1 The `src` Directory: A Mandate for Clean Separation**

The first and most fundamental decision in structuring a Next.js project is the adoption of a `src` directory. While Next.js can function without it, mandating its use is a critical best practice for any serious application. Placing all application source code within `/src` provides a clean and unambiguous separation between the code that developers write and the project's root-level configuration files (e.g., next.config.ts, package.json, tsconfig.json).

This separation offers several distinct advantages:

- **Reduced Root Clutter:** It keeps the project's root directory clean and focused on configuration, making it easier to navigate.
- **Improved Tooling Integration:** Many development tools and scripts can be more easily configured to target the `src` directory, simplifying build and linting processes.
- **Ecosystem Consistency:** It aligns the project with a widely accepted convention in the broader JavaScript and TypeScript ecosystems, making the project more familiar to new developers.

For an AI coding assistant, this structural decision is a powerful initial signal. It can confidently assume that all application logic, components, and utilities reside within `/src`, which significantly narrows its search scope when asked to modify or create files, leading to faster and more accurate code generation.

### **1.2 The Anatomy of the Project Root and Core Directories**

A well-organized project provides a clear purpose for every directory. This eliminates the "miscellaneous" folder problem, where developers are unsure where to place new files, leading to entropy over time. The following structure defines a clear contract for organization, serving as a quick-reference glossary for the entire project.

| Path                     | Purpose                    | Key Principles                                                                                                                        |
| :----------------------- | :------------------------- | :------------------------------------------------------------------------------------------------------------------------------------ |
| /                        | Project Root               | Contains configuration files, package.json, and the src directory.                                                                    |
| /public                  | Static Assets              | For files served directly, like images, fonts, and robots.txt. Path is stable.                                                        |
| /src/app                 | Application Routes         | App Router implementation. File-system based routing, layouts, and pages.                                                             |
| /src/app/(group)         | Route Groups               | Organizes routes logically (e.g., (auth), (marketing)) without affecting the URL path.                                                |
| /src/app/api             | API Route Handlers         | For creating backend API endpoints. Defines GET, POST, etc., handlers in route.ts files.                                              |
| /src/app/\[route\]/\_    | Private Co-located Folders | Folders prefixed with underscore (i.e. \_) (e.g., \_components, \_actions) are not routed. Used for co-locating route-specific logic. |
| /src/components/ui       | Universal UI Components    | Stateless, highly reusable UI primitives (e.g., Button, Card, Input). Often from shadcn/ui.                                           |
| /src/components/features | Shared Feature Components  | Domain-specific components shared across _multiple_ routes (e.g., ProductGrid, CheckoutForm).                                         |
| /src/components/layout   | Layout Components          | Major structural elements like Header, Footer, Sidebar.                                                                               |
| /src/lib                 | Core Libraries & Services  | Foundational code: DB clients (Prisma/Drizzle), API clients, auth helpers, singleton instances.                                       |
| /src/lib/actions         | Global Server Actions      | 'use server' functions for mutations, accessible across the application.                                                              |
| /src/lib/store           | Global State Management    | Location for the Zustand state store and related hooks.                                                                               |
| /src/hooks               | Custom React Hooks         | Reusable hooks (e.g., useMediaQuery, useLocalStorage) that are not tied to a specific component.                                      |
| /src/utils               | Utility Functions          | Simple, pure, stateless helper functions (e.g., formatters, validators).                                                              |
| /src/styles              | Global & Theming Styles    | Contains globals.css, theme variables, etc..                                                                                          |
| /src/types               | Global TypeScript Types    | Shared type definitions and interfaces used across the application.                                                                   |
| /tests/unit              | Unit & Integration Tests   | Vitest tests for individual components, functions, and hooks. Mirrors the /src structure.                                             |
| /tests/e2e               | End-to-End Tests           | Playwright tests simulating full user journeys across the application.                                                                |

### **1.3 Mastering the App Router: Routes as Self-Contained Features**

The Next.js App Router encourages a feature-based organization by mapping the file system directly to URL routes. This template extends that paradigm by treating each route segment as a self-contained micro-feature, achieved through aggressive co-location of related logic.

The primary mechanism for this is the use of **private folders**. Any folder within the app directory prefixed with an underscore (e.g., \_components) is guaranteed by Next.js to be excluded from routing. This powerful feature allows us to place components, server actions, hooks, and utilities that are _only_ used by a specific route (and its children) directly alongside the page.tsx that needs them.

Consider a settings page at /dashboard/settings. The file structure would be:

```
src/app/dashboard/settings/
├── \_actions/
│   └── update-profile.ts  // Server Action for this page
├── \_components/
│   ├── ProfileForm.tsx    // Component used only on this page
│   └── BillingInfo.tsx    // Component used only on this page
├── \_hooks/
│   └── useBillingData.ts  // Hook specific to this page
└── page.tsx               // The page component itself
```

This approach provides immense benefits for both scalability and collaboration. As an application grows, a single global /components directory can become a "component file explosion," making it impossible to know which components are safe to modify or delete. The co-location pattern solves this by creating clear boundaries. A developer working on the settings page knows that the components in /app/dashboard/settings/\_components are directly relevant and can be modified with confidence, drastically reducing cognitive load.

This explicit context is a game-changer for AI collaboration. When an AI assistant is tasked with "adding a password change feature to the settings page," it doesn't need to perform a project-wide search for relevant files. It can infer with high probability that it should create a new component within the local \_components folder and a new server action within the local \_actions folder. This makes the AI's contributions faster, more accurate, and less prone to causing unintended side effects in other parts of the application. It elevates the AI from a general-purpose coder to a context-aware project specialist.

### **1.4 A Scalable Component Model: ui, features, and layout**

While co-location is preferred for route-specific logic, some components are designed for reuse across the application. To prevent these from becoming disorganized, this template defines a clear, three-tiered hierarchy within the global /src/components directory:

1. **/src/components/ui**: This directory is for universal, stateless, and application-agnostic UI primitives. These are the fundamental building blocks of the design system: Button, Input, Card, Modal, Dialog, etc.. They should be pure presentation and contain no business logic. This is the ideal location for components generated by libraries like shadcn/ui, which provides unstyled, copy-pasteable component code that you own and can customize.
2. **/src/components/features**: This directory holds more complex, domain-specific components that encapsulate a piece of business logic and are shared across _multiple_ distinct routes. For example, a ProductCard component might be used on the homepage, a category page, and in search results. It is not universal like a button, but it is not specific to a single route. Other examples include UserAvatarWithProfile, ShoppingCartSummary, or ArticlePreview.
3. **/src/components/layout**: This directory is reserved for major structural components that define the overall chrome of the application, such as Header, Footer, Sidebar, and Navigation. These components are typically used within the root or nested layout.tsx files.

### **1.5 The lib and utils Directories: The Central Nervous System**

To avoid the "Utils Black Hole"—a single, monolithic utils.ts file that grows to thousands of lines—this template establishes a clear distinction between the /lib and /utils directories and mandates internal organization.\[2\]

- **/src/lib**: This directory is for more complex, foundational, and often stateful or singleton-based code. It is the home for the application's core services.
  - /lib/db: Database connection clients and ORM instances (e.g., Prisma Client, Drizzle instance).
  - /lib/auth: Authentication helpers, session management, and integrations with services like NextAuth.js or Lucia.
  - /lib/api: Clients for interacting with third-party APIs.
  - /lib/actions: Home for _globally reusable_ Server Actions.
  - /lib/store: The global Zustand state management store.
- **/src/utils**: This directory is strictly for simple, pure, and stateless helper functions. These functions should have no side effects and be easily testable in isolation.
  - /utils/formatters.ts: Functions for formatting dates, currency, etc.
  - /utils/validators.ts: Simple data validation functions (distinct from schema validation with Zod).
  - /utils/cn.ts: The cn utility for merging Tailwind CSS classes.

This clear separation ensures that the application's core logic is organized and discoverable, while simple helper functions are kept distinct and easy to reuse.

## **Section 2: The Modern Data and State Layer**

Next.js 15, with its deep integration of React 19 and the App Router, champions a server-centric approach to application development. This section outlines the patterns for data and state management that align with this modern paradigm, prioritizing performance, security, and developer experience.

### **2.1 Data Fetching Philosophy: Server-First, Client-When-Necessary**

The fundamental principle of data fetching in this architecture is to perform it on the server by default. React Server Components (RSCs) are the primary vehicle for this approach. Fetching data on the server offers compelling advantages:

- **Performance:** Data is fetched closer to the data source, reducing latency. The client receives a fully-rendered HTML payload, leading to faster initial page loads and a better user experience.
- **Security:** Sensitive information, such as database credentials and API keys, remains securely on the server and is never exposed to the client's browser.
- **SEO:** Search engine crawlers receive complete HTML content, which is optimal for indexing, unlike client-rendered applications that may present an empty shell initially.
- **Reduced Client-Side Code:** Moving data fetching logic to the server reduces the amount of JavaScript that needs to be downloaded and executed by the client.

Client-side data fetching, using hooks like useEffect or libraries like SWR/React Query within a 'use client' component, is still a valuable pattern, but it should be reserved for specific use cases. These include:

- Data that is highly dynamic and changes frequently after the initial page load (e.g., stock tickers, real-time chat messages).
- Data that is specific to a user's interaction on the client (e.g., fetching search results as a user types).
- Data that is not critical for the initial render and can be loaded lazily.

### **2.2 Practical Data Fetching Patterns in Next.js 15**

This template advocates for a set of clear, idiomatic patterns for handling data.

- **Server Components with async/await**: The default and simplest pattern for fetching data needed for a page's initial render is to make the page component async and use await with the native fetch API. React 19 extends fetch with automatic request deduplication. If multiple components in the same render tree request the same URL, React will intelligently make only one network request and share the result, eliminating concerns about redundant fetches.
- **Caching and Revalidation**: Next.js 15 introduces a significant change: GET requests made via fetch in Server Components and in Route Handlers are **uncached by default** (cache: 'no-store'). This ensures data freshness but requires developers to be explicit about caching. To opt into caching, you can use:
  - fetch(url, { cache: 'force-cache' }): Caches the data indefinitely.
  - fetch(url, { next: { revalidate: 3600 } }): Implements time-based revalidation, fetching new data at most once every hour.
  - fetch(url, { next: { tags: \['my-tag'\] } }): Tags a fetch request for on-demand revalidation using revalidateTag() in a Server Action or API route.
- **Parallel Data Fetching**: To avoid "request waterfalls" where sequential await calls block each other, independent data dependencies for a page should be fetched in parallel using Promise.all. This ensures that all data requests are initiated simultaneously, significantly reducing the total data-loading time for the page.
- **Server Actions for Mutations**: For any operation that modifies data (e.g., creating, updating, or deleting), **Server Actions** are the canonical solution. These are server-side functions that can be invoked directly from client components, typically through form submissions or button clicks.
  - **Security:** Server Actions are secure by design. They execute on the server, never in the browser, so they can safely contain database queries and secret credentials.
  - **Validation:** It is absolutely critical to treat all input to a Server Action as untrusted. All data coming from the client must be validated on the server before being used in a database query. Libraries like Zod are highly recommended for this purpose.

### **2.3 A Pragmatic Guide to State Management**

The introduction of React Server Components necessitates a new mental model for state management. The old approach of placing everything in a single global client-side store is no longer viable or desirable. This template codifies a clear set of rules for handling state:

1. **Rule \#1: No Global Stores on the Server.** A global variable representing a store on the server can lead to data contamination between different users' concurrent requests. State management libraries must be instantiated on a per-request basis to ensure isolation.
2. **Rule \#2: RSCs Display Immutable Data.** Server Components are for fetching and displaying data that is considered immutable for the duration of a user's session on that page (e.g., a blog post's content, product details). They do not have "state" in the traditional React sense.
3. **Rule \#3: Client Components Manage Mutable State.** Any state that changes in response to user interaction (e.g., form inputs, toggles, shopping cart contents) belongs in components marked with the 'use client' directive.

Following these rules, developers should adopt a "least powerful tool first" hierarchy for managing state:

1. **URL State:** For state that should be bookmarkable, shareable, and reflected in the URL (e.g., filters, search queries, pagination), use URL Search Parameters.
2. **Local Component State (useState, useReducer):** This is the default for state that is confined to a single component and is not needed by its parents or siblings.
3. **React Context:** For sharing state across a small, localized subtree of components (e.g., theme state within a settings panel) where prop-drilling would be cumbersome. Avoid using Context for high-frequency updates, as it can cause performance issues.
4. **Global State Library (Zustand):** This should only be used when state needs to be shared across disparate parts of the application that do not have a convenient shared parent, such as user authentication status or the contents of a shopping cart.

### **2.4 Selecting a Global State Manager: Zustand vs. Jotai**

For cases requiring a global state manager, the modern landscape has moved beyond the boilerplate-heavy patterns of Redux. The research points to Zustand and Jotai as the leading lightweight, performant alternatives. While both are excellent, this template explicitly recommends **Zustand** for its simplicity and alignment with the goal of an easily understood, collaborative architecture.

| Feature/Aspect            | Zustand                                                                                         | Jotai                                                                                   |
| :------------------------ | :---------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------- |
| **Core Concept**          | Single store with hook-based access (top-down).                                                 | Atomic state model with composable atoms (bottom-up).                                   |
| **API Style**             | Define a store object with state and actions. Access with useStore(state \=\> state.piece).     | Define individual atoms. Access with useAtom(myAtom).                                   |
| **Bundle Size**           | \~4KB (minified \+ gzipped).                                                                    | \~4KB (minified \+ gzipped).                                                            |
| **Performance Model**     | Optimizes re-renders via selectors. Components re-render only if the selected state changes.    | Automatic, granular re-renders. Components re-render only if a subscribed atom changes. |
| **Developer Tools**       | Integrates with Redux DevTools for time-travel debugging.                                       | Integrates with React DevTools for visualizing atom dependencies.                       |
| **Server-Side Rendering** | Simple to implement; create a new store instance for each server request to avoid data leakage. | Integrates deeply with React Suspense for handling async atoms.                         |

While Jotai's atomic model offers incredibly granular performance optimizations, its bottom-up approach represents a paradigm shift that can have a steeper learning curve for teams accustomed to centralized stores like Redux or Context.

Zustand's model, in contrast, is conceptually simpler and more familiar. It is essentially a lightweight, unopinionated version of a Redux store, but without the mandatory boilerplate of actions, reducers, and dispatchers. The mental model is straightforward: "there is a single object that holds our global state."

This simplicity and predictability make Zustand the superior choice for a template designed for broad organizational adoption and AI collaboration. It is easier to teach, easier to reason about, and provides a clear, single location (/src/lib/store) for any developer—human or AI—to find and modify global state. This constraint reduces ambiguity and aligns perfectly with the template's core philosophy of "Clarity as a Feature."

## **Section 3: The Toolchain for Quality and Velocity**

A robust project template extends beyond file structure; it encompasses the entire toolchain that ensures code quality, consistency, and a productive developer experience. This section prescribes a set of modern, best-in-class tools for styling, testing, and code hygiene.

### **3.1 Styling Strategy: Utility-First with Tailwind CSS and shadcn/ui**

For styling, this template advocates a utility-first approach, which has become the industry standard for building modern, maintainable user interfaces.

- **Tailwind CSS**: Tailwind CSS is the recommended primary styling engine. Instead of writing custom CSS classes, developers compose interfaces by applying pre-existing utility classes directly in the markup. This approach offers significant benefits:
  - **Development Speed:** It dramatically speeds up development by eliminating the need to switch contexts between HTML/JSX and CSS files.
  - **Consistency:** It enforces a consistent design system by providing a constrained set of design tokens (spacing, colors, typography).
  - **Performance:** Tailwind automatically removes all unused CSS classes during the build process, resulting in the smallest possible CSS bundle.
  - **Maintainability:** Styles are co-located with their elements, making components truly self-contained and preventing style collisions or unintended side effects from global stylesheet changes.
- **shadcn/ui**: This template recommends using shadcn/ui as the foundation for the component library. Crucially, shadcn/ui is not a traditional component library that you install as a dependency. Instead, it provides a set of beautifully designed, accessible component "recipes" that you copy and paste directly into your project. The code becomes part of your own codebase, located in /src/components/ui. This approach is ideal for a long-term project because it avoids vendor lock-in and gives the team complete ownership and control to modify the components as needed.
- **Hybrid Styling**: While utility classes cover most use cases, there are times when custom CSS is necessary. For these one-off situations, this template supports **CSS Modules**. By creating a file named \*.module.css, developers can write traditional, scoped CSS that won't conflict with any other styles in the application. This hybrid approach offers the best of both worlds: the speed of utilities and the power of custom CSS when required.

### **3.2 A Comprehensive Testing Pyramid**

A reliable application requires a multi-layered testing strategy. This template adopts the "Testing Trophy" model, which balances different types of tests to maximize confidence and minimize cost.

#### **3.2.1 Static & Unit/Integration Testing: TypeScript, Vitest, and React Testing Library**

The base of the testing pyramid consists of fast, targeted tests that run frequently during development.

- **TypeScript**: The first line of defense is a strong type system. TypeScript catches an entire class of errors at compile time, long before the code is ever run.
- **Vitest**: For the test runner, this template strongly recommends **Vitest** over the more traditional Jest. While Jest is a capable tool, Vitest offers a superior developer experience for modern projects. It leverages the speed of Vite's engine, provides a nearly identical API to Jest for easy migration, and requires minimal configuration.
- **React Testing Library (RTL)**: RTL is the standard for testing React components. Its guiding principle is to test components in a way that resembles how a user would interact with them, focusing on behavior rather than implementation details. This leads to more resilient tests that don't break during minor refactors.

Unit and integration tests written with Vitest and RTL should be placed in a /tests/unit directory that mirrors the /src directory structure, ensuring tests are easy to locate.

#### **3.2.2 End-to-End Testing: A Clear Case for Playwright**

At the top of the pyramid are end-to-end (E2E) tests, which simulate complete user journeys through the live application. For this critical task, the research and industry trends point decisively to **Playwright** as the superior choice over its main competitor, Cypress.

| Criterion              | Playwright                                                                                      | Cypress                                                                                   |
| :--------------------- | :---------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------- |
| **Architecture**       | External control via Chrome DevTools Protocol (CDP). More realistic simulation.                 | Runs _inside_ the browser's execution loop. Can lead to discrepancies.                    |
| **Browser Support**    | All modern engines: Chromium (Chrome, Edge), Firefox, and WebKit (Safari).                      | Limited. Primarily Chromium-based. WebKit support is experimental and less mature.        |
| **Parallel Execution** | **Free and built-in**. Drastically reduces CI/CD run times and costs.                           | **Paid feature** via Cypress Cloud Dashboard or requires complex self-hosted workarounds. |
| **Async Handling**     | Uses standard JavaScript async/await. Familiar and flexible for developers.                     | Uses a custom, non-standard command chain (.then()). Can be confusing and limiting.       |
| **Multi-Tab/iFrame**   | Native, robust support for multiple tabs, popups, and iframes. Essential for modern auth flows. | Not supported or very difficult to work with due to its in-browser architecture.          |
| **Tooling Ecosystem**  | Backed by Microsoft, rapidly evolving, with features like Code Gen and Trace Viewer.            | Mature ecosystem, but the core architecture imposes fundamental limitations.              |

The choice of Playwright is not merely a preference; it is a strategic decision based on its fundamentally more capable architecture. Playwright's external control model allows it to automate the browser in the same way a real user would, without injecting itself into the application's context. This is what enables its superior cross-browser support and its native ability to handle complex, multi-tab scenarios like OAuth authentication flows—a common requirement that is notoriously difficult to test in Cypress.

Furthermore, Playwright's inclusion of free, built-in test parallelization is a massive advantage for any project concerned with CI/CD costs and feedback loop times. For a template that must be robust, future-proof, and capable of testing any feature a modern web application might require, Playwright is the clear and definitive choice.

### **3.3 Ensuring Code Consistency: ESLint, Prettier, and Commit Hooks**

To maintain a clean and consistent codebase across a team of developers (including AI), an automated system for code hygiene is essential.

- **ESLint & Prettier**: ESLint is used for static analysis to find potential bugs and enforce coding conventions, while Prettier is an opinionated code formatter that ensures a consistent style. This template uses the modern eslint.config.mjs flat configuration format, which is the new standard for ESLint.
- **Commit Hooks**: To automate this process, the template recommends using a tool like **Husky** or **Lefthook** in combination with **lint-staged**. This setup creates a pre-commit hook that automatically runs ESLint and Prettier on any staged files before a commit is allowed. This guarantees that no poorly formatted or lint-error-containing code ever enters the version control history, enforcing quality at the source.

## **Section 4: The Template in Practice: Annotated Configuration Files**

This section provides the complete, production-ready code for the key configuration files that underpin the template. Each file is exhaustively commented to explain the rationale behind every setting, serving as both a practical boilerplate and a learning resource.

### **4.1 next.config.ts**

This file configures the core behavior of the Next.js framework. Next.js 15 introduced stable support for using TypeScript in this file, which is a significant improvement for type safety.

```
// /next.config.ts

import type { NextConfig } from 'next';

/\*\*  
 \* @type {import('next').NextConfig}  
 \*  
 \* The configuration for the Next.js application.  
 \* Next.js 15 supports using a TypeScript file for configuration, providing type safety.  
 \* @see https://nextjs.org/docs/app/api-reference/config/next-config-js  
 \*/  
const nextConfig: NextConfig \= {  
 // React's Strict Mode is a development-only feature that helps identify potential problems  
 // in an application. It activates additional checks and warnings for its descendants.  
 // It is highly recommended to keep this enabled.
reactStrictMode: true,

// Configuration for the ESLint linter.  
 eslint: {  
 // By default, Next.js lints the \`pages\`, \`app\`, \`components\`, \`lib\`, and \`src\` directories.  
 // This configuration extends linting to our custom \`/tests\` directory during \`next build\`.
dirs: \['src', 'tests'\],  
 },

// Experimental features that can be opted into. Use with caution as APIs may change.  
 experimental: {  
 // Enables the \`after()\` API, which allows scheduling work to be processed \*after\* a response  
 // has finished streaming. Useful for tasks like logging or analytics that shouldn't block  
 // the main response.
after: true,

    // The React Compiler is an experimental compiler from Meta that automatically optimizes
    // React code, reducing the need for manual memoization with \`useMemo\` and \`useCallback\`.
    // Enabling this can lead to performance improvements and simpler code.
    // compiler: true, // Uncomment when the compiler is stable and widely adopted.

},

// In Next.js 15, GET Route Handlers are uncached by default. This configuration is an example  
 // of how you could opt back into the previous caching behavior if needed, though the  
 // recommended approach is to be explicit with \`fetch\` caching options.
// experimental: {  
 // staleTimes: {  
 // dynamic: 30, // Cache dynamic pages for 30 seconds  
 // static: 180, // Cache static pages for 180 seconds  
 // },  
 // },  
};

export default nextConfig;
```

### **4.2 tsconfig.json**

This file configures the TypeScript compiler (tsc), defining how it should check and transpile the project's code. A well-configured tsconfig.json is the foundation of a type-safe application.

```
// /tsconfig.json  
{  
 "compilerOptions": {  
 /\* Type Checking \*/  
 "strict": true, // Enables all strict type-checking options. This is the cornerstone of type safety.
"noImplicitAny": true, // Raise error on expressions and declarations with an implied 'any' type.  
 "strictNullChecks": true, // When true, \`null\` and \`undefined\` have their own distinct types.  
 "noUnusedLocals": true, // Report errors on unused local variables.  
 "noUnusedParameters": true, // Report errors on unused parameters.  
 "noFallthroughCasesInSwitch": true, // Report errors for fallthrough cases in switch statement.

    /\* Modules \*/
    "module": "esnext", // Specify what module code is generated. 'esnext' allows for modern module features.
    "moduleResolution": "bundler", // The recommended mode for modern bundlers like Webpack/Turbopack. It aligns with how Node.js resolves modules.
    "resolveJsonModule": true, // Allows importing \`.json\` files.
    "allowImportingTsExtensions": true, // Required for \`moduleResolution: "bundler"\` when using TypeScript files with extensions.

    /\* JavaScript Support \*/
    "allowJs": true, // Allow JavaScript files to be compiled.
    "checkJs": true, // Report errors in.js files.

    /\* Emit \*/
    "noEmit": true, // Do not emit output files (transpilation is handled by Next.js's compiler).
    "incremental": true, // Enable incremental compilation, speeding up subsequent builds.

    /\* Interop Constraints \*/
    "esModuleInterop": true, // Enables compatibility with CommonJS modules.
    "forceConsistentCasingInFileNames": true, // Ensure that casing is correct in imports.
    "isolatedModules": true, // Ensures that each file can be transpiled without relying on other imports.

    /\* Language and Environment \*/
    "target": "es5", // The language version for emitted JavaScript. 'es5' ensures broad browser compatibility.
    "lib": \["dom", "dom.iterable", "esnext"\], // Specifies the library files to be included in the compilation.
    "jsx": "preserve", // Do not transform JSX. Next.js's compiler handles this.

    /\* Path Aliases \*/
    "baseUrl": ".", // Base directory to resolve non-absolute module names.
    "paths": {
      "@/\*": \["./src/\*"\] // Sets up the '@/' alias to point to the 'src' directory for cleaner imports.
    },

    /\* Plugins \*/
    "plugins": \[
      {
        "name": "next"
      }
    \]

},  
 "include": \[  
 "next-env.d.ts",  
 ".next/types/\*\*/\*.ts",  
 "\*\*/\*.ts",  
 "\*\*/\*.tsx",  
 "\*\*/\*.cjs",  
 "\*\*/\*.mjs"  
 \],  
 "exclude": \["node_modules"\]  
}
```

### **4.3 eslint.config.mjs**

This file configures ESLint using the modern "flat config" format. It combines rules from multiple plugins to enforce a high standard of code quality and consistency.

```
// /eslint.config.mjs

import { FlatCompat } from '@eslint/eslintrc';  
import path from 'node:path';  
import { fileURLToPath } from 'node:url';  
import js from '@eslint/js';  
import tsEslint from 'typescript-eslint';  
import react from 'eslint-plugin-react';  
import reactHooks from 'eslint-plugin-react-hooks';  
import tailwind from 'eslint-plugin-tailwindcss';  
import unicorn from 'eslint-plugin-unicorn';  
import globals from 'globals';

// Recreate the \`\_\_dirname\` constant, which is not available in ES modules.  
const \_\_filename \= fileURLToPath(import.meta.url);  
const \_\_dirname \= path.dirname(\_\_filename);

// FlatCompat is a utility to use legacy \`.eslintrc\` format configs in the new flat config.  
// This is necessary for the \`eslint-config-next\` plugin, which has not fully migrated yet.
const compat \= new FlatCompat({  
 baseDirectory: \_\_dirname,  
});

/\*\* @type {import('eslint').Linter.FlatConfig\[\]} \*/  
export default \[  
 // Global ignores  
 {  
 ignores: \['node_modules/', '.next/', 'dist/'\],  
 },

// JavaScript recommended rules  
 js.configs.recommended,

// TypeScript recommended rules  
 ...tsEslint.configs.recommended,

// React recommended rules  
 {  
 files: \['\*\*/\*.{ts,tsx}'\],  
 plugins: {  
 react,  
 'react-hooks': reactHooks,  
 },  
 rules: {  
 ...react.configs.recommended.rules,  
 ...reactHooks.configs.recommended.rules,  
 'react/react-in-jsx-scope': 'off', // Not needed with Next.js 15 and React 19  
 'react/prop-types': 'off', // Handled by TypeScript  
 },  
 settings: {  
 react: {  
 version: 'detect', // Automatically detect the React version  
 },  
 },  
 },

// Tailwind CSS plugin rules  
 ...tailwind.configs.recommended,

// Unicorn plugin for more opinionated, powerful rules  
 unicorn.configs\['flat/recommended'\],

// Next.js core web vitals and recommended rules, loaded via FlatCompat  
 ...compat.config({  
 extends: \['next/core-web-vitals'\],  
 }),

// Global settings for all files  
 {  
 languageOptions: {  
 globals: {  
 ...globals.browser,  
 ...globals.node,  
 React: 'readonly',  
 },  
 },  
 },  
\];
```

### **4.4 vitest.config.mts**

This file configures Vitest for unit and integration testing. The .mts extension is used for TypeScript ES modules.

```
// /vitest.config.mts

import { defineConfig } from 'vitest/config';  
import react from '@vitejs/plugin-react';  
import tsconfigPaths from 'vite-tsconfig-paths';

/\*\*  
 \* @see https://vitest.dev/config/  
 \*/  
export default defineConfig({  
 plugins: \[react(), tsconfigPaths()\],  
 test: {  
 // The environment to run tests in. 'jsdom' simulates a browser environment.
environment: 'jsdom',

    // Expose global APIs (describe, it, expect) so they don't need to be imported in every test file.
    globals: true,

    // A file to run before each test file. Used for global setup.
    setupFiles: './tests/unit/setup.ts',

    // Configuration for test coverage reporting.
    coverage: {
      provider: 'v8',
      reporter: \['text', 'json', 'html'\],
      exclude: \[
        'node\_modules/',
        'tests/',
        '.next/',
        '\*\*/.\*.js',
        '\*\*/\*.config.\*',
        '\*\*/main.ts',
        '\*\*/types.ts',
      \],
    },

},  
});
```

### **4.5 playwright.config.ts**

This file configures Playwright for end-to-end testing, defining browser projects, server settings, and test execution options.

```
// /playwright.config.ts

import { defineConfig, devices } from '@playwright/test';

/\*\*  
 \* @see https://playwright.dev/docs/test-configuration  
 \*/  
export default defineConfig({  
 testDir: './tests/e2e',  
 // The maximum time one test can run for.  
 timeout: 30 \* 1000,  
 expect: {  
 // The maximum time \`expect\` should wait for a condition to be met.  
 timeout: 5000,  
 },  
 // Run tests in files in parallel.
fullyParallel: true,  
 // Fail the build on CI if you accidentally left \`test.only\` in the source code.  
 forbidOnly: \!\!process.env.CI,  
 // Retry on CI only.  
 retries: process.env.CI ? 2 : 0,  
 // Opt out of parallel tests on CI.  
 workers: process.env.CI ? 1 : undefined,  
 // Reporter to use.  
 reporter: 'html',

// Shared settings for all the projects below.  
 use: {  
 // Base URL to use in actions like \`await page.goto('/')\`.  
 baseURL: 'http://localhost:3000',

    // Collect trace when retrying the failed test.
    trace: 'on-first-retry',

},

// Configure projects for major browsers.
projects: \[  
 {  
 name: 'chromium',  
 use: { ...devices\['Desktop Chrome'\] },  
 },  
 {  
 name: 'firefox',  
 use: { ...devices\['Desktop Firefox'\] },  
 },  
 {  
 name: 'webkit',  
 use: { ...devices\['Desktop Safari'\] },  
 },  
 \],

// Run a local dev server before starting the tests.  
 webServer: {  
 command: 'npm run dev',  
 url: 'http://localhost:3000',  
 reuseExistingServer: \!process.env.CI,  
 },  
});
```

## **Section 5: The AI Collaborator: A Contextual Prompt for Claude**

This section provides a comprehensive "system prompt" designed to onboard an AI coding assistant, specifically Claude, to a project using this template. Providing this context at the beginning of a development session will enable the AI to act as an expert-level contributor that understands and adheres to the project's specific architecture and conventions.

**(Copy and paste the following text into your AI chat interface as a contextual prompt)**

You are an expert full-stack developer specializing in Next.js 15 and React 19\. You are joining our team and will be contributing to a project built on a specific, well-defined architectural template. Your primary goal is to write clean, efficient, and maintainable code that **strictly adheres** to our established patterns. Your adherence to these conventions is critical for maintaining the project's quality and consistency.

### **Core Architectural Principles**

1. **Server-First Mentality:** We use React Server Components (RSCs) by default. Data fetching for initial page loads happens in async Page components on the server. Client Components ('use client') are used only for interactivity and client-side state.
2. **Aggressive Co-location:** Logic specific to a route (components, actions, hooks) is placed inside private \_ folders (e.g., \_components/) within that route's directory in /src/app. This keeps features self-contained.
3. **Component Hierarchy:** We have a strict hierarchy for component placement:
   - /src/components/ui: Universal, stateless UI primitives (e.g., Button, Input).
   - /src/components/features: Shared, domain-specific components used across multiple pages.
   - /src/app/\[route\]/\_components: Components used _only_ by a specific route and its children.
4. **Data Mutations via Server Actions:** All data modifications (CUD operations) are handled by Server Actions ('use server'). These actions must validate their inputs using Zod.
5. **Utility-First Styling:** We use Tailwind CSS for all styling.

### **Project Directory Structure Guide**

Refer to this guide to determine where to place new files:

- **Routes & Pages:** /src/app/
- **API Endpoints:** /src/app/api/
- **Route-Specific Components/Actions:** /src/app/\[route\]/\_components/, /src/app/\[route\]/\_actions/
- **Universal UI Primitives:** /src/components/ui/
- **Shared Feature Components:** /src/components/features/
- **Global Layout Components:** /src/components/layout/
- **Database/Auth/API Clients:** /src/lib/
- **Global Server Actions:** /src/lib/actions/
- **Global State (Zustand):** /src/lib/store/
- **Reusable Custom Hooks:** /src/hooks/
- **Stateless Helper Functions:** /src/utils/
- **Global Styles:** /src/styles/
- **Unit/Integration Tests (Vitest):** /tests/unit/
- **End-to-End Tests (Playwright):** /tests/e2e/

### **Task-Based Instructions & How-To Guides**

Follow these procedures for common tasks:

- **To create a new page at /dashboard/analytics:**
  1. Create a new folder: /src/app/dashboard/analytics/.
  2. Add a page.tsx file inside it. This component should be a Server Component by default.
- **To add a component used ONLY on the analytics page:**
  1. Create a folder: /src/app/dashboard/analytics/\_components/.
  2. Create your component file inside, e.g., Chart.tsx.
  3. Import it into page.tsx using a relative path: import Chart from './\_components/Chart';.
- **To create a new, globally reusable UI component (e.g., an Avatar):**
  1. Create a new folder: /src/components/ui/avatar/.
  2. Create Avatar.tsx inside. It must be a Client Component ('use client') and should be stateless.
- **To fetch initial data for a page:**
  1. Make the page.tsx component async: export default async function AnalyticsPage() {... }.
  2. Use await fetch(...) directly inside the component.
  3. **DO NOT** use useEffect or client-side libraries like SWR/React Query for initial data fetching.
- **To handle a form submission:**
  1. Create a Server Action in a \_actions.ts file co-located with the route (e.g., /src/app/settings/\_actions/update-profile.ts).
  2. The action must start with the 'use server'; directive.
  3. Define a Zod schema for the form inputs and validate the data within the action before performing any database operations.
  4. Import the action into your form component and pass it to the \<form action={...}\> prop.
- **To add a new piece of global state (e.g., user preferences):**
  1. Locate the Zustand store at /src/lib/store/index.ts.
  2. Add the new state property and its updater function to the create call.

### **Code Style and Critical Guardrails**

- **Path Aliases:** ALWAYS use the @/ path alias for imports from the /src directory (e.g., import { Button } from '@/components/ui/button';). NEVER use long relative paths like ../../../components/ui/button.
- **Server/Client Boundary:** NEVER use browser-only APIs (like window, document, localStorage) in Server Components or Server Actions. This will cause a runtime error. All such code must be inside a 'use client' component.
- **Testing:** When creating new components or features, you are expected to provide corresponding tests. Unit tests go in /tests/unit, and E2E tests for user flows go in /tests/e2e.

By following these guidelines, you will be a highly effective member of our team. Let's start building.

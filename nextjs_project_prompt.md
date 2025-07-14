# 🚀 Next.js Project Specification and Configuration

Hi Claude! I need your help creating a complete, production-ready Next.js project. Please help me set up the following:

## 🛠️ Project Configuration

- Next.js 15
- React 19
- Use TypeScript v5
- Tailwind CSS v4
- shadcn/ui library (compatible with Tailwind CSS v4)
- ESLint v9
- Prettier
- Playwright
- Husky
- Lint-Staged
- Use App Router
- Use Turbopack for `next dev`

## 📁 Required Folder & File Structure

```
public/                   # Static assets
src/                      # Source code
├── app/                  # Application Routes (App Router implementation. File-system based routing, layouts, and pages)
│   ├── api/              # API Route Handlers
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
│   ├── cn.ts             # The cn utility for merging Tailwind CSS classes.
│   └── utils.ts          # Simple, pure, stateless helper functions (e.g., formatters, validators)
├── styles/               # Global & Theming Styles
│   └── globals.css       # Contains globals.css, theme variables, etc.
├── types/                # Global TypeScript Types
│   └── types.ts          # Shared type definitions and interfaces used across the application
├── tests/                # test code
│   ├── unit/             # Unit & Integration Tests (tests for individual components, functions, and hooks. Mirrors the /src structure)
│   └── e2e/              # End-to-End Tests (Playwright tests simulating full user journeys across the application)
```

## ⚙️ Configuration Files Needed

- `next.config.ts` (with strictMode and export configuration)
- `tailwind.config.ts` (with shadcn/ui setup)
- `package.json` (with all dependencies)
- `tsconfig.json`
- `.gitignore`
- `eslint.config.mjs`
- `postcss.config.js`
- `prettierrc`

## 📦 Core Dependencies

```json
{
  "dependencies": {
    "next": "15.3.5",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "@radix-ui/react-select": "latest",
    "@radix-ui/react-slider": "latest",
    "@radix-ui/react-switch": "latest",
    "class-variance-authority": "latest",
    "clsx": "latest",
    "tailwind-merge": "latest",
    "lucide-react": "^0.525.0"
  },
  "devDependencies": {
    "@eslint/eslintrc": "^3",
    "@eslint/js": "^9.31.0",
    "@tailwindcss/postcss": "^4.0.0-alpha.42",
    "@types/node": "24.0.13",
    "@types/react": "19.1.8",
    "@types/react-dom": "^19",
    "@typescript-eslint/eslint-plugin": "^8.36.0",
    "@typescript-eslint/parser": "^8.36.0",
    "autoprefixer": "^10.4.21",
    "eslint": "^9.31.0",
    "eslint-config-next": "^15.3.5",
    "eslint-config-prettier": "^10.1.5",
    "eslint-plugin-prettier": "^5.5.1",
    "eslint-plugin-react": "^7.37.5",
    "globals": "^16.3.0",
    "husky": "^9.1.7",
    "lint-staged": "^16.1.2",
    "postcss": "^8.5.6",
    "prettier": "^3.6.2",
    "prettier-plugin-tailwindcss": "^0.6.14",
    "tailwindcss": "^4.0.0-alpha.42",
    "typescript": "5.8.3",
    "typescript-eslint": "^8.36.0"
  }
}
```

## 🎨 Features to Include

- Responsive design
- Type-safe components
- Proper ESLint configuration
- Production-ready setup

## 🔧 Required Base Components

Please provide complete code for:

1. Basic Layout Component with:
   - Metadata configuration
   - Proper font setup (Inter)
   - Tailwind globals import

2. Utility Functions:
   - cn() utility for Tailwind class merging
   - Type-safe props handling
   - Common helper functions

3. shadcn/ui Components:
   - Basic Button
   - Card
   - Select
   - Switch

## 📝 Additional Requirements

- All components should be fully typed with TypeScript
- Include proper error boundaries
- Setup proper metadata in layout
- Include basic SEO configuration
- Ensure all components are accessible

## 🚦 Getting Started Instructions

Please provide the complete setup instructions including:

1. Installation commands
2. Development workflow
3. Building for production
4. Deployment considerations

I need this to be immediately runnable with minimal setup required. All code should be production-ready and follow best practices.

## Final Instructions

💡 After you acknowledge this request, I'll ask for the files one by one to ensure completeness.

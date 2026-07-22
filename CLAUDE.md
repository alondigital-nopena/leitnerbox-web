# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

**Leitner+ Web** is a Next.js web application implementing the Leitner spaced repetition system for flashcard learning. Built with Next.js 16 (App Router), TypeScript, React 19, and Supabase backend.

### What is the Leitner System?

The Leitner system is a spaced repetition learning method using multiple boxes (decks). Cards move forward when answered correctly, backward when incorrect. Cards in later boxes are reviewed less frequently, optimizing learning efficiency.

- **Box 1**: New cards, reviewed daily
- **Box 2**: Cards reviewed every 2 days
- **Box 3**: Cards reviewed every 4 days
- **Box 4**: Cards reviewed weekly
- **Box 5**: Cards reviewed monthly (mastered)

This web app brings the system to any device with a browser.

## Project Structure

```
.
├── app/                      # Next.js App Router
│   ├── layout.tsx            # Root layout
│   ├── page.tsx              # Home page
│   └── globals.css           # Global styles (Tailwind + DaisyUI)
├── components/               # React components
│   └── __tests__/            # Component tests
├── lib/                      # Utilities and client libraries
│   ├── supabase/             # Supabase client and types
│   │   ├── client.ts         # Browser client
│   │   ├── server.ts         # Server client
│   │   └── types.ts          # Database types
│   └── utils/                # Utility functions
├── e2e/                      # Playwright E2E tests
│   └── app.test.ts           # Example E2E test
├── public/                   # Static assets
├── jest.config.ts            # Jest configuration
├── jest.setup.ts             # Jest setup (Testing Library matchers)
├── playwright.config.ts      # Playwright configuration
├── postcss.config.mjs        # PostCSS config (Tailwind 4)
├── tsconfig.json             # TypeScript configuration
├── next.config.ts            # Next.js configuration
└── package.json              # Dependencies and scripts
```

## Architecture Patterns

### UI Components

**DaisyUI + Tailwind CSS 4**: Component-first styling approach

- Prefer DaisyUI components over custom Tailwind classes when component exists
- DaisyUI provides: buttons, cards, modals, forms, navigation, etc.
- Tailwind for custom spacing, layout, responsive design
- Theme configured via DaisyUI themes in globals.css

**Component conventions**:

- Place reusable components in `components/`
- Colocate tests in `__tests__/` folders
- Use Server Components by default (Next.js App Router)
- Add `"use client"` only when needed (interactivity, hooks, context)

### Data Layer

**Supabase**: Backend-as-a-Service for database, auth, storage

**Client setup** (lib/supabase/):

- `client.ts`: Browser client (use in Client Components)
- `server.ts`: Server client (use in Server Components, API routes)
- `types.ts`: Auto-generated database types from Supabase schema

**Database schema** (to be defined in Supabase):

Tables:
- `decks`: Flashcard deck metadata
- `cards`: Individual flashcards with box number and review dates
- `sessions`: Study session records
- `session_cards`: Cards reviewed in each session

**Type generation**:
```bash
npx supabase gen types typescript --project-id <project-id> > lib/supabase/types.ts
```

### Routing Structure

**Next.js App Router**: File-based routing with Server Components

- `app/layout.tsx`: Root layout (metadata, fonts, global providers)
- `app/page.tsx`: Homepage (default route)
- `app/decks/page.tsx`: Deck management (to be created)
- `app/study/[deckId]/page.tsx`: Study session (to be created)

**Route conventions**:
- `page.tsx`: Route segment UI
- `layout.tsx`: Shared layout for route segment
- `loading.tsx`: Loading UI (Suspense boundary)
- `error.tsx`: Error UI (Error boundary)
- `route.ts`: API route handler

**Navigation**: Use Next.js `<Link>` component and `useRouter()` hook

## Testing Strategy

### Unit Tests (Jest + React Testing Library)

**Location**: Colocated with source in `__tests__/` folders

**Setup files**:
- `jest.config.ts`: Jest configuration with next/jest preset
- `jest.setup.ts`: Global test setup (Testing Library matchers)

**Running tests**:
```bash
bun test              # Run all tests
bun test:watch        # Watch mode
bun test:coverage     # Coverage report
```

**Writing tests**:
- Test files: `*.test.ts` or `*.test.tsx`
- Use `@testing-library/react` for component tests
- Use `render`, `screen`, `fireEvent`, `waitFor` from RTL
- Add `data-testid` attributes for reliable querying
- Mock Supabase client in tests

**Test organization**:
- Unit test components in isolation
- Mock external dependencies (Supabase, API calls)
- Test user interactions, not implementation details
- Aim for high coverage on critical paths

### E2E Tests (Playwright)

**Location**: `e2e/` directory

**Setup**: `playwright.config.ts`

**Running E2E tests**:
```bash
bun test:e2e         # Run all E2E tests
bun test:e2e:ui      # Interactive UI mode
bun test:e2e:headed  # Run with browser visible
```

**Writing tests**: TypeScript files in `e2e/`
- Use Playwright API: `page.goto()`, `page.click()`, `expect(page).toHaveTitle()`
- Test critical user journeys: signup, creating deck, studying cards
- Run against local dev server (auto-started via webServer config)

**Current flows**:
- `app.test.ts`: Basic homepage load test

## Development Workflow

### Prerequisites

- Node.js 18+ (managed via nvm)
- Bun (package manager)
- Git

### Commands

```bash
bun install           # Install dependencies
bun dev               # Start Next.js dev server
bun build             # Build for production
bun start             # Start production server

bun test              # Run Jest tests
bun test:watch        # Jest watch mode
bun test:coverage     # Coverage report
bun test:e2e          # Run Playwright E2E tests

bun lint              # ESLint check
bun format            # Prettier format all files
```

### Git Workflow

**Husky hooks**:
- Pre-commit: Blocks direct commits to main/master, runs tsc --noEmit, runs lint-staged
- Pre-push: Blocks direct pushes to main/master

**lint-staged** (package.json):
- Formats `.ts`, `.tsx` files with Prettier
- Lints TypeScript files with ESLint
- Formats `.json` files with Prettier

**Branch workflow**:
- Create feature branches: `git checkout -b feature/your-feature-name`
- Commit to feature branch (hooks enforce this)
- Open PR to main for review

**Commit messages**: Follow conventional commits format (feat, fix, test, docs, refactor, chore)

### Code Style

**TypeScript**:
- Strict mode enabled
- No implicit any
- Explicit return types for exported functions
- Use type imports: `import type { Foo } from './types'`

**Import order**:
1. React/Next.js imports
2. Third-party libraries
3. Local imports (components, lib, utils)
4. Types
5. Styles

**Component conventions**:
- PascalCase for component files (Button.tsx, DeckCard.tsx)
- Default export for components
- Named exports for types/utilities
- Colocate tests in `__tests__/` folder

**Next.js specific**:
- Use Server Components by default (no "use client")
- Add "use client" only when needed (state, effects, event handlers)
- Use `<Link>` from `next/link`, not `<a>` tags
- Use `<Image>` from `next/image` for optimized images
- Fetch data in Server Components, not useEffect

**DaisyUI + Tailwind**:
- Prefer DaisyUI components over custom Tailwind classes
- Use semantic color names: `btn-primary`, `btn-secondary`, not `bg-blue-500`
- Responsive design: mobile-first with `sm:`, `md:`, `lg:` breakpoints
- Never hardcode colors, use DaisyUI theme tokens

## Vercel Deployment

**Platform**: Vercel (zero-config for Next.js)

**Environment variables** (set in Vercel dashboard):
- `NEXT_PUBLIC_SUPABASE_URL`: Supabase project URL
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`: Supabase anon key
- `SUPABASE_SERVICE_ROLE_KEY`: Supabase service role key (server-only)

**Deployment workflow**:
1. Push to main triggers production deploy
2. PRs get preview deployments
3. Vercel automatically builds and deploys
4. No build config needed (Next.js auto-detected)

**Performance**:
- Edge runtime for API routes (opt-in)
- Image optimization via Next.js Image
- Automatic code splitting
- Server-side rendering for SEO

## Dependencies

**Core**:
- `next`: 16.2.11 (App Router, React Server Components)
- `react`: 19.2.4
- `react-dom`: 19.2.4

**Backend**:
- `@supabase/supabase-js`: ^2.110.8 (Supabase client)

**UI**:
- `tailwindcss`: ^4 (utility-first CSS)
- `daisyui`: ^5.7.0 (Tailwind component library)
- `@tailwindcss/postcss`: ^4 (Tailwind 4 PostCSS plugin)

**Testing**:
- `jest`: ^30.4.2
- `@testing-library/react`: ^16.3.2
- `@testing-library/jest-dom`: ^7.0.0
- `@testing-library/user-event`: ^14.6.1
- `@playwright/test`: ^1.61.1
- `jest-environment-jsdom`: ^30.4.1

**Dev tools**:
- `typescript`: ^5
- `eslint`: ^9
- `eslint-config-next`: 16.2.11 (Next.js ESLint rules)
- `prettier`: ^3.9.6
- `husky`: ^9.1.7 (git hooks)
- `lint-staged`: ^17.1.1

## Common Pitfalls

### Server vs Client Components

❌ **Wrong**: Using useState in Server Component

```tsx
// app/page.tsx
export default function Home() {
  const [count, setCount] = useState(0); // Error: useState in Server Component
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

✅ **Correct**: Add "use client" directive

```tsx
// app/page.tsx
"use client";

import { useState } from "react";

export default function Home() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**Why**: Server Components can't use client-side features (state, effects, event handlers). Add "use client" for interactive components.

### Supabase Client Usage

❌ **Wrong**: Using browser client in Server Component

```tsx
// app/page.tsx
import { createClient } from "@/lib/supabase/client";

export default async function Page() {
  const supabase = createClient(); // Wrong client for server
  const { data } = await supabase.from("decks").select();
  return <div>{data}</div>;
}
```

✅ **Correct**: Use server client in Server Components

```tsx
// app/page.tsx
import { createClient } from "@/lib/supabase/server";

export default async function Page() {
  const supabase = await createClient();
  const { data } = await supabase.from("decks").select();
  return <div>{data}</div>;
}
```

**Why**: Server Components need server-compatible Supabase client. Browser client for Client Components, server client for Server Components.

### DaisyUI Components

❌ **Wrong**: Custom Tailwind classes when DaisyUI component exists

```tsx
<button className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
  Click me
</button>
```

✅ **Correct**: Use DaisyUI button component

```tsx
<button className="btn btn-primary">Click me</button>
```

**Why**: DaisyUI handles theming, hover states, focus styles, accessibility. Custom classes bypass DaisyUI theme system.

### Data Fetching

❌ **Wrong**: Using useEffect to fetch data in Client Component

```tsx
"use client";

import { useEffect, useState } from "react";

export default function Decks() {
  const [decks, setDecks] = useState([]);

  useEffect(() => {
    fetch("/api/decks")
      .then((res) => res.json())
      .then((data) => setDecks(data));
  }, []);

  return <div>{decks.map((deck) => ...)}</div>;
}
```

✅ **Correct**: Fetch in Server Component

```tsx
import { createClient } from "@/lib/supabase/server";

export default async function Decks() {
  const supabase = await createClient();
  const { data: decks } = await supabase.from("decks").select();

  return <div>{decks?.map((deck) => ...)}</div>;
}
```

**Why**: Server Components fetch data at build/request time, no loading state needed. Better SEO, faster initial load. Use Client Components only when interactivity required.

### Test IDs

❌ **Wrong**: Querying by text content

```tsx
const button = screen.getByText("Submit");
```

✅ **Correct**: Using data-testid

```tsx
// Component
<button data-testid="submit-button">Submit</button>;

// Test
const button = screen.getByTestId("submit-button");
```

**Why**: Text content changes, testids are stable. Supports i18n without breaking tests.

### Environment Variables

❌ **Wrong**: Using server-only env vars in Client Components

```tsx
"use client";

const serviceKey = process.env.SUPABASE_SERVICE_ROLE_KEY; // Exposed to browser!
```

✅ **Correct**: Use NEXT_PUBLIC_ prefix for browser, no prefix for server-only

```tsx
// Client Component
const url = process.env.NEXT_PUBLIC_SUPABASE_URL; // Safe

// Server Component / API route
const serviceKey = process.env.SUPABASE_SERVICE_ROLE_KEY; // Server-only, safe
```

**Why**: Only `NEXT_PUBLIC_*` vars are bundled for browser. Server-only vars (like service keys) stay on server.

## Future Architecture Considerations

As app functionality grows, document these patterns when implemented:

**State management**: For complex client state, consider Zustand or Jotai (avoid Redux for small apps)
**Real-time**: Use Supabase Realtime subscriptions for live updates
**Optimistic updates**: Implement optimistic UI for better UX during mutations
**Caching**: Use Next.js fetch caching and revalidation strategies
**Animations**: Document animation patterns when card flip/swipe animations implemented
**Accessibility**: Document a11y patterns (keyboard navigation, ARIA labels, semantic HTML)

## Supabase Setup

**Not yet configured** - Document when Supabase project created:

1. Create Supabase project at https://supabase.com
2. Run migrations to create database schema
3. Generate TypeScript types: `npx supabase gen types typescript --project-id <id> > lib/supabase/types.ts`
4. Set environment variables in Vercel
5. Configure Row Level Security (RLS) policies

**Database schema** (to be implemented):
- `decks` table
- `cards` table with foreign key to decks
- `sessions` table for study sessions
- `session_cards` join table

## UI Development

- Prefer DaisyUI components over custom Tailwind classes when component exists

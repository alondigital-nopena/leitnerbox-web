# Leitner+ Web

Web application implementing the Leitner spaced repetition system for flashcard learning. Built with Next.js 16, TypeScript, and React 19.

## Tech Stack

- Next.js 16 (App Router)
- React 19
- TypeScript
- Tailwind CSS 4
- DaisyUI 5
- Supabase (backend)
- Bun (package manager)

## Get started

1. Install dependencies

   ```bash
   bun install
   ```

2. Start the development server

   ```bash
   bun dev
   ```

   Open [http://localhost:3000](http://localhost:3000)

## Testing

### Unit Tests

This project uses Jest and React Testing Library for unit testing:

```bash
bun test              # Run tests
bun test:watch        # Watch mode
bun test:coverage     # Coverage report
```

### E2E Tests

This project uses [Playwright](https://playwright.dev/) for end-to-end testing:

```bash
bun test:e2e         # Run all E2E tests
bun test:e2e:ui      # Interactive UI mode
bun test:e2e:headed  # Run with browser visible
```

Test files are located in `e2e/`.

## Development Commands

```bash
bun dev              # Start dev server
bun build            # Build for production
bun start            # Start production server
bun lint             # Run ESLint
bun format           # Format all files with Prettier
```

## Deployment

Deploy to [Vercel](https://vercel.com/) with zero configuration:

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/alondigital-nopena/leitnerbox-web)

## Learn more

- [Next.js Documentation](https://nextjs.org/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [Playwright Documentation](https://playwright.dev/)

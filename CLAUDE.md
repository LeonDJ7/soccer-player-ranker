# Soccer Player Ranker

## Project Structure

```
frontend/
  src/
    app/              # Next.js App Router pages and layouts (page-related files only)
    components/       # Global, reusable components not tied to a specific feature
    hooks/            # Global hooks
    utils/            # Global utility functions (pure, domain-agnostic)
    types/            # Global TypeScript types/interfaces (core domain models)
    lib/              # Global lib (e.g. shadcn utils, Mantine theme)
    constants.ts      # Global constants
    features/         # Feature modules (most code lives here)
      <feature>/
        components/   # Components scoped to this feature
        hooks/        # Hooks scoped to this feature
        utils/        # Utils scoped to this feature
        types/        # Types scoped to this feature
        data/         # Mock data or static data scoped to this feature
        constants.ts  # Constants scoped to this feature
```

## Conventions

- **Feature-first**: Most code lives inside `src/features/<feature>/`. Only truly shared, reusable code belongs in the global folders.
- **Pages**: `src/app/` contains only Next.js routing files (page.tsx, layout.tsx, loading.tsx, etc.). Business logic and UI components go in `features/`.
- **Utils file naming**: One file per concept/algorithm (e.g. `utils/levenshtein.ts`, `utils/search.ts`). Only create a subfolder when a util grows to multiple related files.
- **Global vs feature utils**: Pure algorithms with no domain knowledge (e.g. `levenshteinDistance`) go in `src/utils/`. Domain-aware functions (e.g. `searchPlayers`) go in the relevant feature's `utils/`.
- **Path aliases**: Use these import aliases (configured in `tsconfig.json`):
  - `@/*` → `src/*`
  - `@/components/*` → `src/components/*`
  - `@/hooks/*` → `src/hooks/*`
  - `@/utils/*` → `src/utils/*`
  - `@/types/*` → `src/types/*`
  - `@/features/*` → `src/features/*`
  - `@/lib/*` → `src/lib/*`
  - `@/constants` → `src/constants`

## Next.js App Router: Server vs Client Components

- **Default to server components.** Pages (`page.tsx`, `layout.tsx`) should be server components unless they require interactivity.
- **Push `'use client'` as far down the tree as possible.** Feature components that need state or browser APIs get the directive; their parent pages do not.
- **Never add `'use client'` to a page just to use a hook.** Extract the interactive part into a feature component and import it.

## Component Decomposition

- **Same file sub-components**: If a component is only ever used by one parent, extract it as a named function in the same file (e.g. `PlayerResult`, `ClubResult` inside `PlayerSearch.tsx`). No need for a separate file.
- **Separate file**: Move to its own file when it's reused across multiple parents or grows large enough to warrant it.

## Styling & UI Libraries

- **Tailwind CSS v4** — use for layout, spacing, and structural styling (`flex`, `grid`, `px-4`, `rounded-xl`, etc.)
- **Mantine v8** — use for UI components and their variants/props (`Badge`, `TextInput`, `ActionIcon`, etc.) and theming. `MantineProvider` + `ColorSchemeScript` set up via `src/components/Providers.tsx` → `src/app/layout.tsx`. Theme config in `src/lib/theme.ts`.
- **shadcn/ui** — component primitives built on Radix; add components via `npx shadcn@latest add <component>` from `frontend/`
  - Config: `components.json` (aliases to `@/components/ui`)
- **Mixing Tailwind + Mantine**: Use Mantine's `className` prop to apply Tailwind utilities on Mantine components when needed. Prefer Mantine's own props (e.g. `size`, `variant`, `color`) over overriding with Tailwind where the prop exists.

## Data Fetching

- **TanStack Query (React Query)** — for all server state, caching, and async data fetching. `QueryClientProvider` is set up in `src/components/Providers.tsx`.
  - Use `useQuery` for reads, `useMutation` for writes.
  - Define query keys as constants in the relevant feature's `constants.ts`.

## Mobile-First Design

This app is primarily used on mobile. Follow these guidelines:
- Design mobile-first; use responsive breakpoints (`sm:`, `md:`) to enhance for larger screens.
- Minimum touch target size: **44×44px** for all interactive elements.
- Use `font-size: 16px` minimum on text inputs to prevent iOS auto-zoom on focus.
- Prefer sticky headers and bottom-anchored actions for thumb reachability.
- Keep layouts single-column on mobile; avoid horizontal scrolling.
- Test at 375px width (iPhone SE) as the baseline mobile viewport.

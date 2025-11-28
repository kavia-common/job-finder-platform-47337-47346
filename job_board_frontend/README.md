# Ocean Jobs — Astro Frontend

A simple, modern job board UI built with Astro. It lists jobs, supports client‑side search/filtering, and shows job details without requiring a backend. Styled using the Ocean Professional theme (blue primary, amber accents, subtle gradients).

Live dev server (container default): http://localhost:3000

## Quick start

- Install deps: `npm install`
- Start dev: `npm run dev`
- Build: `npm run build`
- Preview: `npm run preview`

This app uses no backend by default. It seeds the UI with mock jobs found in `src/lib/mockData.ts`.

## Project structure

```
src/
  components/
    Header.astro         # Header with brand and nav
    JobCard.astro        # Individual job card
    JobList.astro        # Grid with client-side filtering + details
    SearchBar.astro      # Inputs for query/location/type
    ThemeToggle.astro    # Light/Dark
  layouts/
    Layout.astro
  lib/
    types.ts             # Job type and PUBLIC_* env utility
    mockData.ts          # Mock job data and getter
  pages/
    index.astro          # Home page with search + list + details
  styles/
    theme.css            # Ocean Professional theme tokens and globals
public/
  assets/                # Place company logos or images here (optional)
```

## Ocean Professional theme

- Primary: `#2563EB` (blue)
- Secondary/Accent: `#F59E0B` (amber)
- Error: `#EF4444`
- Background: `#f9fafb`
- Surface: `#ffffff`
- Text: `#111827`
- Includes subtle gradients (`--gradient-soft`, `--gradient-accent`), rounded corners, and soft shadows.

Global tokens live in `src/styles/theme.css`.

## How search and state work

- The SearchBar emits `filter-change` events with `{ q, loc, type }`.
- JobList keeps local state and filters jobs on the client (no network).
- Clicking “Details” uses a hash (`#job-<id>`) to render details in a sticky panel.

No backend is required to use the app.

## Adjust mock data

Edit `src/lib/mockData.ts` to add/remove jobs or update job properties. Fields:
- id (string)
- title
- company
- location
- type: 'Full-time' | 'Part-time' | 'Contract' | 'Internship' | 'Remote'
- salaryRange (optional)
- postedAt (ISO string)
- description
- tags: string[]
- logoUrl (optional, place image under `public/assets/` and set e.g., `/assets/your-logo.png`)
- applyUrl (optional)

After changes, restart dev server or refresh the page if running with HMR.

## Future: Hooking to an API

This app safely reads public env variables via `getPublicEnv()` in `src/lib/types.ts`. For future integration:

- Set environment variable `PUBLIC_API_BASE` to your backend base URL. Examples:
  - `PUBLIC_API_BASE=https://api.example.com`
- Implement a fetch in `src/pages/index.astro` to replace the mock call:

```ts
// Example sketch (not active by default)
import { getPublicEnv } from '../lib/types';
const { PUBLIC_API_BASE } = getPublicEnv();

let jobs = await getJobsFromMock();
if (PUBLIC_API_BASE) {
  try {
    const res = await fetch(`${PUBLIC_API_BASE}/jobs`, { headers: { 'Accept': 'application/json' } });
    if (res.ok) {
      jobs = await res.json();
    }
  } catch (e) {
    console.warn('Falling back to mock jobs due to API error:', e);
  }
}
```

Guidelines:
- Do not break when `PUBLIC_API_BASE` is absent.
- Validate the shape to match `Job` type or map the fields.
- Consider pagination and server-side filtering later.

Other public envs available (read but not required): `PUBLIC_BACKEND_URL, PUBLIC_FRONTEND_URL, PUBLIC_WS_URL, PUBLIC_NODE_ENV, PUBLIC_ENABLE_SOURCE_MAPS, PUBLIC_PORT, PUBLIC_TRUST_PROXY, PUBLIC_LOG_LEVEL, PUBLIC_HEALTHCHECK_PATH, PUBLIC_FEATURE_FLAGS, PUBLIC_EXPERIMENTS_ENABLED`.

## Accessibility and semantics

- Buttons and links use clear labels.
- Details panel uses an aside with aria-label.
- Inputs have aria labels and keyboard submit.

## License

MIT

# VINPlus — Use Case 2: Policy Work Control

An interactive Policy Work Control dashboard for reviewing policy-work postings: KPI cards, trend and breakdown charts,
a review queue, and a manager review drawer. It is a React 19 + Vite rebuild of the original single-file HTML dashboard
(kept for reference in [`reference/`](reference)).

The app runs in **Demo Mode** by default, using built-in sample data and making no network requests. An optional
**Live Mode** reads the KPI cards, filter options and charts from Lumenore workflows.

## Requirements

- Node.js **22.12 or newer** (see `engines` in `package.json`)
- npm (bundled with Node)

## Install

```bash
git clone https://github.com/dhwajdhruvkar/vinplus-usecase2.git
cd vinplus-usecase2
npm install
```

## Run

```bash
npm run dev
```

Open the URL Vite prints (usually http://localhost:5173).

Sign in with the demo account shown on the login screen:

| Username | Password  |
|----------|-----------|
| `manager` | `demo123` |

This login is a front-end demo gate only. Do not enter real credentials.

## Scripts

| Command           | What it does                                          |
|-------------------|-------------------------------------------------------|
| `npm run dev`     | Start the development server                          |
| `npm run build`   | Create a production build in `dist/`                  |
| `npm run preview` | Serve the production build locally                    |
| `npm run test`    | Run the test suite (Node's built-in test runner)      |
| `npm run lint`    | Lint the source with oxlint                           |

## Using the dashboard

- **Filters** (top): date range, brand, department, journal and review status. They update the KPI cards and charts.
  Active filters appear as chips, each removable on its own, with **Clear all** to reset.
- **KPI cards**: net policy expense, gross debits, credits/offsets, posting entries, unique references and
  high-value review candidates, with month-over-month change.
- **Charts**: monthly trend, expense by department, expense by journal, and a department × month matrix.
  Click a chart element to filter the dashboard by it. Each widget has an info icon, CSV export and full-screen view.
- **Review queue**: sort, page and open any transaction in the review drawer to set a decision and leave a manager comment.
  Reviews and comments are saved in your browser's local storage only.
- **Transactions** (sidebar): the full transaction list with the same sorting, export and review actions.

## Demo Mode and Live Mode

Demo Mode is the default and needs no setup.

To try Live Mode, copy `.env.example` to `.env.local` (git-ignored) and set:

```bash
VITE_DATA_MODE=live
```

| Variable                     | Default | Purpose                                                                                 |
|------------------------------|---------|-----------------------------------------------------------------------------------------|
| `VITE_DATA_MODE`             | `demo`  | `demo` for built-in data, `live` to call the Lumenore workflows                         |
| `VITE_WORKFLOW_BASE_URL`     | unset   | Override the workflow endpoint. Leave unset in development to use the local proxy       |
| `VITE_WORKFLOW_CREDENTIALS`  | `omit`  | Fetch credentials policy: `omit` or `include`                                           |

Restart the dev server after changing `.env.local`.

In Live Mode:

- The KPI cards (values, month-over-month, KPI trend), the five filter dropdowns and the four charts are filled
  from the backend. Empty, loading and error states are shown instead of demo values.
- The remaining widgets (review flags, priority investigation, source journal breakdown and the review queue) still use demo data.
- During `npm run dev`, requests go through a **development-only** proxy (`/lumenore-workflow/`, configured in
  `vite.config.js`) to avoid browser CORS blocks. Production builds call the endpoint directly.
- Workflow IDs are registered in `src/api/workflowConfig.js`.

> **Status:** Live Mode is implemented and tested against backend-supplied sample responses (fixtures), but has
> **not been validated against the live API**. Live requests currently return HTTP 403 until the backend confirms how
> a front end should authenticate. Never put tokens, cookies or secrets in a `VITE_` variable: every `VITE_`
> variable is shipped to the browser.

## Testing

```bash
npm run test
```

The tests cover the workflow request format, the response adapters, filter handling, chart rendering, empty states
and negative values. They use the response samples in `tests/fixtures/` and make no network requests.

## Project structure

```
src/
  api/          workflow registry, request client and response adapters
  components/   dashboard UI (KPI cards, charts, filters, drawers, tables)
  hooks/        data hooks (workflow data, filter options, local storage)
  pages/        Policy Work Control and Transactions pages
  utils/        formatting, filtering, chart and export helpers
  data/         deterministic demo ledger
  styles/       dashboard stylesheet
tests/          test suite and backend response fixtures
reference/      original single-file HTML dashboard and stylesheet
```

## Deployment

The project is a standard Vite app: `npm run build` produces a static site in `dist/`. On Vercel, import this
repository with the Vite framework preset (build command `npm run build`, output directory `dist`).
Set environment variables only if you want Live Mode in that deployment.

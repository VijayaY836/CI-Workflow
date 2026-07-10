# CI Workflow

A sample React + Vite application wired up with a complete Continuous Integration (CI) pipeline using GitHub Actions. Every push and pull request is automatically installed, built, linted, format-checked, tested, and security-scanned.

The application itself is intentionally minimal — it exists to give the CI pipeline something real to install, build, lint, and test.

There are two workflows in `.github/workflows/`:

### 1. CI — `ci.yml`

Runs the core quality gate on every push and pull request:

| Step                 | Command                | Purpose                                              |
| -------------------- | ---------------------- | ---------------------------------------------------- |
| Install dependencies | `npm ci`               | Clean, reproducible install from `package-lock.json` |
| Lint                 | `npm run lint`         | Static analysis with ESLint                          |
| Format check         | `npm run format:check` | Verifies code matches Prettier style                 |
| Test                 | `npm test -- --run`    | Runs the Vitest unit test suite                      |
| Build                | `npm run build`        | Produces a production build with Vite                |
| Upload artifact      | —                      | Saves the `dist/` build output for 5 days            |

### 2. Security Scan — `security.yml`

Runs on every push, every pull request, and on a weekly schedule (Mondays 06:00 UTC):

| Job                           | Tool                           | Purpose                                    |
| ----------------------------- | ------------------------------ | ------------------------------------------ |
| Dependency Vulnerability Scan | `npm audit --audit-level=high` | Flags known-vulnerable dependencies        |
| CodeQL Analysis               | GitHub CodeQL                  | Static application security testing (SAST) |
| Secret Leak Scan              | Gitleaks                       | Detects committed secrets/credentials      |

### Dependency analysis — `dependabot.yml`

Dependabot opens weekly pull requests to keep both npm packages and GitHub Actions up to date.

## Triggers

Both workflows trigger on:

- `push` to `main`, `master`, or `develop`
- `pull_request` targeting `main`, `master`, or `develop`

The security workflow additionally runs on a weekly cron schedule so vulnerabilities are caught even when no code changes.

## Project structure

```
.
├── .github/
│   ├── workflows/
│   │   ├── ci.yml          # Build, lint, format, test
│   │   └── security.yml    # npm audit, CodeQL, Gitleaks
│   └── dependabot.yml      # Automated dependency updates
├── src/
│   ├── App.jsx             # Sample component
│   ├── App.test.jsx        # Vitest unit test
│   ├── main.jsx
│   └── setupTests.js
├── eslint.config.js        # ESLint (flat config)
├── .prettierrc.json        # Prettier config
├── vite.config.js          # Vite + Vitest config
└── package.json
```

## Setup instructions

### Prerequisites

- Node.js 20.x or later
- npm (bundled with Node.js)

### Run locally

```bash
# 1. Install dependencies
npm install

# 2. Start the dev server
npm run dev

# 3. Run the quality checks (the same ones CI runs)
npm run lint          # ESLint
npm run format:check  # Prettier (use `npm run format` to auto-fix)
npm test -- --run     # Vitest
npm run build         # Production build
```

### Enable CI on your own repository

1. Push this repository to GitHub.
2. Open the **Actions** tab — the CI and Security Scan workflows run automatically on the next push or pull request.

No secrets or extra configuration are required. `GITHUB_TOKEN` used by Gitleaks and CodeQL is provided automatically by GitHub Actions.

## Available npm scripts

| Script                 | Description                                               |
| ---------------------- | --------------------------------------------------------- |
| `npm run dev`          | Start the Vite dev server                                 |
| `npm run build`        | Build for production                                      |
| `npm run preview`      | Preview the production build                              |
| `npm run lint`         | Run ESLint                                                |
| `npm run format`       | Auto-format with Prettier                                 |
| `npm run format:check` | Check formatting without writing changes                  |
| `npm test`             | Run Vitest (watch mode; add `-- --run` for a single pass) |

## Tech stack

- **React 18 + Vite 6** — application and build tooling
- **Vitest + Testing Library** — unit testing
- **ESLint 9 (flat config)** — linting
- **Prettier 3** — formatting
- **GitHub Actions** — CI/CD, CodeQL, Gitleaks, Dependabot

# Facilities Management

Facilities maintenance, scheduling, and reporting for restaurant operations. This repository currently ships a static interactive demo. See the [repository audit](docs/repository-audit.md) for the demo inventory, missing Supabase source, and development priorities.

## Run the demo locally

From the repository root, run:

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

Open <http://127.0.0.1:8000/> in a browser. No build step or environment configuration is needed. The application is in `index.html`, with inline JavaScript, CSS, and embedded views. The PDF export loads jsPDF from a CDN. Use fictitious data and a disposable browser profile. Some demo data persists in browser storage.

## Test changes

- Start the local server and exercise the affected views in the browser. For a smoke test, switch roles, create and complete a sample request, reopen it, and review the schedule and Daily Recap. Exercise PM progress and reload behavior with synthetic photos.
- Run `git diff --check` before opening a pull request. The repository has no automated test suite yet.
- Once the schema and Supabase-backed frontend are recovered, run database tests and browser integration tests against an isolated local stack. The [Supabase runbook](docs/supabase-development.md) describes the prerequisites and commands.

## Deploy

The demo can be published as static files. If GitHub Pages is in use, verify the configured source and publishing behavior in repository settings. The root `.nojekyll` file supports serving the static files as-is. Keep previews on a separate origin and use synthetic data. Confirm the production frontend source, destination, and release process before publishing a production change.

Open development work against `main` as a pull request. Production deployments, database changes, and merging to `main` require human approval. The [Supabase runbook](docs/supabase-development.md) covers local development and optional staging access.

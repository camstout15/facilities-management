# Repository audit

Reviewed `main` at `ceb57376aa3d6246f2a2bf3970515761cd060524`. GitHub listed one branch at review time. All 15 commit trees reachable from that head were inspected. The initial commit contained only `README.md`. The remaining trees contained exactly `index.html`, `README.md`, and `.nojekyll`.

## Source inventory

| Area | Evidence in the audited revision |
| --- | --- |
| Entry point | Root `index.html` contains all demo HTML, CSS, and JavaScript. Five embedded views cover maintenance, PM, scheduling, settings, and accounts. The outer shell handles navigation, demo role selection, and Daily Recap. |
| Maintenance | Sample tasks and browser-side controls for requests, priority, waiting for parts, completion, reopening, flagging, and history. Selected media uses browser object URLs or data URLs. The maintenance task array is in memory. |
| Scheduling and PM | Sample technician assignments, role-dependent schedule views and controls, and browser-local schedule state. PM has templates, checklists, evidence capture, progress saved to browser storage, and a way to create a linked demo request. |
| Reporting and accounts | The Daily Recap builds from demo data on demand. The source includes settings, notification preferences, account-management mockups, and a PDF export path that loads jsPDF from a CDN. |
| Authentication and storage | The role selector drives demo visibility. The revision contains no Supabase client, authentication integration, signed URL handling, or application calls to a backend. There is no service worker or web app manifest. |
| Duplicate or obsolete files | No separately tracked duplicate prototypes, test files, or obsolete test artifacts were found. Several demo views are embedded in the same file. |

The handoff reports email/password login, Administrator and Restaurant Manager profiles, maintenance request creation with mandatory photo, private uploads and signed reads, and limited isolation checks for two restaurants. Those results refer to an implementation whose source is absent from the reviewed history. The reported isolation checks cover reads for the tested account and paths. Authorization on writes, other roles, and the full production configuration remain unverified.

## Missing source and migration status

| Item | Finding |
| --- | --- |
| Migration 001 | Absent from the reviewed branch and its reachable history. Filename, contents, and applied state are unverified. |
| Migration 002 | Absent from the reviewed branch and its reachable history. Filename, contents, and applied state are unverified. |
| `003_task_lifecycle.sql` | Absent from the reviewed branch and its reachable history. The handoff reports successful execution in Supabase. The SQL, database migration history, deployed function definitions, grants, and behavior were unavailable for inspection. |
| Supabase-backed frontend | Retrieve the exact source used for the reported sign-in, creation, upload, and cross-restaurant read tests. Identify its deployment origin and commit before wiring a backend into the demo. |
| Reproducible database setup | Recover the original SQL and an approved schema-only snapshot or catalog export. Include `app` and `public` definitions, function owners and grants, RLS policies, Storage bucket privacy and policies, and migration history. Include synthetic seed fixtures after the baseline is verified. |
| Hosted services | No tracked digest job, CI workflow, package manifest, or deployment configuration was found. The current hosting settings were not verified. |

## Security and lifecycle review

The demo role selector and front-end checks are presentation controls. The page exposes embedded sample data and browser state to the visitor. Keep real restaurant requests and photos out of the demo, browser-local storage, fixtures, and this public repository. Several render paths build HTML strings. Review each path that accepts user content before connecting production data, and favor text nodes for untrusted text.

An implementation-specific SQL review is blocked until the migration or deployed definitions are available. The following review targets are requirements to verify against the recovered definitions.

| Area | Verification required |
| --- | --- |
| Authorization | Inspect function signatures, security mode, owner, effective search path, and grants to PUBLIC, anonymous, and authenticated roles. For definer functions, verify trusted schema resolution and explicit restaurant, role, and task-assignment checks as applicable. Derive actors from authenticated identity. Check direct table permissions and RLS for every write path, constraints across task, assignment and attachment records, and Storage policies for object ownership and restaurant scope. |
| Task transitions | Verify allowed prior states, completion and reopening permissions, completed-task immutability, required nonblank waiting note, retention of all assignees, and nondeletion. Check that callers cannot bypass transitions with direct writes. |
| Concurrency and rollback | Verify state checks serialize safely under concurrent changes. Task, assignment, and audit changes should commit atomically. Test competing completion and assignment, a failed audit insert, and retries that could leave duplicate events. Uploads and database changes span separate APIs. Verify that failures do not leave a request with missing required evidence or unmanaged uploads. |
| History | Verify append-only events with authenticated actor, database timestamp, task identifier, prior and new values, and required notes. Reopening must preserve prior completion evidence and timestamps in history. Check that priority changes and assignment edits record actual changes, and verify schedule, flag, and PM audit rules when those paths exist. |

GitHub returned `protected: false` for `main` and an empty repository ruleset list at inspection time. Confirm the intended required reviews and status checks before enabling automated production changes.

## Proposed structure

Keep the root static entry point and embedded demo intact in this PR. After source recovery, prefer the recovered app's existing structure. If it needs organization, keep `index.html` as the entry point and extract shared styles and JavaScript into `assets/css/` and `assets/js/` in incremental changes. Use `supabase/config.toml`, `supabase/migrations/`, and `supabase/tests/` for the local stack, verified migrations, and database tests. Use `tests/e2e/` for browser tests and `docs/` for developer and deployment instructions. Add deployment workflows after the production source and approval path are confirmed.

## Automated test plan

1. Use pgTAP with a clean local migration replay and synthetic principals across two restaurants. Test authorized and unauthorized reads, direct writes, RPCs, and audit access across the documented roles. Assert exact resulting task, assignment, and event rows.
2. Add integration tests using Supabase Auth, PostgREST, and Storage for required photo uploads, attachment consistency, signed URLs, and anonymous and cross-restaurant denial. Keep any privileged setup identity inside isolated test setup.
3. Add lifecycle integration tests for shared assignment, completion, reopening, waiting notes, rollback, and concurrent updates. Add scheduling, flag, PM, and digest cases as the corresponding backend paths become available, including restaurant-local 6 PM and daylight saving transitions.
4. Run Playwright against a locally served frontend and disposable Supabase stack for request creation, role visibility, photo reads, and lifecycle paths. Gate pull requests on clean migration replay and the database and browser suites once the baseline is available.

## Verification performed

GitHub tree and source inspection, a Git blob hash match for the reviewed HTML, and Node syntax checks on five inline scripts. The Supabase CLI was unavailable in the workspace and Docker socket access was denied. Browser workflows, production database definitions, migration replay, and unauthorized database operations were not tested.

# Supabase development

## Local setup

Use a current Supabase CLI release and Docker with a running, accessible daemon. Confirm `supabase --version` and `docker info`. Use the same CLI version in development and CI once the baseline is recovered. The CLI needs network access to fetch the local stack images on its first run.

Recover and review migrations 001 through 003, including the restaurant access helper, maintenance request RPC, grants and RLS, and bucket and object policies. Reconcile migration filenames and versions with the database's recorded history. A script run in the Supabase SQL editor may have changed the schema without recording a CLI migration version. Preserve the originals during reconciliation.

After the baseline has been recovered, run the following in a development checkout. Run `supabase init` once if the recovered source does not include `supabase/config.toml`. Put the verified migrations in `supabase/migrations/` before starting the stack. Configure any seed to use synthetic records.

```sh
supabase start
supabase status
supabase db reset --local
supabase test db --local
```

The reset rebuilds the disposable local database. Run the test command after adding pgTAP tests under `supabase/tests/`. Local development needs no hosted Supabase login or project link. When the recovered frontend has a configuration loader, use the API URL and publishable key or legacy anon key reported by `supabase status`. Review the recovered loader before serving it. The current demo ignores `.env.example`. Keep service-role keys, database passwords, and CLI access tokens out of browser code and Git.

## Optional hosted staging connection

Use a separate staging project with synthetic data. To connect, obtain the staging project reference, a Supabase account authorized for that project, a personal access token if using noninteractive login, and the staging database password if prompted. The CLI uses the account or token for Supabase management access and the database credential for direct database access. A frontend publishable key is for application API calls. Confirm network access to the Supabase management API and the staging database endpoint or the Supabase pooler.

In a dedicated staging checkout with a verified configuration, inspect the organization and project in the dashboard. Verify the full staging reference against the dashboard and confirm it differs from the production reference in the handoff before linking. Set `STAGING_PROJECT_REF` to that verified reference in your shell.

```sh
supabase login
supabase projects list
supabase link --project-ref "${STAGING_PROJECT_REF:?Set the verified staging project reference}"
```

In CI, inject `SUPABASE_ACCESS_TOKEN` and, where required, `SUPABASE_DB_PASSWORD` from the protected secret store. Keep stage and production credentials separate, and keep generated CLI state out of Git. Avoid pasting secret values into shell commands, PRs, logs, or chat.

After confirming the linked staging reference, `supabase migration list --linked` compares local versions with staging history. A preview using `supabase db push --linked --dry-run` shows the migrations a subsequent push would apply. Run these only against the verified staging project. Keep ordinary pull-request tests local.

## Production schema recovery

Have an authorized database owner provide the original migrations and an approved read-only, schema-only export with the relevant definitions and version history. Exclude data rows, credentials, object contents, and signed URLs. Include function definitions and grants, RLS, Storage policies and bucket privacy, and any schedule configuration relevant to the app. Review the export for sensitive material before committing anything to this public repository. Compare the export and originals locally and document drift. A new baseline, migration-history repair, or any command that can change production requires explicit approval before execution. Keep production deployments and migration jobs behind a human approval gate.

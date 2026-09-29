# VIREN VRN — Staging Completion Runbook

1. Create a dedicated Supabase staging project.
2. Run `sql/schema.sql` once against that project.
3. Create a server-only `SUPABASE_SERVICE_ROLE_KEY`; never expose it as `NEXT_PUBLIC_*`.
4. Set `NEXT_PUBLIC_SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `SESSION_SECRET` (32+ random characters), and `NEXT_PUBLIC_SITE_URL`.
5. Install Node 22 dependencies with `npm ci` when a lockfile exists, otherwise `npm install` and commit the generated lockfile.
6. Run `npm run contract`.
7. Run `npm run verify`.
8. Test the acceptance workflows in `DEPLOYMENT.md` against staging.
9. Test backup and restore using a staging snapshot.
10. Only after all staging checks pass, create production Supabase and repeat the schema/deployment process.
11. Configure the production host with the same server-only environment variables.
12. Enable HTTPS and the production domain.
13. Run smoke tests after deployment.
14. Record the deployment version and database migration state.
15. Declare `VIREN FINAL / LOCKED` only after the acceptance checklist passes.

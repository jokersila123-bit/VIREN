
## 0. Preflight before touching staging/production
Run:
```bash
npm install
npm run verify
node scripts/preflight.mjs
```
`preflight.mjs` checks required environment variables, required schema markers, production-only secret configuration, and that the application is not accidentally configured to use a placeholder URL.

Never run a production migration until the preflight and verification steps pass.

# VIREN VRN — Production Deployment Runbook

## 1. Infrastructure
- PostgreSQL/Supabase project: production project, separate from staging.
- Node.js: 22+.
- Host: Vercel, Docker host, or another Node-compatible production host.
- HTTPS: required.
- Storage: private `viren-media` bucket.

## 2. Secrets
Set these only in the hosting provider's server-side environment:
- `NEXT_PUBLIC_SUPABASE_URL`
- `SUPABASE_SERVICE_ROLE_KEY`
- `SESSION_SECRET`
- `NEXT_PUBLIC_SITE_URL`
- optional `RECOVERY_WEBHOOK_URL`

Never expose or commit the service-role key or session secret.

## 3. Database
1. Create a staging Supabase project.
2. Run `sql/schema.sql` on staging.
3. Seed only approved VIREN content.
4. Execute acceptance tests.
5. Verify backup and restore.
6. Apply the same migration to production during a controlled deployment window.

## 4. Build
```bash
npm install
npm run verify
```
The build must pass before production deployment.

## 5. Docker
```bash
docker build -t viren-vrn .
docker run --env-file .env.production -p 3000:3000 viren-vrn
```
The container expects Next.js standalone output.

## 6. Post-deployment checks
- `GET /api/health` returns healthy database status.
- Login/logout works.
- Password reset works through the configured recovery webhook.
- Suspended/terminated accounts are denied.
- Officer cannot mutate another server.
- Application approval creates exactly one account and the selected memberships.
- Attendance is server-scoped.
- Event capacity is concurrency-safe.
- Economy actions are atomic and idempotent.
- Store claims cannot overdraw balance.
- Media remains private unless explicitly authorized.
- Audit logs are generated for privileged operations.

## 7. Backup / recovery
- Enable scheduled database backups.
- Keep a separate recovery copy according to the hosting provider's retention policy.
- Test a restore in staging before any destructive production migration.
- Never treat a destructive UI action as a backup mechanism.

## 8. Production lock
After successful staging and production acceptance tests, tag the deployed commit. Future changes must be component-scoped, reviewed, tested, and regression-tested before release.

# VIREN — Final Deployment Gate

## Architecture
Cloudflare Workers + OpenNext + Supabase.

Cloudflare's current documentation supports OpenNext for maintaining an existing Next.js application; vinext is the recommended path for new applications. This release deliberately retains OpenNext to avoid a framework migration during the finalization of the existing VIREN codebase.

## Required GitHub environment secrets
Create two GitHub environments: `staging` and `production`.

Each environment needs:
- `NEXT_PUBLIC_SUPABASE_URL`
- `SUPABASE_SERVICE_ROLE_KEY`
- `SESSION_SECRET`
- `NEXT_PUBLIC_SITE_URL`
- `RECOVERY_WEBHOOK_URL` (only if enabled)
- `CLOUDFLARE_API_TOKEN`
- `CLOUDFLARE_ACCOUNT_ID`

Never commit these values.

## Staging gate
1. Create a dedicated Supabase staging project.
2. Apply `sql/schema.sql` to staging.
3. Configure the staging environment secrets.
4. Run the GitHub workflow manually with `staging`, or push to `main` to use the default staging deployment.
5. Require `npm run verify` and `npm run build:cloudflare` to pass.
6. Preview/test the deployed Worker before production.

## Production gate
Production must use a separate Supabase project and separate Cloudflare environment secrets.

Do not promote staging credentials into production.

Before selecting `production` in the workflow, verify:
- authentication and session persistence
- logout and session revocation
- Member/Officer/Moderator/Dev authorization boundaries
- recruitment acceptance and withdrawal
- multi-server memberships
- independent server lineup roles
- roster and current lineup
- attendance verification and duplicate prevention
- events/registration/cancellation
- announcements/notifications
- economy transactions and idempotency
- reports
- media/file permissions
- audit logs
- mobile navigation and responsive layouts
- maintenance mode
- account lifecycle states

## Rollback
Cloudflare Workers creates versions for Worker code/configuration. Keep the previous known-good production deployment available for rollback.

## Final lock
Only after the production smoke/E2E suite passes should the project be marked `VIREN FINAL / LOCKED`.

# VIREN VRN Verification Status

## Source-level checks completed
- TypeScript/TSX syntax parse across `app/` and `lib/`: PASS
- ZIP integrity: PASS
- Database membership uniqueness: PASS
- Event registration uniqueness: PASS
- Coin idempotency uniqueness: PASS
- Security-definer functions use an explicit `search_path`: PASS
- Economy atomic function has declared duplicate-result state: PASS
- Server membership role is nullable so membership and lineup role remain separate: PASS
- Officer application scope is enforced in the database approval function: PASS
- Staff global-role changes are Dev-only: PASS

## Environment-limited checks
The execution environment repeatedly timed out while downloading npm dependencies. Therefore a real `next build` cannot be truthfully reported as passed here.

## Required staging verification
1. Create Supabase staging project.
2. Run `sql/schema.sql`.
3. Configure `.env.local` from `.env.example`.
4. Run `npm install`.
5. Run `npm run verify`.
6. Exercise authentication, recruitment, membership, lineup, attendance, events, announcements, notifications, economy, store, reports, staff permissions and account lifecycle.
7. Verify database backup/restore.
8. Deploy staging and perform mobile/desktop QA.
9. Promote the verified build to production.

## V7 Verification Boundary

The application intentionally keeps Supabase RLS enabled on all production tables. The application API uses the server-only service-role credential after performing its own authentication/authorization checks. Do not expose `SUPABASE_SERVICE_ROLE_KEY` to the browser and do not add permissive `anon` policies as a shortcut. If direct browser Supabase access is introduced later, it must receive explicit least-privilege RLS policies first.

Run `npm run contract` before deployment. Run `npm run verify` only after dependencies are installed and a real staging environment is configured.

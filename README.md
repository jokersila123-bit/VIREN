# VIREN VRN — Production Full-Stack Website

This is the new VIREN VRN implementation. It is designed as a real Next.js + Supabase application, not a static prototype.

## Stack
- Next.js / React / TypeScript
- Supabase PostgreSQL
- Server-side Supabase service-role access
- Database RLS with server-side authorization
- Node scrypt password hashing
- Server-side revocable sessions
- Asia/Manila display timezone

## Required production setup
1. Create a Supabase project.
2. Run `sql/schema.sql` in the Supabase SQL editor.
3. Create a private storage bucket for media and configure your storage policies/adapter as required by your deployment.
4. Copy `.env.example` to `.env.local` and provide real values.
5. Generate a long random `SESSION_SECRET`.
6. Install dependencies with `npm install`.
7. Run `npm run build` and fix any environment-specific configuration warnings before deployment.
8. Deploy the Next.js application to Vercel, Cloudflare, or another Node-compatible host.
9. Configure the same environment variables in the production host.
10. Configure your production domain and HTTPS.

## Security requirements
- Never commit `.env.local` or service-role keys.
- Never expose `SUPABASE_SERVICE_ROLE_KEY` to browser code.
- Use a separate staging Supabase project for testing migrations.
- Take a database backup before destructive administration.
- Test recovery before declaring production locked.

## Core invariants
- One website account can have many server memberships.
- One account + one server has at most one membership.
- Server roles belong to memberships, not global accounts.
- Event registration is unique per account/event.
- Attendance is unique per account/server/date/event.
- Economy operations are transactional and idempotent.
- Suspended/terminated accounts cannot authenticate.
- Officer operations are scoped to assigned servers.
- Moderator/Dev restrictions are enforced server-side.

## Production readiness
The codebase contains the core public site, recruitment, authentication/recovery, profile, roster, attendance, events, announcements, notifications, reports, economy/store, staff controls, permissions, media foundation, audit logging, account lifecycle, and database transaction/constraint layer.

Before public launch, connect real Supabase storage, populate approved VIREN artwork/content, run the complete migration on staging, execute the end-to-end acceptance tests in the project specification, verify backups/restoration, and deploy through your hosting provider.

## Acceptance tests before production lock
- Applicant selects all 3 servers -> one account + exactly 3 memberships.
- Changing Revibe role never changes NextGen or Revival role.
- Removing one membership leaves the account and other memberships intact.
- Officer cannot mutate another server.
- Member cannot reach staff endpoints.
- Suspended/terminated account cannot create a session.
- Password reset revokes prior sessions.
- Duplicate event registration is rejected atomically.
- Event capacity cannot be exceeded through concurrent requests.
- Duplicate coin idempotency keys do not double-credit/debit.
- Store claims cannot overdraw balance or bypass cooldowns.
- Attendance is server-scoped and duplicate protected.
- Destructive account termination archives active memberships and preserves history.
- Media type/size validation rejects unsupported uploads.
- Audit records exist for sensitive administrative changes.

## Expanded production systems in this build
The current source also includes profile API correction, internal notification read actions, global search, Work/Daily/Rob/Coin Flip/Dice/Slots economy actions with cooldown/idempotency protection, coin transfers, event withdrawal, event CRUD, server-scoped Officer attendance/announcement controls, signed private media access, and a production deployment runbook.

A successful public deployment still requires a real Supabase project, approved VIREN artwork/media, a production host, environment secrets, backup verification, and an executed `npm run build` in the deployment environment.

## Authoritative permission matrix

| System | Member | Officer | Moderator | Dev |
|---|---|---|---|---|
| Own profile | Edit permitted fields | Edit permitted fields | Edit permitted fields | Edit permitted fields |
| Own attendance | Create/View | Create/View | Manage | Manage |
| Assigned server | View | Manage | Manage | Manage |
| Other server | View public data only | Denied | Authorized | Manage |
| Applications | Apply/View own | Scoped review where granted | Manage | Manage |
| Memberships | None | Assigned server | Authorized | All |
| Lineup | None | Assigned server | Authorized | All |
| Accounts | Own | Limited | Manage | All |
| Economy | Use | Limited | Limited | Manage |
| Website settings | None | None | None | Manage |
| Audit logs | None | Scoped/authorized | Authorized | All |
| Dev Control | Denied | Denied | Denied | Manage |

The server must enforce this matrix. Frontend visibility is convenience only and is never a security boundary.

## Account lifecycle

`PENDING → ACTIVE → SUSPENDED → ACTIVE` is reversible. `ACTIVE/SUSPENDED → TERMINATED` revokes sessions and archives active memberships while preserving required history. `ARCHIVED` is a non-active historical state. `DELETED` is reserved for controlled data-retention workflows and must not be used as a substitute for termination when audit/history must remain.

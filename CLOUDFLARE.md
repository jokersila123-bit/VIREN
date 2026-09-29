# VIREN VRN — Cloudflare Workers Deployment

VIREN is configured for Cloudflare Workers using `@opennextjs/cloudflare`.

## Build

```bash
npm install
npm run verify
npm run build:cloudflare
```

The Cloudflare adapter runs the normal `next build` and then produces `.open-next/` for the Workers runtime.

## Local Workers preview

Create local secrets from `.dev.vars.example` as `.dev.vars`, then:

```bash
npm run preview
```

## Cloudflare Workers Builds

Recommended CI configuration:

- Build command: `npx @opennextjs/cloudflare build`
- Deploy command: `npx @opennextjs/cloudflare deploy`
- Preview command: `npx @opennextjs/cloudflare preview`

Set all required environment variables in Cloudflare Workers **Build variables and secrets**. Runtime secrets must also be configured in the Worker environment. Never commit `.dev.vars` or secret values.

Required values:

- `NEXT_PUBLIC_SUPABASE_URL`
- `SUPABASE_SERVICE_ROLE_KEY`
- `SESSION_SECRET`
- `NEXT_PUBLIC_SITE_URL`
- `RECOVERY_WEBHOOK_URL` (if recovery webhook is enabled)

## Supabase

Use a separate Supabase project for staging and production. Apply `sql/schema.sql` to staging first. Do not point production at staging credentials.

## Production

Deploy only after:

1. `npm run verify` passes.
2. `npm run build:cloudflare` passes.
3. Worker preview/E2E tests pass.
4. Supabase backup/restore procedure is verified.
5. Production environment variables are configured.
6. Health, authentication, permissions, membership, attendance, events, economy, media, notifications, reports, and audit tests pass.

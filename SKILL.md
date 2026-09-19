---
name: cloudflare-deploy-website
description: Deploy an existing static, SPA, SSR, or full-stack website to Cloudflare Workers and obtain a verified workers.dev URL. Use for a site's first Cloudflare deployment; domain onboarding and custom-domain cutover belong in cloudflare-connect-domain.
---

# Cloudflare Deploy Website

Deploy the user's existing project with the smallest Cloudflare-specific change its architecture needs. Treat the repository, lockfile, framework, and current deployment scripts as the source of truth.

## Start with evidence

1. Inspect the working tree and do not overwrite unrelated or uncommitted work.
2. Identify the package manager, build command, output directory, framework, runtime requirements, current hosting adapter, existing Wrangler configuration, and pinned Wrangler version.
3. Determine whether the site is static, an SPA, SSR/full-stack, or already deployed with Pages. Use Workers Static Assets for a new static or SPA deployment and the framework's current Workers adapter for SSR/full-stack. Preserve an existing Pages deployment unless the user requests migration.
4. Read [references/deployment-workflow.md](references/deployment-workflow.md) before changing configuration or deploying.

## Establish the target

Confirm or infer only values that can be derived safely from the project: Worker name, Cloudflare account, production environment, build output, and required bindings. Ask for a choice only when it changes ownership, cost, or production behavior.

Deploy to `*.workers.dev` first. A custom domain is not a prerequisite and is outside this skill's main workflow. Do not add D1, KV, R2, Queues, or other products merely because Cloudflare offers them; create only resources the application actually uses.

## Authenticate and protect secrets

- Run the project-local Wrangler `whoami` before remote changes and verify the intended account.
- If login, account selection, payment confirmation, or another browser-only step is required, give the user one precise action and resume from the failed checkpoint afterward.
- Keep secret values out of source, command arguments, chat output, and logs. Use Wrangler's interactive or documented protected-input mechanism. Ignore local secret files in Git.
- Treat database migrations, secret changes, resource creation, and deployment as production mutations. Resolve the exact account, environment, and resource first.

## Configure and deploy

- Use the project's pinned Wrangler and package-manager commands. Do not silently upgrade dependencies.
- Prefer `wrangler.jsonc` for a new configuration. For a new Worker, set the compatibility date to the current date; do not advance an existing date incidentally.
- Edit the source configuration, not framework-generated output. For build systems that generate a flattened Wrangler file, build first and deploy the generated config exactly as the project expects.
- Keep public configuration in Wrangler vars and secrets in the secret store. Bind existing resources by verified identifiers; avoid accidental automatic provisioning.
- Run the repository's relevant checks and production build. Use `wrangler deploy --dry-run` when supported, understanding that it does not validate remote resources.
- Create required remote resources, apply remote migrations only to the confirmed database, set required secrets, and then run the project's deployment command. Prefer an existing orchestrated command over reconstructing its individual steps.

Stop after the same failure repeats or when the next step requires new authorization, a paid product, destructive data work, or a missing user-owned credential. Report the exact checkpoint instead of retrying blindly.

## Verify the live result

Do not equate a successful upload with a working site. Verify the returned production URL over HTTPS, representative routes, static assets, SPA fallback or SSR behavior, API/health endpoints when present, and every storage or database path affected by the deployment. Inspect logs for runtime failures when the UI result is ambiguous.

Finish with the Worker name, account/environment, live `workers.dev` URL, resources and secrets configured (names only), migrations applied, checks performed, and any manual or unverified items. If the user later has a domain, route that task to `cloudflare-connect-domain`.

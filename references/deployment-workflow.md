# First deployment workflow

Use this reference for the initial Cloudflare deployment. Re-check current Cloudflare documentation and the project-local Wrangler help before relying on exact fields or flags.

## Choose the deployment shape

| Project evidence | Preferred path |
| --- | --- |
| Static files with no server runtime | Workers Static Assets pointed at the real build directory |
| Client-side SPA | Workers Static Assets plus the framework-appropriate SPA fallback |
| SSR or full-stack framework | The framework's current Cloudflare Workers adapter and generated deployment config |
| Existing Pages project | Maintain Pages unless migration is explicitly requested |
| Existing Worker config and deploy script | Preserve them and make the smallest necessary correction |

Do not assume `dist`, `build`, or a root-level package. Derive the output and command from scripts and framework configuration.

## Proven sequence

1. Record `git status`, package manager, lockfile, build/deploy scripts, framework, Wrangler version, and existing Cloudflare files.
2. Run the project-local Wrangler identity command and confirm the account.
3. Build locally and fix deployment-blocking issues without broad unrelated cleanup.
4. Create or adjust the source Wrangler configuration. A new static project normally needs a Worker name, current compatibility date, and an assets directory. Full-stack projects may instead produce a deployable Wrangler config during build.
5. Add only the bindings required by code. For an application that genuinely uses them, create D1/KV/R2 resources, write their verified identifiers into configuration, and apply remote D1 migrations before depending on the schema.
6. Set production secrets through protected input. Never copy values into tracked files.
7. Perform a dry run when supported, then use the repository's deploy command. A project-specific command may intentionally sequence resource checks, build, migrations, Worker deployment, and content synchronization; preserve that sequence.
8. Capture the deployed URL and verify the application from outside the local dev server.

## Command selection

Use the local package manager rather than a global Wrangler. Typical command shapes are shown only as discovery aids:

```sh
<package-manager> exec wrangler whoami
<package-manager> exec wrangler deploy --dry-run --config <config>
<package-manager> exec wrangler deploy --config <config>
```

Use `wrangler <command> --help` from the pinned version to confirm resource, migration, environment, and configuration flags. Do not paste remembered commands when the repository already provides an equivalent script.

## Acceptance checks

- The expected Worker and environment appear in deployment output.
- The `https://<worker>.<account-subdomain>.workers.dev` URL returns the intended site.
- A direct request to a non-root application route works as designed.
- JS, CSS, images, and other hashed assets load without mixed-content or MIME errors.
- SSR/API routes return expected status and content; logs show no startup exception.
- Remote D1/KV/R2 behavior is tested only when the application uses those bindings.
- No secrets, local state, generated credentials, or database exports entered Git.

## Current authoritative sources

- Workers Static Assets: <https://developers.cloudflare.com/workers/static-assets/>
- Framework guides: <https://developers.cloudflare.com/workers/framework-guides/>
- Wrangler commands: <https://developers.cloudflare.com/workers/wrangler/commands/>
- Wrangler configuration: <https://developers.cloudflare.com/workers/wrangler/configuration/>
- Secrets: <https://developers.cloudflare.com/workers/configuration/secrets/>
- Workers deployments: <https://developers.cloudflare.com/workers/configuration/versions-and-deployments/>

# Kutt — new-owner runbook

Source commit: `029da42b2459693681886665589fd9512d6b473c`. Static draft for a future operator. **None of these commands was run during this assessment.** Review the pinned source and use a separately approved environment. “Proposed” steps are operator guidance inferred from repository files, not tested upstream recovery instructions.

Static check closure adds dependency metadata, structural candidates, redacted secret scanning and base-image OS packages; it does not validate these procedures. Tests, build/deploy and runtime security/performance **cannot be checked on this machine**. A safe validation needs an isolated disposable VM or container, no network, no host mounts, pre-vetted inputs and synthetic data. See `../coverage.md` for what each run would prove.

## 1. Establish custody before deployment

1. Ask the seller for the running version/image digest, database choice and version, domain/registrar accounts, custom-domain certificates, proxy settings, email/identity providers, backup location and maintenance window. Domain ownership has already mattered to this product. `README.md:9`, `README.md:129`, `README.md:138`.
2. Record whether Redis is enabled and the number of app instances. Agree which instance runs the expiry timer and what happens to pending visit statistics during a move. `server/server.js:20`, `server/queues/queues.js:15`, `server/queues/visit.js:11`.
3. Ask the seller for an actual backup/restore demonstration and release regression evidence. The reviewed manifest provides no test script; a build workflow alone does not demonstrate correct redirects or safe migrations. `package.json:6`, `.github/workflows/docker-build-latest.yaml:32`.

## 2. Configure and deploy

1. Choose one topology to match the seller. The default Compose file uses SQLite with persistent database and customization volumes. PostgreSQL/Redis and MariaDB/Redis examples are alternatives, not additional services required for every deployment. `docker-compose.yml:5`, `README.md:76`.
2. Prepare an approved pinned image and configuration outside this report. The Dockerfile uses Node 22 and an exact lockfile install, but its base tag is mutable. Proposed: record the built image digest and source commit. No image was built for this sample. `Dockerfile:2`, `Dockerfile:11`.
3. Configure variable names only: production requires `JWT_SECRET`; confirm `DEFAULT_DOMAIN`, `PORT`, `TRUST_PROXY`, `DISALLOW_REGISTRATION`, `DISALLOW_ANONYMOUS_LINKS`, `ENABLE_RATE_LIMIT`. Documentation supplies defaults, not a safe configuration for every network. Ask the seller for the approved proxy and abuse-prevention policy. `README.md:96`, `README.md:105`, `README.md:111`, `README.md:130`.
4. For SQLite confirm `DB_CLIENT` and `DB_FILENAME`; default Compose places the file in `/var/lib/kutt`. For PostgreSQL/MySQL confirm `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_SSL`; for Redis, confirm `REDIS_ENABLED`, `REDIS_HOST`, `REDIS_PORT`, `REDIS_PASSWORD`, `REDIS_DB`. Ask the seller for credentials through a secure channel. No credential/environment files were inspected. `docker-compose.yml:9`, `server/knex.js:10`, `server/redis.js:7`.
5. When needed, configure SMTP names `MAIL_ENABLED`, `MAIL_HOST`, `MAIL_PORT`, `MAIL_USER`, `MAIL_PASSWORD`, `MAIL_FROM`, `MAIL_SECURE`; optional OIDC names include `OIDC_ENABLED`, `OIDC_ISSUER`, `OIDC_CLIENT_ID`, `OIDC_CLIENT_SECRET`. Ask the seller for provider ownership and callback settings. `README.md:131`, `README.md:138`, `server/mail/mail.js:9`.
6. The documented operator launch is `docker compose up`, or `docker compose -f docker-compose.postgres.yml up` for that alternative. Proposed: inspect/override the template first, add restart/health/secret configuration appropriate to the hosting platform, and keep public ingress closed until initialization is complete. Startup runs `npm run migrate` before `npm start`. `README.md:70`, `README.md:76`, `Dockerfile:25`.
7. A new installation prompts for an administrator; an existing one needs a controlled account transfer. Proposed validation: check `/api/v2/health`, sign in, create a disposable short link, follow its redirect, verify its statistics, and check account email if enabled. The health handler only returns text and does not prove database or queue health. `README.md:61`, `server/server.js:78`, `server/routes/routes.js:18`, `server/routes/health.routes.js:5`, `server/queues/visit.js:11`.

## 3. Back up and restore — proposed; ask the seller to validate

1. Define recovery-time and data-loss targets and a maintenance window with the seller. Stop new writes and let pending work drain using a seller-approved procedure. The source defines storage and queues, not a complete operational recovery command. `docker-compose.yml:6`, `server/queues/queues.js:16`.
2. SQLite: ask the seller for a tested transaction-consistent backup method. Proposed fallback for an agreed outage: stop **all** database writers, preserve the complete database volume, and back up the separate customizations volume. Do not just copy a live database file. Retain the matched image and configuration names with the encrypted, off-host backup. `docker-compose.yml:6`, `docker-compose.yml:7`, `server/knex.js:12`.
3. PostgreSQL/MySQL: **ask the seller** for database-native backup/restore commands matched to server versions, a verified dump and the customizations volume. Do not treat an uncoordinated copy of the live database volume as a recovery procedure. The PostgreSQL example's image is not version-pinned. `docker-compose.postgres.yml:22`, `docker-compose.postgres.yml:26`, `README.md:112`.
4. Redis: **ask the seller** whether pending visit jobs must survive. This is not solely a cache. Agree drain/snapshot/replay handling and duplicate-statistics behavior; the example does not provide a complete queue-recovery policy. `server/queues/queues.js:16`, `server/queues/queues.js:19`, `server/queues/visit.js:23`.
5. Proposed restore drill: restore volumes/database into an isolated instance with the matched application image and custom files; control egress and domain routing to prevent sending production mail or taking live redirects. Check users, link count, a sample redirect, customization and visit statistics. Ask the seller to approve results before traffic cutover. `docker-compose.yml:5`, `server/server.js:54`, `server/server.js:82`, `server/mail/mail.js:21`.
6. Do not assume an older app image undoes a database migration. Keep the pre-migration database checkpoint and matched application together; ask the seller for a rehearsed rollback. `Dockerfile:25`, `knexfile.js:23`.

## 4. Rotate secrets and transfer accounts

1. Proposed: inventory secret names/custodians, arrange new provider access, and schedule a maintenance window. Keep all values outside this pack. The app supports file-supplied variables as documented; the required production signing-secret name is `JWT_SECRET`. `README.md:96`, `README.md:98`.
2. Replace the JWT signing secret through the approved deployment channel and restart all app instances together. Proposed: expect active JWTs and OIDC cookie sessions signed with the old key to stop validating; test fresh login and rollback access. Ask the seller for an exact tested rotation procedure and acceptable logout window. `server/passport.js:11`, `server/server.js:46`.
3. Rotate database, Redis, SMTP and identity-provider secrets at each provider and update the app in a coordinated window. Ask the seller for overlap behavior and recovery access; provider-specific commands were not validated. `server/knex.js:17`, `server/redis.js:12`, `server/mail/mail.js:15`, `README.md:142`.
4. Regenerate individual API keys through the supported authenticated account workflow and update clients before revoking access. Ask the seller for the client inventory and whether old keys remain in backups/caches. The handler creates and writes a replacement key; do not paste it into this report. `server/handlers/auth.handler.js:212`, `server/handlers/auth.handler.js:218`, `server/passport.js:64`.
5. Transfer registrar, DNS, certificate-renewal and provider billing ownership. For custom domains the README makes certificate generation an operator responsibility. Ask the seller for renewal automation, expiry dates and recovery codes through a secure handover channel. `README.md:129`, `README.md:9`.

## 5. First week checklist

| When | Action and completion evidence |
| --- | --- |
| Day 1 | Confirm domain/provider control, database topology, instance count and a pre-transfer backup. `README.md:9`, `docker-compose.yml:5`, `server/server.js:20`. |
| Day 2 | Demonstrate isolated restore including custom files and queued visits, then agree recovery targets. Ask the seller to sign off. `docker-compose.yml:7`, `server/queues/queues.js:16`. |
| Day 3 | Triage hbs/MySQL advisory matches and ask for a representative regression checklist or private tests. `package.json:41`, `package.json:48`, `package.json:6`. |
| Day 4 | Transfer/rotate secrets, test fresh login/API access and mail, and verify custom-domain TLS. `server/passport.js:13`, `server/mail/mail.js:21`, `README.md:129`. |
| Day 5 | Observe expiry processing, queue failures and visit counts; agree alerting and an escalation owner. Ask the seller for monitoring thresholds. `server/cron.js:5`, `server/queues/queues.js:21`, `server/queues/visit.js:23`. |
| Days 6–7 | Rehearse a pinned release and matched rollback in isolation; retain the MIT notice in distributed copies. `Dockerfile:25`, `LICENSE:12`. |

This runbook cannot yet guarantee that another owner could run the seller's actual deployment. Tested backup commands, provider-specific rotation, production configuration, resource limits and release regression evidence remain **ask the seller** gaps.

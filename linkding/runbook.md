# Linkding — new-owner runbook

Source commit: `27b7303baf41bb28babc610ac8eaa486e1ddfab5`. Static draft for a future operator. **None of these commands was run during this assessment.** Execute only in a separately approved environment after reviewing the pinned source. Instructions labelled “proposed” are operator guidance inferred from the files, not a repository-tested procedure.

Static check closure adds dependency metadata, structural candidates, redacted secret scanning and base-image OS packages; it does not validate these procedures. Tests, build/deploy and runtime security/performance **cannot be checked on this machine**. A safe validation needs an isolated disposable VM or container, no network, no host mounts, pre-vetted inputs and synthetic data. See `../coverage.md` for what each run would prove.

## 1. Establish custody before deployment

1. Ask the seller for the running image digest, database engine, data-volume location, domain/proxy ownership, user inventory, enabled integrations, backup schedule and recovery targets. Record answers without copying credential values into this pack. The supplied deployment is only a template: `docker-compose.yml:3`, `docker-compose.yml:4`, `docker-compose.yml:8`.
2. Agree a maintenance window and rollback checkpoint. Startup runs migrations automatically; retain a matched application version and database backup. Proposed release rule based on `bootstrap.sh:19`.
3. Choose the standard image or the larger snapshot-capable variant to match the seller's features; Chromium changes memory/disk needs. Ask the seller for actual resource use. `docs/src/content/docs/installation.md:14`, `docker/default.Dockerfile:95`.

## 2. Configure and deploy

1. Use the Docker or Compose installation path in `docs/src/content/docs/installation.md:50` or `docs/src/content/docs/installation.md:67`. Proposed: pin an approved image digest, bind the persistent data volume at `/etc/linkding/data`, and expose port 9090 only through the agreed network boundary. `docker-compose.yml:4`, `docker-compose.yml:6`, `docker-compose.yml:8`.
2. Supply configuration through an operator-controlled secret/configuration channel. The provided Compose template references a local environment file; that file and its example were not inspected. Ask the seller to supply and explain an equivalent configuration securely. `docker-compose.yml:9`.
3. Variable names to confirm, with requiredness determined by deployment: `LD_HOST_PORT`, `LD_HOST_DATA_DIR`, `LD_CONTAINER_NAME` have template defaults (`docker-compose.yml:3`); PostgreSQL needs `LD_DB_ENGINE`, `LD_DB_HOST`, `LD_DB_DATABASE`, `LD_DB_USER`, `LD_DB_PASSWORD`, `LD_DB_PORT` as applicable (`bookmarks/settings/base.py:263`). Proxy/origin settings include `HOST_NAME`, `LD_CSRF_TRUSTED_ORIGINS`, `LD_USE_X_FORWARDED_HOST`; optional login modes include `LD_ENABLE_AUTH_PROXY`, `LD_ENABLE_OIDC` (`bookmarks/settings/prod.py:26`, `docs/src/content/docs/options.md:278`, `docs/src/content/docs/options.md:123`, `docs/src/content/docs/options.md:175`). Full source-derived names are in `facts.json`; this is not a verified production configuration.
4. The documented Compose launch is `docker-compose up -d`. It is a future operator instruction; review and adapt the template first. Startup generates a signing key, migrates, initializes optional user settings and launches workers/web. `docs/src/content/docs/installation.md:71`, `bootstrap.sh:16`, `bootstrap.sh:30`.
5. For a new isolated installation, create the initial administrator interactively with `docker-compose exec linkding python manage.py createsuperuser`. For an existing installation, arrange account transfer instead of creating an untracked extra administrator. `docs/src/content/docs/installation.md:78`.
6. Proposed validation: check the `/health` route, sign in, create/retrieve a disposable bookmark, and confirm a background task finishes. A health response alone does not prove worker health. Ask the seller for a safe test account and endpoint/proxy mapping. `docker/default.Dockerfile:79`, `supervisord-tasks.conf:9`, `bookmarks/services/tasks.py:126`.

## 3. Back up and restore

1. For SQLite, the documented commands are `docker exec -it linkding python manage.py full_backup /etc/linkding/data/backup.zip`, followed by `docker cp linkding:/etc/linkding/data/backup.zip backup.zip`. Adapt the container name. Move the result off the application host into access-controlled backup storage. `docs/src/content/docs/backups.md:27`, `docs/src/content/docs/backups.md:33`.
2. Proposed: pause writes and background work for a consistent database/assets checkpoint, or have the seller demonstrate consistency under live writes. The command uses SQLite's backup API, but walks asset directories separately. Avoid an uncoordinated copy of a live database file. `bookmarks/management/commands/full_backup.py:20`, `bookmarks/management/commands/full_backup.py:33`, `docs/src/content/docs/backups.md:54`.
3. Ask the seller for signing-key escrow and a policy for pending background jobs: the zip does not include the signing-key file or queue store. Keep secret material outside this report and transfer it securely; never inspect or paste it into the pack. `bookmarks/settings/prod.py:19`, `bookmarks/settings/base.py:180`, `bookmarks/management/commands/full_backup.py:18`.
4. The documented restore extracts the zip into a new data directory, mounts it at `/etc/linkding/data`, then starts the container. Proposed: restore into a separate isolated instance using the matched image; verify users, bookmark counts, one snapshot/image and job behavior before any cutover. Confirm permissions with the seller; bootstrap changes data ownership. `docs/src/content/docs/backups.md:40`, `bootstrap.sh:28`.
5. PostgreSQL deployments: **ask the seller** for a tested database-native dump/restore procedure, server version/extensions, credentials custody and a corresponding assets snapshot. The SQLite command is not a PostgreSQL backup. Do not run it as a substitute. `docs/src/content/docs/backups.md:23`, `bookmarks/settings/base.py:271`.

## 4. Rotate secrets and transfer integrations

1. Proposed: inventory signing-key, database, API, proxy and OIDC credentials by name and custodian only. Ask the seller which are live; source references do not establish ownership. `bookmarks/settings/prod.py:19`, `bookmarks/settings/base.py:267`, `docs/src/content/docs/options.md:175`.
2. Signing key: schedule a forced reauthentication window, securely provision a new persistent key through the deployment's approved process, then restart and validate login. **Ask the seller** for the exact rotation/rollback procedure: the generator command only creates a file when absent and is not itself a rotation command. `bookmarks/management/commands/generate_secret_key.py:14`, `bookmarks/settings/prod.py:19`.
3. Database/OIDC credentials: proposed coordinated change at the provider and app configuration, followed by restart and a database/login check. Ask the seller for overlap support and rollback access. No provider-specific procedure was validated. `bookmarks/settings/base.py:267`, `docs/src/content/docs/options.md:175`.
4. API tokens: ask the seller for the client inventory and replace/revoke tokens using the supported account workflow; test each dependent client before revocation. Token authentication is exercised by `bookmarks/tests/test_auth_api.py:12`. An automated bulk-rotation procedure is not supplied here.
5. Decide whether Internet Archive integration is appropriate for the new operation; it sends bookmarked URLs to a third party when active. Ask the seller for user settings and consent expectations. `bookmarks/services/tasks.py:43`, `bookmarks/services/tasks.py:60`.

## 5. First week checklist

| When | Action and completion evidence |
| --- | --- |
| Day 1 | Obtain infrastructure/domain custody, record image/configuration names, and secure a pre-transfer backup. Ask the seller for the running topology. `docker-compose.yml:4`, `docker-compose.yml:8`. |
| Day 2 | Demonstrate isolated restore with sample counts and assets; settle key/queue recovery. Ask the seller to sign off recovery time and loss tolerance. `docs/src/content/docs/backups.md:40`, `bookmarks/settings/base.py:180`. |
| Day 3 | Review OSV matches and agree a tested patch order; request actual unit/browser test results from the release environment. `uv.lock:317`, `.github/workflows/main.yaml:33`, `.github/workflows/main.yaml:62`. |
| Day 4 | Transfer/rotate credentials and test a representative API/SSO client; retain rollback access only for the agreed window. `bookmarks/settings/prod.py:19`, `docs/src/content/docs/options.md:175`. |
| Day 5 | Check worker logs and snapshot completion; agree alerting, backup retention and on-call ownership. Ask the seller for thresholds and escalation contacts. `supervisord-tasks.conf:10`, `bookmarks/services/tasks.py:303`. |
| Days 6–7 | Rehearse a release with a pre-migration backup, record decisions, and retain the licence notice in distributed copies. `bootstrap.sh:19`, `LICENSE.txt:7`. |

Unresolved seller answers are handover requirements, not steps this sample has completed. Runtime validation, resource sizing, monitoring configuration, PostgreSQL restore and exact credential-rotation commands remain unverified.

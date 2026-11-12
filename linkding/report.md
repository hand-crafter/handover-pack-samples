# Linkding — buyer answer sheet

Public-source sample · 12 November 2026 · Static inspection, not a security assurance or a report on a business for sale.

Repository: https://github.com/sissbruecker/linkding · Commit: `27b7303baf41bb28babc610ac8eaa486e1ddfab5`.

## Page 1 — Ten questions before buying

<!-- licence-screen:start -->
### Dependency licence screen — ask before buying

**31 copyleft-labelled; 20 unlabelled** dependency versions out of 641 screened (4 other/review labels). Copyleft terms can require sharing source code or keeping notices when software is distributed; some also apply to network use. Unlabelled packages leave permission to use or transfer them uncertain. This can add legal review, replacement work or conditions for a buyer; a label alone does not establish an obligation. **Ask the seller** for the exact package licences, which packages ship or run in the product, how licence duties are met, and evidence of rights to transfer the code. Counts include development and transitive versions, not just shipped packages. Evidence: `facts.json`, dependencies.licence_mix and registry_metadata; registry labels only, no legal clearance.
<!-- licence-screen:end -->

| Buyer question | Evidence-backed answer |
| --- | --- |
| 1. What am I taking over? | A Django bookmark manager with sharing, archiving and an API. `README.md:19`, `README.md:61`. |
| 2. What must stay running? | A web process and Huey worker, with a separate SQLite task store. `bootstrap.sh:30`, `bookmarks/settings/base.py:178`. |
| 3. Where is customer data? | SQLite or PostgreSQL, plus on-disk assets; a database alone is insufficient. `docs/src/content/docs/backups.md:10`. |
| 4. Can another engineer deploy it? | Docker instructions exist; the supplied image tag moves. Ask for the deployed version. `docker-compose.yml:4`. |
| 5. Can I recover after a server loss? | SQLite restore is documented; PostgreSQL recovery needs a seller procedure. `docs/src/content/docs/backups.md:23`. |
| 6. Are there known dependency flaws? | OSV matches need triage against the locked Django/REST-framework versions. `uv.lock:290`, `uv.lock:317`. |
| 7. What evidence supports safe changes? | CI declares unit and browser tests; passing results were not checked. `.github/workflows/main.yaml:33`, `.github/workflows/main.yaml:62`. |
| 8. Does it expose credentials? | A base signing-key candidate is replaced in production; no deployed leak is established. `bookmarks/settings/base.py:25`, `bookmarks/settings/prod.py:19`. |
| 9. What outside services and permissions transfer? | Optional archiving sends bookmarked URLs to Internet Archive. Ask for account/settings custody. `bookmarks/services/tasks.py:60`. |
| 10. Can I maintain and distribute it? | MIT permits reuse with its notice retained. Ask for a maintainer handover; bus factor is unproven. `LICENSE.txt:7`, `README.md:57`. |

Not checked: running software, installed dependencies, builds, current CI outcomes, measured test coverage, attack resistance, load, production settings, actual backups/restores, account ownership, revenue, operating costs, customer-data compliance, full history or downloaded package licence texts or transfer rights. Environment and credential files were excluded. Nothing in this sample establishes that any business is sale-ready.

## Page 2 — Verdict and evidence coverage

Verdict: **a documented product that warrants a recovery drill and dependency triage before a buyer relies on the handover.** Green means documentary evidence exists, amber means a material question remains, red means a handover condition is unmet. These are review priorities, not a valuation or vulnerability severity score.

| Area | Light | Buyer outcome and evidence |
| --- | --- | --- |
| Product and structure | GREEN | Conventional web process and worker are visible; ownership boundaries still need confirmation. `README.md:61`, `supervisord-all.conf:8`, `supervisord-all.conf:17`. |
| Dependencies | AMBER | Known-version advisory matches need runtime triage; Python and frontend/docs dependencies have different exposure. `uv.lock:290`, `uv.lock:317`, `docker/default.Dockerfile:26`. |
| Credential handling | AMBER | Production reads a generated signing key; operational rotation and custody are not supplied. `bookmarks/settings/prod.py:19`, `bookmarks/management/commands/generate_secret_key.py:14`. |
| Tests and CI | GREEN | 1,393 declarations in 101 test-area files (including helpers), with unit/browser CI jobs; no passing-state claim. `bookmarks/tests/test_auth_api.py:12`, `.github/workflows/main.yaml:32`, `.github/workflows/main.yaml:61`. |
| Deployment and rollback | AMBER | Moving image tag plus startup migrations require a seller-tested version/rollback pair. `docker-compose.yml:4`, `bootstrap.sh:19`. |
| Recovery | AMBER | SQLite backups are documented, but key/queue custody and PostgreSQL recovery require extra work. `docs/src/content/docs/backups.md:23`, `bookmarks/management/commands/full_backup.py:18`, `bookmarks/settings/base.py:180`. |
| Maintainer dependency | AMBER | First-person contribution policy and concentrated recent history make handover availability worth asking about. Neither proves one-person operational dependency. `README.md:57`; history method in `facts.json`. |
| Licence | GREEN | Retain the MIT copyright and permission notice; this does not clear bundled code or acquisition rights. `LICENSE.txt:7`. |

Measured inventory: **36,737 code lines** after scc excludes blanks/comments, across eligible first-party Python, JS/TS, HTML, CSS and shell. Protected files, binaries and `vendor` directories are excluded. Counts include tests, migrations and styles; they are not a complexity or quality score. Exact inputs and hashes are in `facts.json`. Check coverage: **12 ran, 7 partial, 3 not run** of 22; see `../coverage.md`.

Observed history: 100 reachable commits in a depth-100 shallow clone, 22 author-name strings. The leading human name has 43%; an automated dependency author has 37%. Last commit: 13 September 2026. Names are not alias-merged; no lifetime bus-factor claim is made. Evidence is Git commit metadata at the recorded hash, rather than a fabricated source-line citation.

Dependency appendix: 641/641 unique pins queried against public OSV; 16 versions matched advisory IDs. Local osv-scanner also inventoried 641 pins. Aliases are not independent flaws. All 641 pins have publish dates (62–4652 days old); age is not risk. Registry licence screen: 586 permissive, 31 copyleft candidates, 20 unknown, 4 other/review. Labels include dev/platform tuples and dual-licence alternatives, not a legal clearance. Base-image OS inventory is in `facts.json`; final target images remain unverified.

Two advisory records were inspected manually: [REST framework content-negotiation denial of service](https://osv.dev/vulnerability/GHSA-33wh-fxxf-88vv) reports a fixed version of 3.18.2; [Django GeoDjango denial of service](https://osv.dev/vulnerability/GHSA-q238-5cxm-5c9h) concerns an optional subsystem. Do not infer that Linkding exposes GeoDjango simply because Django is installed. Locked pins are at `uv.lock:317` and `uv.lock:290`; the saved advisory review records describe the provider response, not an exploit test.

## Page 3 — Five questions to resolve first

1. **What exactly survives a restore?** The “full backup” command packages the database, assets, favicons and previews; it does not package `secretkey.txt` or `tasks.sqlite3`. The signing key is read separately and the task store has its own file. Ask the seller to demonstrate restore, explain queued-work handling and agree whether a forced sign-in is acceptable. Evidence: `bookmarks/management/commands/full_backup.py:18`, `bookmarks/management/commands/full_backup.py:25`, `bookmarks/settings/prod.py:19`, `bookmarks/settings/base.py:180`.

2. **Which advisory matches affect the shipped app?** Triage the REST-framework pin first, distinguish optional Django subsystems, and separate docs/build dependencies from runtime. Ask for a reviewed update and test results; this pack does not prescribe an untested upgrade. Evidence: `uv.lock:317`, `uv.lock:290`, `pyproject.toml:11`, `docker/default.Dockerfile:27`.

3. **Can the seller reproduce and roll back the running version?** Compose references a moving image tag; bootstrap automatically migrates the database. A previously working image may not understand a newly migrated schema. Ask for an image digest, configuration inventory and a matched pre-upgrade backup. Evidence: `docker-compose.yml:4`, `bootstrap.sh:19`.

4. **Where do background failures become visible?** The normal startup path supervises the worker separately and logs it to a file, while an experimental mode sends worker output to container logs. Ask which mode is deployed and how stalled snapshots are noticed; no production alerting was inspected. Evidence: `bootstrap.sh:32`, `supervisord-tasks.conf:9`, `supervisord-tasks.conf:10`, `supervisord-all.conf:21`.

5. **Who controls authentication and outbound requests?** Production replaces the base signing-key candidate; this is not a demonstrated production secret leak. Optional proxy authentication and OIDC change the trust boundary, and archive jobs send bookmarked URLs outward. Ask for key custody, proxy/identity-provider ownership and archive consent settings. Evidence: `bookmarks/settings/base.py:25`, `bookmarks/settings/prod.py:19`, `docs/src/content/docs/options.md:123`, `docs/src/content/docs/options.md:175`, `bookmarks/services/tasks.py:60`.

### What this check did not cover

All findings are static observations or stated inferences. No dependency or target code was installed, built or executed. No database, running container, deployed endpoint, sign-in, seller account or secret value was accessed for validation. The regex and gitleaks scans cannot clear a repository of secrets: protected configuration/credential paths, binary files, large files and Git history are outside it. File inventory exclusions are explicit in `facts.json`. Published base images received OS-package inventories; target-built images and the host OS were not assessed. It does not assess asset ownership, all licence obligations, exploitable call paths, security guarantees, capacity, customer count, financial performance or a seller's actual operating practices.

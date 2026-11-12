# Kutt — buyer answer sheet

Public-source sample · 12 November 2026 · Static inspection, not a security assurance or a report on a business for sale.

Repository: https://github.com/thedevs-network/kutt · Commit: `029da42b2459693681886665589fd9512d6b473c`.

## Page 1 — Ten questions before buying

<!-- licence-screen:start -->
### Dependency licence screen — ask before buying

**1 copyleft-labelled; 8 unlabelled** dependency versions out of 422 screened (2 other/review labels). Copyleft terms can require sharing source code or keeping notices when software is distributed; some also apply to network use. Unlabelled packages leave permission to use or transfer them uncertain. This can add legal review, replacement work or conditions for a buyer; a label alone does not establish an obligation. **Ask the seller** for the exact package licences, which packages ship or run in the product, how licence duties are met, and evidence of rights to transfer the code. Counts include development and transitive versions, not just shipped packages. Evidence: `facts.json`, dependencies.licence_mix and registry_metadata; registry labels only, no legal clearance.
<!-- licence-screen:end -->

| Buyer question | Evidence-backed answer |
| --- | --- |
| 1. What am I taking over? | A Node/Express URL shortener with custom domains, accounts and statistics. `README.md:5`, `server/server.js:31`. |
| 2. What must stay running? | Web server, expiry timer and visit processing; Redis is optional. `server/server.js:20`, `server/queues/queues.js:15`. |
| 3. Where is customer data? | SQLite and customizations use separate volumes; other databases are supported. `docker-compose.yml:5`, `README.md:112`. |
| 4. Can another engineer deploy it? | Setup is documented; container startup migrates the database automatically. `README.md:57`, `Dockerfile:25`. |
| 5. Can I recover after a server loss? | Storage is defined; ask for a tested database, customization and queue recovery procedure. `docker-compose.yml:5`, `server/queues/queues.js:16`. |
| 6. Are there known dependency flaws? | OSV matches include hbs and optional mysql2; exploitability was not tested. `package.json:41`, `package.json:48`. |
| 7. What evidence supports safe changes? | No matching tests, package test script or CI test job were found in this snapshot. `package.json:6`, `.github/workflows/docker-build-latest.yaml:12`. |
| 8. Does it expose credentials? | No eligible-text regex hits; this is not clearance. Production needs a signing secret. `README.md:96`, `server/passport.js:13`. |
| 9. What outside services and permissions transfer? | SMTP, optional identity login and domain certificates need custody. `README.md:129`, `README.md:131`, `README.md:138`. |
| 10. Can I maintain and distribute it? | Retain the MIT notice. Request maintainer handover; recent commit share is not a bus factor. `LICENSE:12`, `README.md:260`. |

Not checked: running software, installs/builds, current CI results, private tests, measured coverage, exploitation, load, production settings, actual backup/restore, domain/provider ownership, revenue, costs, data compliance, full history or downloaded package licence texts or transfer rights. Environment and credential files were excluded. This public code sample is not an assessment of a business being offered for sale.

## Page 2 — Verdict and evidence coverage

Verdict: **a small, understandable product whose buyer still needs a demonstrated recovery procedure and evidence for regression safety.** Green means documentary evidence exists, amber means an unresolved question, red means an unmet handover condition. Colours express buyer priorities, not measured vulnerability severity or valuation.

| Area | Light | Buyer outcome and evidence |
| --- | --- | --- |
| Product and structure | GREEN | Express entry point, templates, API and redirect route are visible. `server/server.js:31`, `server/server.js:64`, `server/server.js:78`, `server/server.js:82`. |
| Dependencies | AMBER | Runtime hbs and optional MySQL driver have advisory matches to triage. `package.json:41`, `package.json:48`, `server/knex.js:7`. |
| Credential handling | AMBER | JWT signing secret and account-provider secrets require a transfer/rotation plan; zero regex hits do not prove safety. `README.md:96`, `README.md:142`, `server/passport.js:13`. |
| Tests and CI | RED | No test script in the manifest or test step in the reviewed build workflow; request private test evidence and a release smoke suite. `package.json:6`, `.github/workflows/docker-build-latest.yaml:32`. |
| Deployment and rollback | AMBER | Container startup runs migrations; the alternative database image is unpinned. `Dockerfile:25`, `docker-compose.postgres.yml:22`. |
| Recovery | RED | Persistent volumes are defined, but the setup section gives no demonstrated restore procedure. Request one before relying on handover. `docker-compose.yml:6`, `docker-compose.yml:7`, `README.md:57`. |
| Maintainer dependency | AMBER | Contributor acknowledgements and recent commit concentration support requesting a guided transfer; not proof of a one-person bus factor. `README.md:260`; history method in `facts.json`. |
| Licence | GREEN | Retain the MIT notice; bundled dependencies, branding and transfer rights need separate review. `LICENSE:12`. |

Measured inventory: **12,878 code lines** after scc excludes blanks/comments in eligible JavaScript, HTML, Handlebars and CSS. Counts include documentation/static material; protected files and unsupported binaries are excluded. File hashes and exclusions are in `facts.json`. Check coverage: **12 ran, 7 partial, 3 not run** of 22; see `../coverage.md`.

Observed history: 147 reachable commits in a depth-100 shallow clone; merges can yield more than 100 commits. There are 13 author-name strings; the leading name has 120 commits (81.6%) and a possible alias has seven (4.8%), not merged by the collector. Last commit: 19 October 2026. This is recent-history concentration, not an assessment of employee count or operational knowledge. Evidence is Git metadata at the recorded commit, not a source-file line.

Dependency appendix: 422/422 unique pins queried against public OSV; 16 versions matched advisory IDs. Local osv-scanner also inventoried 422 pins. Aliases are not independent flaws. All 422 pins have publish dates (130–5452 days old); age is not risk. Registry licence screen: 411 permissive, 1 copyleft candidates, 8 unknown, 2 other/review. Labels include dev/platform tuples and dual-licence alternatives, not a legal clearance. Base-image OS inventory is in `facts.json`; final target images remain unverified.

Manually inspected provider records: [hbs information exposure](https://osv.dev/vulnerability/GHSA-7f5c-rpf4-86p8) marks versions through 4.4.0 affected and supplies no fixed version; [mysql2 authentication downgrade](https://osv.dev/vulnerability/GHSA-3f6p-5ww8-9rcr) reports a fix at 3.22.0. These records do not establish an exploit in Kutt. Verify upstream guidance, database choice and compatibility before an update. The pins are at `package.json:41` and `package.json:48`.

## Page 3 — Five questions to resolve first

1. **What proves a release still works?** The manifest has start/migration/docs scripts but no test script, and the reviewed workflow builds/pushes an image without a test step. Request any private tests, past release evidence and a repeatable smoke checklist for redirects, accounts and statistics. Evidence: `package.json:6`, `.github/workflows/docker-build-latest.yaml:12`, `.github/workflows/docker-build-latest.yaml:32`.

2. **Can the seller restore every durable component?** The default deployment persists SQLite and customized assets separately. With Redis enabled, visit work is queued there, so describing Redis as disposable cache would hide potential unprocessed statistics. Request a restore demonstration and an agreed queue-loss policy. Evidence: `docker-compose.yml:6`, `docker-compose.yml:7`, `server/queues/queues.js:16`, `server/queues/visit.js:11`.

3. **Which advisory matches warrant action on this deployment?** hbs is a runtime template dependency; mysql2 is relevant when a MySQL-family backend is selected. Triage against upstream records and request compatible updates, without presenting all OSV IDs as exploitable bugs. Evidence: `package.json:41`, `package.json:48`, `server/server.js:64`, `server/knex.js:7`.

4. **What is the tested upgrade and rollback sequence?** Container startup applies migrations automatically; the PostgreSQL example uses an unversioned image. Ask for pinned images and a database backup made before migration; restarting an older image is not a demonstrated schema rollback. Evidence: `Dockerfile:25`, `docker-compose.postgres.yml:22`, `knexfile.js:23`.

5. **Who owns the domains, certificates and background-work convention?** The README records a former domain passing to another party and says custom-domain certificates are manual. The expiry timer is gated on an instance number, so clustered hosting needs one agreed scheduler. Ask for registrar/TLS custody and the process topology. Evidence: `README.md:9`, `README.md:129`, `server/server.js:20`, `server/cron.js:5`.

### What this check did not cover

No target code, package install, build, container or test was run. No production service, account or secret was contacted for validation. Tests may exist outside the tracked tree. Regex and gitleaks scans exclude protected files, binaries, large files and Git history; a clean result is not secret clearance. Published base images received OS-package inventories; target-built images and the host OS were not assessed. No authorization, template-injection, database transport-security, rate-limit, performance, revenue, privacy-compliance or acquisition-rights audit was performed. Infrastructure settings, private integrations and operational experience can change every handover conclusion here.

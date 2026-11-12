# What was checked, and what was not

12 November 2026. Both samples were regenerated in place at their existing frozen commits. No target dependency was installed, and no target code, build, test, container or deployment was executed. Docker was not started. Registry package metadata and published base-image layers were inspected as data. Protected environment/credential files were not opened; candidates retain locations and kinds only, with no values.

## Before and after

“Ran” means the stated static operation completed, not that the product passed a security or sale-readiness gate. Seven previously unrun checks now have evidence in each sample; the licence screen remains partial because some registry labels are absent.

| Sample | Before: ran / partial / not run | After: ran / partial / not run | Pins with dates / total | Final collector seconds |
| --- | --- | --- | --- | --- |
| linkding | 6 / 6 / 10 | 12 / 7 / 3 | 641 / 641 | 298.07 |
| kutt | 6 / 6 / 10 | 12 / 7 / 3 | 422 / 422 | 178.21 |

## The ten original gaps

| Check | Linkding before → after | Kutt before → after | Method, result and remaining limit |
| --- | --- | --- | --- |
| `gitleaks` | not run → **ran** | not run → **ran** | Verified gitleaks, `dir` non-Git mode (current equivalent of `--no-git`) per explicit eligible file; full redaction and trusted config. Linkding 395 files / 0 findings; Kutt 207 / 0. No errors. Protected/oversized/binary files and Git history excluded; no secret clearance. The separate regex candidate count remains 1 / 0. |
| `ast_grep` | not run → **ran** | not run → **ran** | Installed ast-grep structural Python/JS/TS rules for entry points, routes, jobs, DB access and outbound HTTP; stdin only, saved locations merged into `structure`. Linkding 257 files / 707 matches; Kutt 64 / 313. No errors. Structural candidates do not establish call paths or deployment topology. |
| `osv_scanner` | not run → **ran** | not run → **ran** | Verified local scanner on explicit eligible lockfiles, trusted config; no resolution, Git/directory scan or call analysis. Linkding 641 package rows / 16 matched; Kutt 422 / 16. No errors. Public querybatch also covers every supported pin; aliases and reachability still need triage. |
| `transitive_dependency_age` | not run → **ran** | not run → **ran** | npm version publish timestamps and earliest PyPI version-file upload dates for all unique pinned tuples, including transitives. Linkding 641/641, 62–4652 days; Kutt 422/422, 130–5452 days. Zero missing dates or request failures. Four workers, at most five request starts/second per run, 15-second HTTP timeouts; no npm/PyPI package downloads. Age is not vulnerability or update freshness. |
| `dependency_licences` | not run → **partial** | not run → **partial** | Same metadata pass: npm version `license`, PyPI `info.license` / licence classifiers. Linkding: 586 permissive, 31 copyleft, 20 unknown, 4 other/review. Kutt: 411 permissive, 1 copyleft, 8 unknown, 2 other/review. Every tuple queried; no request failures. Unknown labels and nonstandard text require authoritative version metadata or separately authorized licence-text/rights review; no package archives downloaded. |
| `untracked_files` | not run → **ran** | not run → **ran** | `git status --porcelain=v1 --ignored -z`, counts/names only. Linkding 0 untracked / 0 ignored; Kutt 0 / 0. No contents read. Git directory entries count once rather than counting their descendants. |
| `container_os_dependencies` | not run → **ran** | not run → **ran** | Verified Syft, `registry:` source only, linux/amd64, OS catalogers; no Docker daemon, target image build or container. Linkding: all three public bases, 19 / 29 / 87 OS packages. Kutt: its one public base, 19. No errors; image digests and package tuples saved. Does not cover host OS, packages added by target Dockerfiles, final images, other architectures or OS vulnerabilities. |
| `test_execution` | not run → **not run** | not run → **not run** | **Cannot be checked on this machine:** target code is untrusted and execution is forbidden. Safe prerequisite and proof are listed below; no target tests were run. |
| `build_and_deploy` | not run → **not run** | not run → **not run** | **Cannot be checked on this machine:** target installation/build/deployment is forbidden; Docker is stopped and was not started. Safe prerequisite and proof are listed below. |
| `runtime_security_performance` | not run → **not run** | not run → **not run** | **Cannot be checked on this machine:** dynamic security, load and recovery checks execute untrusted code and are forbidden. Static scans cannot substitute for them; safe prerequisite and proof are listed below. |

## What would close the three execution checks

All three need a separately approved, isolated disposable VM or container, **no network, no host mounts**, pre-vetted dependencies/tools staged in its disposable filesystem, resource limits and synthetic data. None was provisioned or run here.

| Check | Safe run needed | What it would prove, within that environment |
| --- | --- | --- |
| Tests | Install only pre-vetted offline inputs in the disposable environment, collect and run the intended suites, retain results and coverage if requested. | Which tests are collected and whether they pass; coverage only if measured. Kutt first needs an actual seller-provided or newly authorized suite, since this snapshot exposes no matching test inventory. |
| Build/deploy | Reproduce the pinned build with offline inputs; deploy only to disposable internal services and synthetic databases, then rehearse startup, migration and rollback. | Whether that artifact builds and the stated release/rollback sequence works under the specified setup. It would not certify production. |
| Runtime security/performance | Start only inside the isolated environment; use synthetic identities/data, vetted authorization/security checks, local load generation and a restore drill. | Observed authorization behavior, failures, capacity and recovery under stated conditions; not exhaustive security or production assurance. |

## Licence and structure interpretation

Licence categories are metadata screening heuristics. A copyleft candidate includes an expression containing a copyleft alternative: Kutt’s DOMPurify label is `(MPL-2.0 OR Apache-2.0)`, so its count does not establish a mandatory copyleft obligation. Linkding includes platform-specific sharp/libvips and lightningcss tuples plus development tools; counts do not mean all variants ship together. Long free-form PyPI licence text uses its explicit classifier for screening, rather than keyword-matching incidental licence references in the text. Stored text labels are bounded to 240 characters with an explicit truncation flag. No full licence or acquisition-rights audit occurred.

Structural matches are candidates, including tests and optional integrations. Category counts in the final runs:

| Candidate kind | Linkding | Kutt |
| --- | --- | --- |
| entry_points | 2 | 4 |
| routes | 66 | 150 |
| background_jobs | 15 | 9 |
| data_stores | 612 | 149 |
| third_party_calls | 12 | 1 |

## Published base images inspected

Only external `FROM` images were scanned; named build-stage aliases were skipped. All scans selected linux/amd64. Image tags can move; `container_os_dependencies.scans` records the observed manifest digests and package tuples.

| Published base | Linkding OS packages | Kutt OS packages |
| --- | --- | --- |
| `node:22-alpine` | 19 | 19 |
| `python:3.13.7-alpine3.22` | 29 | — |
| `python:3.13.7-slim-trixie` | 87 | — |

## Downloaded inspection binaries

Official GitHub release assets were downloaded into a local tools folder. Each asset was compared against its project’s published SHA-256 checksum file **before extraction or execution**. No package manager or global installation was used. Only the named regular binary was extracted from each archive.

| Binary | Version | Checksum verified | Verified release-asset SHA-256 |
| --- | --- | --- | --- |
| gitleaks | v8.30.1 | yes | `b40ab0ae55c505963e365f271a8d3846efbc170aa17f2607f13df610a9aeb6a5` |
| osv-scanner | v2.7.1 | yes | `b22c02877a811c8f78956e8643efa554932b7378f590d13f8e97d4e9448a29f3` |
| syft | v1.57.1 | yes | `e0a74a5d2688d1254b4e17b71f068a83215e3625f846265bf6614c7ba06f3a96` |

Official asset and checksum-file URLs are retained in each facts file’s `tool_provenance`. The scanners ran with our own configurations; configuration files inside the inspected projects were not loaded.

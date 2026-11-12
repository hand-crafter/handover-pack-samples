# Handover pack: two samples

A handover pack answers one question for someone selling or buying a small software business:
**what would a new owner be taking on, and what would they need to run it without the founder?**

Each pack has two parts:

- a **buyer answer sheet** (`report.md`): ten questions a buyer asks, each answered from the code with a file and line reference, a red / amber / green table, and the five things to settle before money changes hands;
- a **handover runbook** (`runbook.md`): how to take custody, deploy, back up and restore, and rotate secrets, written from what the repository actually contains, with the gaps marked.

`pack.html` is the same content as one printable page. `facts.json` is the raw evidence the report was written from. `advisory-review.json` lists the dependency advisories that were read by hand.

| Sample | Open | Source inspected |
| --- | --- | --- |
| Linkding (bookmark manager) | [report](linkding/report.md) · [runbook](linkding/runbook.md) · [one page](https://hand-crafter.github.io/handover-pack-samples/linkding/pack.html) | [sissbruecker/linkding](https://github.com/sissbruecker/linkding) at commit `27b7303` |
| Kutt (link shortener) | [report](kutt/report.md) · [runbook](kutt/runbook.md) · [one page](https://hand-crafter.github.io/handover-pack-samples/kutt/pack.html) | [thedevs-network/kutt](https://github.com/thedevs-network/kutt) at commit `029da42` |

## Read this before relying on them

- **Neither project is for sale, and neither asked for this.** Linkding and Kutt are public open-source projects, picked only because their code is open and well kept. Their maintainers did not commission, review or endorse these samples. The "before buying" wording shows the format a real pack uses.
- **Static inspection only.** Nothing from either project was installed, built or run. Of 22 checks, 12 ran, 7 ran in part and 3 did not run (tests, build and deploy, and security and load testing while running). [`coverage.md`](coverage.md) lists every check and its limit.
- **Not a security audit.** Dependency advisories come from the public OSV database and were not tested for whether they can be exploited here. A clean scan is not a clearance.
- **Prepared with automated scanners and AI agents.** No independent human engineer reviewed these two samples. Every statement carries a file and line reference so a reader can check it.
- **Corrections and removal.** If you maintain either project and want something corrected or the sample taken down, write to the address below and it will be done.

## Getting one for your own product

A pack for one repository is a fixed-scope job: you give read access to the code (no production access, no secrets), and get the answer sheet and runbook back.

- Introductory price: **US$49** for each of the first three packs.
- Regular price: US$149 for a seller preparing a sale, US$349 for a buyer checking a business before buying.

Write to **handoverdesk@proton.me**.

## Licence and credit

The text of these samples is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Linkding and Kutt are the work of their authors and are distributed under the MIT licence in their own repositories; file paths and short references here point to that code and do not replace it.

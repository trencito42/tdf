# DollForum Platform Modernization

DollForum is a long-running community platform with roughly 2.9 million forum posts, 119,000 registered accounts and 1.5 million attachments. Its dedicated server also hosts DollHarem, DollAlbum, CoverDoll and several legacy or migration applications.

This repository contains the engineering assessment and modernization plan for that shared environment. The work began after recurring periods of 30–60 second page loads and expanded beyond the main forum because the applications share database, backup, storage, web and operating-system services.

## Start here

Owners and senior administrators should begin with the [owner decision brief](docs/owner-decision-brief.md). It summarizes the evidence, the immediate decisions and the work that can be prepared without changing production.

## Current status

The initial server and application assessment is complete. Targeted verification remains open, including:

- controlled assessment of whether backup artifacts in web-served trees are externally retrievable;
- read-only review of Cloudflare zone configuration and security events;
- an isolated restore test;
- direct validation of the database-lock mechanism during a backup window;
- owner review of privileged forum accounts and legacy-data retention.

No production remediation has been carried out under this project. No application upgrade, database conversion, PHP migration, DNS change, firewall change or reboot is approved by the documents in this repository.

## Main findings

- Both production phpBB forums run versions covered by a vendor-confirmed authentication vulnerability. The assessment found no evidence proving exploitation.
- Backup artifacts containing application configuration material were found in web-served directory trees. Their external accessibility has not been tested.
- Repeated timing evidence links the nightly DollForum slowdown to the MyISAM database backup. Direct lock correlation remains a validation step.
- Active applications depend on unsupported PHP versions and legacy CMS/gallery software.
- Cloudflare proxy coverage is inconsistent across the hosted domains, and the dashboard configuration has not yet been reviewed.
- DollForum and DollHarem are independently customized applications. A blanket upgrade or global PHP change would create avoidable compatibility risk.

## Documentation

- [Owner decision brief](docs/owner-decision-brief.md)
- [Executive summary](docs/executive-summary.md)
- [Platform overview](docs/platform-overview.md)
- [Application inventory](docs/application-inventory.md)
- [Security assessment](docs/security.md)
- [Performance assessment](docs/performance.md)
- [Modernization roadmap](docs/modernization-roadmap.md)
- [Implementation plan](docs/implementation-plan.md)
- [Validation and rollback](docs/validation-and-rollback.md)

## Evidence language

The documents distinguish among confirmed software or configuration state, strong evidence-based conclusions, unverified exposure or impact, and proposed work. Vulnerable software is confirmed; compromise is not.

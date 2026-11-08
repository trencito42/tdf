# DollForum Platform Modernization

DollForum is a long-running community platform with roughly 2.9 million forum posts, 119,000 registered accounts and 1.5 million attachments. Its primary forum shares a dedicated production server with DollHarem, DollAlbum, CoverDoll and several legacy or migration applications.

This repository records the engineering assessment and proposed modernization of that environment. The work began after recurring periods of 30–60 second page loads and expanded into a server-wide review because application, database, backup, storage and runtime changes cannot be evaluated safely in isolation.

## Current position

Assessment basis: production evidence reviewed through 8 November 2026.

The platform is operating, but several risks require prompt decisions:

- Both production phpBB forums run versions covered by a vendor-confirmed authentication vulnerability. No evidence reviewed during the assessment proves exploitation.
- Backup artifacts containing application configuration material were found in publicly served directory trees. External retrieval was not tested; containment is the first recommended production action.
- Active applications depend on PHP 5.6 and PHP 7.4, both outside upstream support.
- The nightly database backup causes a repeatable period of severe DollForum latency because most of its large forum tables still use MyISAM.
- CoverDoll runs Drupal 7 and Gallery 2; DollAlbum and DollHarem run older Coppermine releases.
- Cloudflare traffic paths are inconsistent across the hosted domains, while all sites share one origin.
- The operating system requires a coordinated kernel and core-library maintenance window.

These findings do not support a rushed rewrite. DollForum contains substantial custom moderation, private-message, marketplace, chat, style and attachment behavior. An incremental modernization keeps established URLs, content, accounts and staff workflows while allowing each risk to be tested and reversed independently.

## Recommended sequence

1. Contain potentially exposed backup artifacts.
2. Apply independently tested security containment to both phpBB forums.
3. Prove database and configuration restore procedures in isolation.
4. Upgrade DollHarem and DollForum separately to a current phpBB 3.3 release.
5. Move each application to a supported PHP runtime only after compatibility testing.
6. Update the two Coppermine galleries and plan migrations for Drupal 7 and Gallery 2.
7. Remove the MyISAM backup bottleneck through a rehearsed database and backup program.
8. Standardize Cloudflare and origin controls without breaking other hosted services.
9. Complete shared-host kernel maintenance and retire unused runtimes and legacy applications.
10. Establish latency, backup, capacity and security monitoring suitable for ongoing operations.

Staging, restore validation, Cloudflare review and operating-system preparation can proceed in parallel. Production changes remain separate approval decisions.

## What was examined

The assessment covered:

- all Plesk domains, hosted applications and database assignments;
- nginx, Apache, PHP-FPM, MariaDB, Redis and storage dependencies;
- both phpBB installations, their extensions, themes and custom behavior;
- active Drupal, Gallery 2 and Coppermine installations;
- scheduled work, Plesk backups and recurring slow-request evidence;
- public DNS paths and the limits of the current Cloudflare architecture;
- operating-system lifecycle, disk capacity and shared-service risk.

The review was non-invasive. It did not exploit vulnerabilities, inspect private messages, export user data, alter production configuration or perform load testing.

## Implementation status

**Assessment complete; production remediation not started.**

No application upgrade, database migration, service restart, DNS change, firewall change or reboot described here has been performed. Every production work package requires owner approval, a recorded baseline, validation criteria and a rollback path.

## Documents

- [Executive summary](docs/executive-summary.md)
- [Platform overview](docs/platform-overview.md)
- [Security assessment](docs/security.md)
- [Performance assessment](docs/performance.md)
- [Application inventory](docs/application-inventory.md)
- [Modernization roadmap](docs/modernization-roadmap.md)
- [Implementation plan](docs/implementation-plan.md)
- [Validation and rollback](docs/validation-and-rollback.md)

## Evidence language

The documents use three categories consistently:

- **Confirmed:** supported by configuration, software metadata, database metadata or bounded production logs.
- **Inferred:** the evidence supports a likely explanation, but a controlled test is still required.
- **Open:** access, owner intent or safe production testing is still needed.

This distinction is important: vulnerable software is confirmed, but compromise is not.

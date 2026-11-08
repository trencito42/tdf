# Modernization Roadmap

[Owner decision brief](owner-decision-brief.md) · [Implementation plan](implementation-plan.md) · [Validation and rollback](validation-and-rollback.md)

This roadmap is proposed work. It records dependencies and sequencing; it does not indicate production approval or completion.

The existing server administrator will prepare and execute approved phases. Owners retain approval over priorities, production changes, service interruptions and maintenance windows; no separate technical team is being requested.

## Principles

- Preserve content, accounts, permissions, URLs and staff workflows.
- Treat each application as an independent compatibility problem.
- Separate urgent containment from long-term migration.
- Prove restore before schema or engine changes.
- Measure performance before and after every optimization.
- Keep shared-infrastructure changes in coordinated maintenance windows.
- Remove legacy material only after ownership and retention decisions.

## Phase 1 — Critical security containment

After explicit approval, contain potentially exposed backup artifacts without destroying a unique recovery copy. Determine whether their contents or access history require credential rotation.

Apply the phpBB vendor containment independently to DollForum and DollHarem, or accelerate a staged upgrade when containment side effects cannot be accepted. Validate database login, logout, remembered sessions, moderator access and administration behavior on each forum.

This phase requires small, explicit production approvals. It does not include a database migration, global PHP change or DNS redesign.

## Phase 2 — Staging and compatibility validation

Build isolated staging with:

- restored database copies;
- separate cookie/session names;
- disabled outbound mail, analytics and production monitoring;
- non-indexed access controls;
- representative attachment/media fixtures or read-only references;
- captured application code/configuration artifacts;
- automated smoke tests for critical user and staff workflows.

Perform a real restore rehearsal before using staging as an upgrade safety claim.

The smaller DollHarem forum is a useful rehearsal for deployment mechanics. It is not a compatibility test for DollForum.

## Phase 3 — phpBB and PHP modernization

Upgrade DollHarem and DollForum through separate work packages to the current secure phpBB 3.3 release. Test phpBB on the existing runtime first, then evaluate supported PHP branches independently. This isolates application changes from runtime changes.

For DollForum, port direct core behavior to maintained extensions/events where feasible, but do not remove behavior simply to obtain a clean diff. Functional parity is the first acceptance criterion.

Retire PHP 7.4 only after forums, galleries, CLI jobs and administrative functions have no remaining dependency.

## Phase 4 — Database and backup improvements

After restore capability is proven:

- rehearse DollForum MyISAM-to-InnoDB conversion;
- validate full-text search, storage growth and maintenance duration;
- test candidate composite indexes on production-shaped data;
- select a transaction-consistent or physical backup method appropriate to the final engines;
- verify recovery time and cross-site load.

CoverDoll's mixed-engine Drupal database needs its own analysis. Existing InnoDB applications should not be converted simply for uniformity.

## Phase 5 — Cloudflare and infrastructure hardening

Begin with an approved read-only export of every Cloudflare zone. Standardize TLS and origin expectations per domain only in a later approved package, then address direct-origin bypass when Plesk, mail, staging and operational access are accounted for.

Prepare the current kernel and a controlled reboot with console rollback. Complete disk attribution, log retention and capacity alerting. Do not combine the reboot with an application upgrade unless a tested dependency requires it.

## Phase 6 — Interface, navigation and user experience

UI work follows functional and security stability. Priorities are:

- eliminate parser-blocking third-party dependencies;
- improve responsive navigation and accessibility;
- preserve familiar moderation and posting flows;
- optimize thumbnails and attachment presentation;
- consolidate duplicate JavaScript and template overrides;
- measure Core Web Vitals separately from backend TTFB.

URLs, BBCode rendering, search behavior and permissions remain compatibility contracts.

## Phase 7 — Monitoring and ongoing maintenance

Introduce privacy-safe request/upstream timings, backup-lock correlation, capacity alerts and application error-rate monitoring. Establish routine review for phpBB, PHP, gallery/CMS and operating-system security releases.

Each custom feature should have an owner, source location, supported versions and a small regression test. This turns the next upgrade from a discovery exercise into routine maintenance.

## Parallel work

The following can proceed in parallel without production mutation:

- phpBB extension and style testing;
- restore rehearsal;
- Coppermine staging;
- Drupal/Gallery migration mapping;
- Cloudflare export and traffic analysis;
- kernel/reboot preparation;
- observability design.

Production work remains serialized where it shares MariaDB, storage, DNS or a runtime service.

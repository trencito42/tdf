# Owner Decision Brief

[Project overview](../README.md) · [Implementation plan](implementation-plan.md) · [Application inventory](application-inventory.md)

## Why I performed this assessment

DollForum has experienced recurring periods in which pages take 30–60 seconds to load. As the existing server administrator, I performed the initial technical audit to identify the cause without making changes to a live community platform. I expanded the review when it confirmed that DollForum shares its server, database service, backup system, storage and web stack with DollHarem, DollAlbum, CoverDoll and several older applications.

The completed initial audit covered both phpBB forums, their extensions and custom behavior; the active Drupal, Gallery 2 and Coppermine applications; PHP-FPM, MariaDB, Redis, nginx and Apache; scheduled work and backups; storage placement; public DNS paths; and operating-system maintenance state. Targeted verification remains where the evidence could not safely be completed without access approval or a controlled production observation.

No production remediation has been carried out under this project.

I will lead and carry out the proposed remediation and modernization work. I am not asking the owners to hire outside engineers or appoint another technical team. I am asking them to review the findings, agree on priorities and authorize the scoped production work that requires their approval.

## What was found

The two production forums run phpBB 3.3.7 and 3.3.3. Both versions are within a range for which phpBB issued urgent guidance about an authentication vulnerability that can lead to account takeover. The applicable temporary containment was not present when the systems were reviewed. This confirms exposure to the software issue; it does not establish that an account or server has been compromised.

Large backup artifacts were also found inside web-served directory trees. At least one contains application configuration material. Their location and filesystem permissions are confirmed, but external retrieval was deliberately not tested. They require controlled containment and an exposure assessment before anyone decides whether credential rotation or incident response is necessary.

The nightly DollForum slowdown has strong repeated timing evidence. The main forum database is still dominated by MyISAM tables, and its roughly 70-second backup interval overlaps with hundreds of PHP requests exceeding 20 seconds. The working conclusion is that requests queue behind backup-related database locking. A controlled observation of lock state during a backup should close the remaining evidence gap before the backup method or table engines are changed.

Several active applications depend on software outside normal upstream support: PHP 5.6, PHP 7.4, Drupal 7 and Gallery 2. The two Coppermine galleries are also behind current security releases. Cloudflare is in the traffic path for some domains but not others; dashboard rules, caching, WAF and security events have not yet been reviewed.

These risks are linked by shared infrastructure. A database, PHP, network, storage or operating-system change intended for one site can affect all of them.

## Why this is not a standard upgrade

DollForum has 23 active extensions, custom HOLD moderation and private-message behavior, ProChat, marketplace functions, custom styles and specialized attachment handling. DollHarem has its own extensions, theme and configuration. DollAlbum and DollHarem use separate Coppermine customizations, while CoverDoll depends on Drupal and Gallery 2.

Existing behavior must be preserved unless the owners explicitly approve its removal. Replacing the platform would require rebuilding permissions, millions of posts, attachments, BBCode behavior, search, moderation history and familiar staff workflows before it produced a security benefit. Staged modernization addresses the urgent risks sooner and makes each change testable and reversible.

## How I will prepare the work

With the required access and data-handling approval, I can prepare and test the following without changing production:

- isolated staging with separate sessions and disabled outbound integrations;
- extension, theme and custom-feature compatibility tests;
- a database/configuration restore rehearsal;
- read-only Cloudflare exports and event review;
- migration inventories for Drupal, Gallery 2 and Coppermine;
- kernel/reboot and monitoring runbooks;
- baseline measurements and acceptance tests.

I will document the results and bring any production change, expected interruption and maintenance window back to the owners for the required approval. Preparation and testing do not authorize a production deployment.

## Decisions requested now

I am asking for permission to proceed with the following clearly scoped tasks. The owners can approve each item independently:

### A. Backup artifact containment and exposure assessment

Approve me to record hashes and dependencies, preserve any unique recovery copy, remove the artifacts from web serving or apply a tested deny rule, and perform a controlled accessibility and access-history assessment. If the evidence indicates that credentials must be rotated or an incident response is required, I will present that as a separate scoped action.

### B. Emergency phpBB containment

Approve me to prepare, test and apply separate emergency containment procedures for DollForum and DollHarem. Each forum will have its own original-state capture, compatibility checks, validation and rollback. Approval for one forum does not approve changes to the other and does not authorize the later core upgrade.

### C. Isolated staging and restore validation

Approve me to create access-controlled, isolated copies of application databases and representative media/configuration. I will use them to demonstrate restore, upgrade and rollback procedures without contacting users or changing production.

### D. Read-only Cloudflare review

Approve me to export and review DNS, proxy state, TLS mode, WAF/custom rules, rate limits, bot controls, cache rules and anonymized security events for each available zone. This is a read-only review and does not authorize DNS, WAF or origin changes.

## Later approvals remain separate

phpBB core upgrades, database-engine conversion, PHP migrations, gallery/CMS migrations, DNS or firewall changes, Cloudflare rule deployment and the server reboot each require their own proposal and maintenance approval after staging evidence is available. The owners retain control over priorities, major changes, service interruptions and maintenance windows; I will prepare the technical plan, validation evidence and rollback procedure for each decision.

The recommended immediate sequence is A, B and C, with D proceeding in parallel. The supporting evidence and rollback requirements are detailed in the [security assessment](security.md), [performance assessment](performance.md), [implementation plan](implementation-plan.md) and [validation plan](validation-and-rollback.md).

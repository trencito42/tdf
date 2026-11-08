# Owner Decision Brief

[Project overview](../README.md) · [Implementation plan](implementation-plan.md) · [Application inventory](application-inventory.md)

## Why this assessment was performed

DollForum has experienced recurring periods in which pages take 30–60 seconds to load. The initial work was intended to identify the cause without making changes to a live community platform. It was expanded when the review confirmed that DollForum shares its server, database service, backup system, storage and web stack with DollHarem, DollAlbum, CoverDoll and several older applications.

The assessment covered both phpBB forums, their extensions and custom behavior; the active Drupal, Gallery 2 and Coppermine applications; PHP-FPM, MariaDB, Redis, nginx and Apache; scheduled work and backups; storage placement; public DNS paths; and operating-system maintenance state.

No production remediation has been carried out under this project.

## What was found

The two production forums run phpBB 3.3.7 and 3.3.3. Both versions are within a range for which phpBB issued urgent guidance about an authentication vulnerability that can lead to account takeover. The applicable temporary containment was not present when the systems were reviewed. This confirms exposure to the software issue; it does not establish that an account or server has been compromised.

Large backup artifacts were also found inside web-served directory trees. At least one contains application configuration material. Their location and filesystem permissions are confirmed, but external retrieval was deliberately not tested. They require controlled containment and an exposure assessment before anyone decides whether credential rotation or incident response is necessary.

The nightly DollForum slowdown has strong repeated timing evidence. The main forum database is still dominated by MyISAM tables, and its roughly 70-second backup interval overlaps with hundreds of PHP requests exceeding 20 seconds. The working conclusion is that requests queue behind backup-related database locking. A controlled observation of lock state during a backup should close the remaining evidence gap before the backup method or table engines are changed.

Several active applications depend on software outside normal upstream support: PHP 5.6, PHP 7.4, Drupal 7 and Gallery 2. The two Coppermine galleries are also behind current security releases. Cloudflare is in the traffic path for some domains but not others; dashboard rules, caching, WAF and security events have not yet been reviewed.

These risks are linked by shared infrastructure. A database, PHP, network, storage or operating-system change intended for one site can affect all of them.

## Why this is not a standard upgrade

DollForum has 23 active extensions, custom HOLD moderation and private-message behavior, ProChat, marketplace functions, custom styles and specialized attachment handling. DollHarem has its own extensions, theme and configuration. DollAlbum and DollHarem use separate Coppermine customizations, while CoverDoll depends on Drupal and Gallery 2.

Existing behavior must be preserved unless the owners explicitly approve its removal. Replacing the platform would require rebuilding permissions, millions of posts, attachments, BBCode behavior, search, moderation history and familiar staff workflows before it produced a security benefit. Staged modernization addresses the urgent risks sooner and makes each change testable and reversible.

## Work that can begin without disrupting production

Subject to access and data-handling approval, engineers can prepare:

- isolated staging with separate sessions and disabled outbound integrations;
- extension, theme and custom-feature compatibility tests;
- a database/configuration restore rehearsal;
- read-only Cloudflare exports and event review;
- migration inventories for Drupal, Gallery 2 and Coppermine;
- kernel/reboot and monitoring runbooks;
- baseline measurements and acceptance tests.

These activities do not authorize a production deployment.

## Decisions requested now

The owners can approve the following independently:

### A. Backup artifact containment and exposure assessment

Authorize engineers to record hashes and dependencies, remove the artifacts from web serving or apply a tested deny rule, and perform a controlled accessibility/access-history assessment. The work must preserve any unique recovery copy. Credential rotation, if required, would be handled as a separate scoped action.

### B. Emergency phpBB containment

Authorize separate containment procedures for DollForum and DollHarem, each with its own original-state capture, compatibility checks, validation and rollback. Approval for one forum does not approve changes to the other and does not authorize the later core upgrade.

### C. Isolated staging and restore validation

Authorize isolated copies of application databases and representative media/configuration so that restore, upgrade and rollback procedures can be demonstrated without contacting users or changing production.

### D. Read-only Cloudflare review

Authorize export and review of DNS, proxy state, TLS mode, WAF/custom rules, rate limits, bot controls, cache rules and anonymized security events for each available zone. This approval does not authorize DNS, WAF or origin changes.

## Later approvals remain separate

phpBB core upgrades, database-engine conversion, PHP migrations, gallery/CMS migrations, DNS or firewall changes, Cloudflare rule deployment and the server reboot each require their own proposal and maintenance approval after staging evidence is available.

The recommended immediate sequence is A, B and C, with D proceeding in parallel. The supporting evidence and rollback requirements are detailed in the [security assessment](security.md), [performance assessment](performance.md), [implementation plan](implementation-plan.md) and [validation plan](validation-and-rollback.md).

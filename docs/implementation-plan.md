# Implementation Plan

[Project overview](../README.md) · [Roadmap](modernization-roadmap.md) · [Validation and rollback](validation-and-rollback.md)

Every package is currently **proposed**. None has production approval or implementation status.

## Work package summary

| Package | Priority | Expected production disruption | Staging requirement | Status |
|---|---|---|---|---|
| WP-01 Backup artifact containment | Critical decision | None or brief routing validation | Recommended for rule validation | Proposed |
| WP-02A/B phpBB containment | Critical | Brief per-forum change window | Strongly recommended | Proposed |
| WP-03 Staging and restore proof | High | None | This package creates staging | Proposed |
| WP-04 DollHarem phpBB upgrade | Critical/high | Maintenance window | Required | Proposed |
| WP-05 DollForum phpBB upgrade | Critical/high | Dedicated maintenance window | Required | Proposed |
| WP-06A/B Coppermine updates | High | Short separate gallery windows | Required | Proposed |
| WP-07 Drupal migration | High | Planned cutover window | Required | Proposed |
| WP-08 Gallery 2 migration | High | Planned cutover window | Required | Proposed |
| WP-09 Database/backup modernization | High | Database maintenance window | Required with production-shaped data | Proposed |
| WP-10 Cloudflare/origin controls | High | Staged DNS/rule changes | Configuration testing required | Proposed |
| WP-11 Operating-system maintenance | High | Full-server reboot window | Runbook rehearsal required | Proposed |
| WP-12 Observability/capacity/cleanup | Medium/high | Mostly online; reloads or small task windows | Recommended for configuration changes | Proposed |

## WP-01 — Backup artifact containment

- **Problem:** backup artifacts containing configuration material reside inside served directory trees.
- **Scope:** affected production/test vhosts and any credentials present in the artifacts.
- **Proposed change:** record hashes, ownership and retention intent; move artifacts to approved non-web storage or add a tested explicit deny rule; review access history.
- **Dependencies:** owner confirmation that the files are not the only recoverable copies; known backup/migration consumers.
- **Expected outcome:** artifacts are not retrievable through the web tier.
- **Main risk:** breaking an undocumented migration or backup workflow; deleting unique recovery data.
- **Validation:** configuration syntax, local routing evaluation, approved external reachability check and application smoke tests.
- **Rollback:** restore the exact storage location or rule only if a real dependency is demonstrated; do not restore public exposure for convenience.
- **Approval:** production file-handling/security approval.

## WP-02A / WP-02B — phpBB security containment

- **Problem:** both forums run versions in the vendor-confirmed authentication vulnerability range.
- **Scope:** DollForum and DollHarem as independent sub-packages.
- **Proposed change:** apply vendor containment with captured original files/cache state, or perform the staged upgrade if it can be completed promptly.
- **Dependencies:** authentication-provider inventory, cache-specific procedure and owner test accounts.
- **Expected outcome:** the documented vulnerable provider path is unavailable pending upgrade.
- **Main risk:** vendor-documented administration side effects or custom authentication assumptions.
- **Validation:** login/logout, remembered sessions, account administration, moderator and administrator access.
- **Rollback:** restore exact files and cache state if acceptance criteria fail.
- **Approval:** explicit approval for each forum.

## WP-03 — Isolated staging and restore proof

- **Problem:** production change risk is high and backup creation has not been proven through restoration.
- **Scope:** application databases, code/configuration artifacts and representative media.
- **Proposed change:** create access-controlled staging with disabled external side effects; restore databases and configuration; document recovery time and missing dependencies.
- **Dependencies:** sufficient isolated storage, mail suppression and separate sessions/cookies.
- **Expected outcome:** repeatable staging and a documented restore procedure.
- **Main risk:** staging accidentally contacting users or sharing production session/integration state.
- **Validation:** data counts, schema/application version, login with test accounts, read-only media fixtures and no outbound production integrations.
- **Rollback:** destroy only the isolated staging copy; production remains untouched.
- **Approval:** staging resource/data-handling approval; no production change implied.

## WP-04 — DollHarem phpBB upgrade

- **Problem:** phpBB 3.3.3 and PHP 7.4 are unsupported/insecure.
- **Scope:** DollHarem forum, three extensions, custom style and attachment behavior.
- **Proposed change:** upgrade staged core to the current secure 3.3 release; validate/replace the thumbnail extension; test a supported PHP branch separately.
- **Dependencies:** WP-03 and WP-02B; custom extension/style regression suite.
- **Expected outcome:** supported forum release with preserved registration, anti-spam, advertising and style behavior.
- **Main risk:** extension or template incompatibility.
- **Validation:** complete forum workflow and cross-site resource baseline.
- **Rollback:** atomic code/config/database restoration; no partial schema downgrade.
- **Approval:** maintenance-window approval.

## WP-05 — DollForum phpBB upgrade

- **Problem:** phpBB 3.3.7, unsupported PHP and a large custom compatibility surface.
- **Scope:** 23 extensions, three styles, HOLD/PM behavior, ProChat, marketplace, search and attachments.
- **Proposed change:** three-way source reconciliation, current phpBB 3.3 upgrade, extension/style remediation and separate supported-PHP validation.
- **Dependencies:** WP-03 and WP-02A; owner-approved behavior catalogue.
- **Expected outcome:** secure phpBB/PHP platform without loss of community or staff functionality.
- **Main risk:** regression in moderation, private messaging, permissions, attachments or custom integrations.
- **Validation:** full functional suite, representative performance baseline and post-change logs.
- **Rollback:** atomic database and code/config restore before reopening writes.
- **Approval:** dedicated maintenance-window approval.

## WP-06A / WP-06B — Coppermine updates

- **Problem:** DollAlbum and DollHarem galleries run Coppermine 1.6.10 and PHP 7.4.
- **Scope:** separate code, database, theme, plugin and media paths for each gallery.
- **Proposed change:** update to a current secured 1.6.x release and test a supported PHP branch.
- **Dependencies:** WP-03; media fixture/read-only strategy.
- **Expected outcome:** supported gallery code with preserved media and upload workflows.
- **Main risk:** custom theme/plugin or image-processing incompatibility.
- **Validation:** authentication, permissions, albums, upload, thumbnails and representative media.
- **Rollback:** restore code/config/database; avoid modifying original media during rehearsal.
- **Approval:** separate approval for each gallery.

## WP-07 — CoverDoll Drupal migration

- **Problem:** Drupal 7 and PHP 5.6 are end-of-life.
- **Scope:** 55 active modules, themes, content, accounts, roles, files and URLs.
- **Proposed change:** decide temporary extended support versus accelerated migration; build a supported Drupal replacement and rehearsed cutover.
- **Dependencies:** module/content model and owner workflow mapping.
- **Expected outcome:** maintained CMS with equivalent content and access behavior.
- **Main risk:** data, permissions, editorial workflow or URL loss.
- **Validation:** content/role counts, editorial acceptance, redirects, search and file access.
- **Rollback:** retain the restricted legacy application and database until acceptance.
- **Approval:** design, staging and cutover approvals.

## WP-08 — Gallery 2 migration

- **Problem:** active legacy Gallery 2 depends on PHP 5.6.
- **Scope:** database, media, metadata, permissions and active modules/themes.
- **Proposed change:** migrate to a maintained gallery platform with stable item mapping and redirects.
- **Dependencies:** media inventory, owner requirements and WP-03.
- **Expected outcome:** maintained gallery without loss of media or references.
- **Main risk:** metadata/permission loss, broken links or excessive storage duplication.
- **Validation:** item/file counts, sampled hashes, metadata, permissions and redirects.
- **Rollback:** switch traffic back to the preserved restricted legacy application.
- **Approval:** migration and cutover approvals.

## WP-09 — Database and backup modernization

- **Problem:** DollForum MyISAM tables cause backup locks; restore capability is unproven.
- **Scope:** forum engines, full-text search, candidate indexes and server-wide backup method.
- **Proposed change:** rehearse InnoDB conversion, measure storage/time, validate search, then select a transaction-consistent or physical backup approach.
- **Dependencies:** WP-03 and application upgrades where schema compatibility matters.
- **Expected outcome:** backups without user-facing lock queues and a tested recovery path.
- **Main risk:** long maintenance, storage expansion, search/index regression or recovery failure.
- **Validation:** restore test, query plans, lock counters, next-backup observation and cross-site latency.
- **Rollback:** restore the pre-change database before reopening writes; later rollback requires data reconciliation.
- **Approval:** database maintenance-window approval.

## WP-10 — Cloudflare and origin controls

- **Problem:** proxy status and security policy differ by domain; the shared origin remains discoverable.
- **Scope:** all zones, origin services and operational access paths.
- **Proposed change:** export configuration; define per-zone proxy/TLS/WAF/cache policy; add origin authentication/restriction only after dependency mapping.
- **Dependencies:** zone access, mail/Plesk/staging requirements and prior rule export.
- **Expected outcome:** deliberate, documented edge policy and reduced origin bypass.
- **Main risk:** blocking legitimate users, search engines, downloads or administrative services.
- **Validation:** legitimate route matrix, attachment downloads, crawler behavior and direct-origin tests.
- **Rollback:** prior rule export, DNS TTL plan and preserved management path.
- **Approval:** Cloudflare and shared-network approval.

## WP-11 — Operating-system maintenance

- **Problem:** older kernel, reboot-required core libraries and long uptime.
- **Scope:** the entire server and both storage devices.
- **Proposed change:** install the current approved kernel, verify boot fallback and perform one controlled reboot.
- **Dependencies:** console access, service inventory, backup health and cross-site acceptance checklist.
- **Expected outcome:** current kernel/core libraries and a proven restart sequence.
- **Main risk:** boot, mount, network or service-start failure affecting every site.
- **Validation:** kernel, mounts, MariaDB, Redis, PHP services, web/mail/DNS and every hosted application.
- **Rollback:** boot the previous kernel through console and restore only independently changed configuration.
- **Approval:** shared-host maintenance-window approval.

## WP-12 — Observability, capacity and legacy cleanup

- **Problem:** request timing is incomplete, root capacity is high and ownership of legacy material is unclear.
- **Scope:** privacy-safe logs/metrics, retention, staging/legacy paths and scheduled work.
- **Proposed change:** add request/upstream timing; alert on capacity and backup degradation; deduplicate phpBB cron; restrict/archive approved legacy material; right-size unused pools.
- **Dependencies:** owner retention decisions and configuration-reload approval.
- **Expected outcome:** earlier incident detection and reduced unnecessary attack/resource surface.
- **Main risk:** logging sensitive data, removing required history or suppressing scheduled work.
- **Validation:** privacy review, alert tests, 24-hour scheduled-task observation and recoverable archives.
- **Rollback:** restore previous log format, cron entry, pool configuration or archived path.
- **Approval:** separate observability, cron and retirement approvals.

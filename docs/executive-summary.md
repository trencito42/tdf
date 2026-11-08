# Executive Summary

[Owner decision brief](owner-decision-brief.md) · [Roadmap](modernization-roadmap.md) · [Implementation plan](implementation-plan.md)

## Operational position

DollForum remains a viable platform with valuable content, established community workflows and extensive custom functionality. The immediate problem is not insufficient server capacity. Short production samples showed ample CPU and memory headroom. The principal risks come from unsupported software, shared-service coupling, a locking database backup and years of accumulated application-specific changes.

The dedicated server hosts more than the main forum. DollHarem has a second phpBB forum and a gallery; DollAlbum has a large gallery; CoverDoll runs Drupal and Gallery 2. These applications share the web stack, database service, storage, backup system and operating system. A global runtime, database or network change intended for one site can therefore interrupt another.

The initial inventory and assessment are complete. Cloudflare dashboard review, external artifact accessibility, isolated restore testing and direct backup-lock observation remain open. No production remediation has been performed under this project.

## Security position

The two production forums run phpBB 3.3.7 and 3.3.3. Both fall within a vulnerability range for which phpBB issued urgent guidance. The applicable temporary mitigation was not present at the time of review. Database authentication is selected, which narrows the active configuration, but does not remove the need for containment and upgrade.

The assessment found no evidence proving account takeover or server compromise. That distinction must be preserved. A focused owner review of privileged accounts and authentication history is still required.

A second urgent concern is the presence of large backup artifacts inside publicly served directory trees, including application configuration material. Their filesystem placement and permissions are confirmed; external retrieval was deliberately not tested. They should be contained through a controlled production change, followed by an assessment of whether any embedded credentials require rotation.

Active PHP 5.6 and 7.4 runtimes are outside upstream support. Drupal 7 is also beyond community security support. These lifecycle risks cannot be solved safely by changing a global PHP handler because the applications have different compatibility requirements.

## Performance position

The recurring nightly slowdown has strong, repeated timing evidence. DollForum's main database is approximately 7 GiB and is dominated by MyISAM tables. During the nightly Plesk database dump, hundreds of PHP requests exceed the configured 20-second slow threshold. The same overlap appears across multiple nights. This strongly supports backup-related table locking as the cause, but lock state should still be captured directly before changing the backup method or storage engines.

PHP-FPM is not the initiating bottleneck in that incident. Workers accumulate while waiting on MariaDB. Increasing worker counts would allow a larger queue and consume more memory without removing the lock.

There are additional application-level inefficiencies: representative forum queries require filesorts, scheduled phpBB work is duplicated, and the primary DollForum style still loads a monitoring dependency synchronously. These are real issues, but they do not explain the nightly outage as strongly as the backup evidence does.

## Technical debt and shared-server risk

The environment contains:

- two independently customized phpBB installations;
- two Coppermine galleries with different themes, plugins and media placement;
- an end-of-life Drupal application with 55 active modules;
- a Gallery 2 installation with a large extension/theme surface;
- staging, migration and legacy trees whose retention intent is not yet documented;
- two storage devices with application data split across them;
- a long-running operating system that needs coordinated kernel maintenance.

This is manageable, but only if application-specific work is separated from shared-infrastructure work.

## Community and business impact

The current risks affect owners and users in practical ways:

- unresolved authentication exposure threatens trust in staff and member accounts;
- nightly stalls make the forum appear unavailable and create repeated support incidents;
- unsupported applications increase the chance that a future security issue has no straightforward patch;
- weak restore evidence raises the consequence of a failed upgrade, storage problem or accidental deletion;
- uncoordinated server changes could interrupt multiple communities at once;
- loss of custom moderation or attachment behavior would be operationally more damaging than a delayed visual refresh.

## Investment priorities

The first investment should be controlled security containment and staging, not interface redesign. The second should be proven restore capability and application upgrades. Database and backup modernization follows once rollback is credible. Cloudflare, kernel maintenance and observability should then be coordinated across the whole server.

Modernizing the current forum is more practical than rebuilding it. A rewrite would have to reproduce permissions, millions of posts, attachments, BBCode behavior, search, private messaging, moderation history, custom HOLD handling, marketplace features and familiar staff workflows before it delivered any security benefit. Incremental modernization addresses verified risks sooner and keeps each change measurable.

## Decision required

Owners should consider four initial approvals independently:

1. controlled containment and exposure assessment for backup artifacts in served directories;
2. separate emergency security containment for each phpBB forum;
3. construction of isolated staging and a restore test;
4. read-only Cloudflare configuration and event review.

Approval of these items does not authorize application upgrades, database conversion, PHP migration, DNS or firewall changes, Cloudflare rule deployment or a reboot.

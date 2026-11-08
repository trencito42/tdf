# Performance Assessment

[Owner decision brief](owner-decision-brief.md) · [Platform overview](platform-overview.md) · [Validation and rollback](validation-and-rollback.md)

## Strong evidence for the nightly incident mechanism

The recurring severe slowdown around the nightly backup has a repeatable evidence chain:

1. Plesk starts the DollForum database dump.
2. The dump operates on a schema where 72 significant tables use MyISAM.
3. MyISAM backup consistency relies on table locking rather than an InnoDB transaction.
4. PHP-FPM slow logs record hundreds of requests exceeding 20 seconds during the same interval.
5. Slow stacks terminate in database calls rather than CPU-heavy PHP code.

On the reviewed night, the DollForum dump occupied roughly 70 seconds and overlapped with approximately 300 slow PHP requests. Similar timing was present on earlier days.

This supports a high-confidence working conclusion: the backup is the initiating event and PHP-FPM is the waiting queue. A controlled capture of table-lock state during the backup remains necessary to demonstrate the lock path directly.

## What the evidence does not show

Short samples did not show sustained CPU exhaustion, memory pressure, swap activity or a current InnoDB row-lock incident. Recent bounded access-log samples did not show a production 5xx burst. Historical counters are not proof of current saturation.

Increasing PHP-FPM capacity or MariaDB connection limits would not remove a table lock. It could increase the number of concurrent requests waiting behind it.

## Database observations

DollForum's database is approximately 7 GiB, with most data and indexes in MyISAM. Historical MariaDB counters include millions of table-lock waits and a large slow-query record over the server's long uptime.

Representative plans showed filesorts for:

- forum topic listings ordered by topic type and last-post time;
- post listings ordered by post time and post identifier;
- sorting introduced by a topic-hierarchy extension.

These may justify composite indexes, but only after testing on a restored production-shaped database. An index added directly to production could create a long table lock and additional storage pressure.

## Application and frontend contributors

- phpBB scheduled work is defined twice at the same five-minute interval. phpBB locking may serialize it, but the duplicate adds process/database work and obscures ownership.
- The main DollForum style loads the external monitoring SDK synchronously. The Aero style already loads it asynchronously. A slow monitoring endpoint can therefore delay rendering for users of the main style.
- The existing access-log format records status and bytes but not request or upstream duration. Browser time, edge time, web-server time and PHP/database time cannot be separated reliably for ordinary traffic.

These findings matter, but they should remain separate from the proven backup incident.

## Shared-server implications

The daily backup processes all hosted domains. Table locks apply to the affected tables, while MariaDB scheduling, CPU and storage I/O are shared. CoverDoll's active Drupal schema also contains many MyISAM tables and requires its own backup-window observation.

DollForum attachments and DollAlbum media share the data filesystem. MariaDB, logs, dumps and DollHarem content share the root filesystem. Storage work should therefore be measured for cross-site impact even when the application databases are separate.

## Required baselines

Before claiming improvement, record:

- p50, p95 and p99 request and upstream duration by route class;
- 4xx/5xx rates and PHP slow requests;
- MariaDB running threads, table-lock waits and query latency;
- PHP-FPM active/idle workers and max-children events;
- backup phase timing and restore duration;
- root/data filesystem capacity and latency;
- Redis and OPcache usage;
- browser rendering and Core Web Vitals for primary styles.

Query strings, cookies and personal identifiers should not be added to performance logs.

## Acceptance targets

Targets should be set after the first clean baseline. Initial acceptance criteria are behavioral:

- the next backup completes without a corresponding PHP slow-stack burst or material lock queue;
- forum login, posting, moderation, search and attachment delivery remain correct;
- no hosted site shows a new error or latency regression;
- frontend rendering remains usable when the monitoring endpoint is delayed or unavailable;
- database changes produce a measurable plan/latency improvement on representative queries;
- backup artifacts can be restored, not merely created.

## Remediation direction

No database or backup change has been made under this project. The immediate response must preserve recoverability. The proposed path is:

1. prove restore procedures;
2. rehearse MyISAM-to-InnoDB conversion and full-text behavior;
3. measure storage expansion and maintenance duration;
4. move to transaction-consistent or physical backup methods appropriate to the final engines;
5. deploy candidate indexes only after restored-data testing.

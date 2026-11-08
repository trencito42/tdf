# Validation and Rollback

[Project overview](../README.md) · [Implementation plan](implementation-plan.md) · [Performance](performance.md)

## Change discipline

Every production batch should have:

1. an exact scope and owner approval;
2. a captured pre-change state;
3. a tested backup or configuration artifact;
4. objective acceptance criteria;
5. a named rollback decision point;
6. a cross-site validation checklist;
7. an implementation record and result.

Unexpected schema, extension, storage or traffic-path conditions stop the batch. They are not reasons to improvise additional production changes.

## Staging controls

Staging must not share production side effects. It requires:

- separate hostname and access control;
- separate cookie names and sessions;
- suppressed or redirected outbound email;
- disabled analytics, monitoring and webhooks unless explicitly test-scoped;
- no production OAuth or API credentials;
- no public indexing;
- representative attachment/media fixtures or read-only references;
- an explicit cron policy.

Large media stores do not need to be duplicated in full to test application behavior. Database metadata, path semantics and representative files do need to match reality.

## Functional compatibility suite

### Both phpBB forums

- anonymous and authenticated navigation;
- registration where enabled;
- login, logout, remembered login and password reset;
- posting, editing, quoting and BBCode;
- private messages and notifications;
- search and pagination;
- permissions, groups and moderator/admin access;
- attachment upload, thumbnail, download and accelerated delivery;
- scheduled tasks and session cleanup;
- all active styles at desktop/mobile widths.

### DollForum custom behavior

- HOLD moderation and private-message workflows;
- MCP queue restrictions and direct permission checks;
- ProChat and online-user generation;
- marketplace, thanks, mentions, topic hierarchy and report links;
- registration agreement and terms;
- attachment template changes and existing URLs.

### DollHarem custom behavior

- custom registration defaults and notifications;
- Stop Forum Spam integration;
- advertisement placement by forum/search context;
- custom rules, terms and style pages;
- thumbnail behavior.

### Galleries and Drupal

- authentication and role/permission boundaries;
- album/content navigation and search;
- upload validation and quota behavior;
- image processing, thumbnails and metadata;
- custom themes/plugins/modules;
- file/media mapping and sampled hashes;
- canonical URLs and redirects;
- administrative/editorial workflows.

## Security validation

- Confirm the targeted vulnerable condition is no longer present without publishing exploit details.
- Review privileged accounts and direct permissions with owners.
- Verify session and cookie attributes over HTTPS.
- Confirm sensitive artifacts are outside served paths or explicitly denied.
- Check source/config permissions and prevent secrets from entering logs or repositories.
- Validate Cloudflare rules against legitimate users, staff, crawlers and downloads.
- Review application/error logs for new authentication or permission failures.

## Performance validation

Compare the same route classes and time windows before and after change:

- p50/p95/p99 request and upstream duration;
- PHP slow-request count and worker occupancy;
- MariaDB running threads, lock waits and query latency;
- HTTP status/error rates;
- backup phase duration and user-facing impact;
- storage capacity and latency;
- frontend rendering with third-party dependencies available and blocked.

A faster synthetic health page is not evidence that phpBB rendering, search or attachment delivery improved.

## Backup and restore validation

A successful backup job is only the beginning. A restore test should prove:

- archive readability and checksum verification;
- database/schema/application version alignment;
- configuration recovery without exposing secrets;
- attachment/media path reconstruction;
- application startup in isolation;
- representative authentication and content access;
- measured recovery time and required manual steps.

Restores must not send email, run production cron or contact live integrations.

## Cross-site regression checks

After any shared-service change, verify:

- DollForum forum and attachments;
- DollHarem forum and gallery;
- DollAlbum gallery;
- CoverDoll CMS and gallery;
- required staging/forwarding behavior;
- MariaDB and Redis health;
- every required PHP service and pool;
- both storage mounts;
- mail, DNS, certificates and Plesk access where in scope.

Record response status and representative functional results without storing user data.

## Rollback criteria

Rollback should begin when any of the following is true:

- authentication, permissions or staff access fails;
- posts, messages, attachments or media become unavailable or inconsistent;
- schema migration fails or an unexpected migration runs;
- error rates or latency materially exceed the agreed baseline;
- another hosted site regresses;
- storage growth threatens safe operating headroom;
- backup/restore verification fails;
- the planned rollback window is about to close.

Rollback is not always a file copy. After a database migration or reopening writes, restoring an older database can discard new user activity. The change plan must therefore define whether writes remain closed until acceptance and how reconciliation would work.

## Completion record

For each package, record:

- approval and maintenance window;
- original versions/configuration hashes;
- backup/restore evidence;
- exact changes applied;
- validation results and metric comparison;
- cross-site checks;
- rollback actions, if any;
- remaining follow-up work.

Until this record exists and acceptance criteria pass, the package remains in progress rather than complete.

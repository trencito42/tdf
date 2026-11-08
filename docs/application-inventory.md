# Application Inventory

[Project overview](../README.md) · [Platform overview](platform-overview.md) · [Roadmap](modernization-roadmap.md)

## Production applications

| Application | Deployed stack | Maintenance position | Important customization | Principal risk |
|---|---|---|---|---|
| DollForum | phpBB 3.3.7, PHP 7.4, Redis, MariaDB/MyISAM | Active production | 23 extensions, HOLD moderation and PM behavior, ProChat, marketplace, custom styles and attachment handling | Authentication exposure; upgrade complexity; backup locking |
| DollHarem forum | phpBB 3.3.3, PHP 7.4, file cache, MariaDB/InnoDB | Active production | Custom advertising/registration extension, custom style, anti-spam integration | Authentication exposure; incompatible extension declaration |
| DollHarem gallery | Coppermine 1.6.10, PHP 7.4, MariaDB/InnoDB | Active production | Custom theme and three plugins | Behind upstream security releases; unsupported PHP |
| DollAlbum | Coppermine 1.6.10, PHP 7.4, MariaDB/InnoDB | Active production | Custom theme, upload plugins, large media library | Behind upstream security releases; shared media storage |
| CoverDoll CMS | Drupal 7.102, PHP 5.6, mixed-engine MariaDB | Active production | 55 enabled modules and two themes | Drupal/PHP end of life; migration complexity |
| CoverDoll gallery | Gallery 2, PHP 5.6, MariaDB | Active legacy production | 29 active modules and nine themes | Obsolete application/runtime; data preservation |

## DollForum compatibility surface

DollForum cannot be upgraded as a stock phpBB installation. Compatibility must be demonstrated for:

- custom HOLD moderation and private-message behavior;
- MCP queue behavior and permissions;
- ProChat and its generated online-user data;
- marketplace and report-link functions;
- thanks, mentions, topic hierarchy and search extensions;
- custom registration and terms behavior;
- three active styles and template overrides;
- attachment display, thumbnailing and accelerated delivery;
- existing URLs, BBCode, search index and notifications.

Several extensions declare phpBB ranges that exclude the current 3.3 branch, while another requires a newer phpBB than production currently provides. Local extensions also contain incomplete dependency metadata. Runtime behavior, not metadata alone, is the acceptance standard.

## DollHarem compatibility surface

DollHarem has fewer extensions and an unmodified official core, making it a lower-complexity upgrade project. It is still independent from DollForum.

The custom extension controls advertisement fragments and registration notification defaults. The active style includes custom rules, terms and visual content. The thumbnail extension declares support only below phpBB 3.3, so it needs replacement or validation before upgrade.

## Gallery applications

The two Coppermine installations use the same deployed version but different themes, plugins and storage placement. They should receive separate staging and production work packages.

Required tests include authentication, albums, upload, thumbnail generation, image processing, permissions, plugin behavior, media paths and rollback without modifying original media.

CoverDoll's Gallery 2 installation is a migration candidate. Its database and media should be treated as records to preserve. A modern replacement must demonstrate stable item mapping, metadata, permissions and redirects before cutover.

## CoverDoll Drupal

The active Drupal 7 site uses 55 modules covering forums/content, private messaging, invitations, CAPTCHA, media/editor integration, search and XML sitemaps. This is not an in-place major-version update.

The owners need to choose between temporary commercial Drupal 7 extended support and an accelerated migration. Either route requires module provenance, content types, users/roles, files, URLs, redirects and editorial workflows to be mapped first.

## Staging and legacy estate

The server also contains:

- `test.coverdoll.com`, with Drupal, Gallery 2 and Piwigo data;
- `updated.coverdoll.com`, with Gallery 3 migration code copies;
- `t5.dollforum.com`, a parked/static subdomain receiving automated probes;
- `matrix.dollforum.com`, a forwarding object with no public address observed during the assessment;
- old unconfigured phpBB code trees;
- an inactive phpBB 3.0-era database;
- historical Drupal, gallery, advertising, counter and chat data.

These are not automatically safe to delete. Each requires an owner, retention purpose, last-use evidence, backup location and recoverability decision. Public access should be restricted before long-term archival or removal.

## Shared dependencies

- All database-backed applications share MariaDB.
- DollForum, DollHarem and DollAlbum share the PHP 7.4 service.
- CoverDoll production depends on PHP 5.6.
- DollForum and DollAlbum share the data filesystem.
- MariaDB, Plesk backups, logs and DollHarem content share the root filesystem.
- Every application depends on the same nginx, Apache, network and operating system.

These dependencies are why application upgrades and infrastructure maintenance appear as separate work packages in the [implementation plan](implementation-plan.md).

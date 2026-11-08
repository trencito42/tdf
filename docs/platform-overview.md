# Platform Overview

[Project overview](../README.md) · [Application inventory](application-inventory.md) · [Performance](performance.md)

## Hosting model

The platform runs on a dedicated AlmaLinux 9 server managed through Plesk. It has 96 logical CPU cores, approximately 125 GiB of memory and two ext4 storage devices. The system is not currently constrained by CPU or available memory during ordinary samples.

The common request path is:

`DNS or Cloudflare -> nginx -> Apache -> per-site PHP handler -> MariaDB and application storage`

nginx and Apache are shared by every hosted domain. PHP-FPM pools are separated by vhost, but applications using the same PHP branch share its service. MariaDB, the backup system, the operating system and network stack are common to all sites.

## Runtime allocation

| Runtime | Production consumers | Operational consequence |
|---|---|---|
| PHP 5.6 FPM | CoverDoll Drupal and Gallery 2 | Unsupported runtime; cannot be retired until both applications are migrated |
| PHP 7.4 FPM | DollForum, DollHarem, DollAlbum and a parked subdomain | Unsupported runtime shared by unrelated forums and galleries |
| PHP 8.2 | CoverDoll test and migration hosts | Suitable for current staging only in the short term; not a long-lived modernization target |

DollForum has a large PHP-FPM ceiling intended for high traffic. A parked subdomain currently carries a similarly large pool configuration despite having no demonstrated application workload. This is not causing the observed incident, but it should be right-sized after owner intent is confirmed.

## Data services

MariaDB 10.5 serves every database-backed application. Schemas are separate, but the database process, memory, I/O and maintenance events are shared.

DollForum uses Redis for phpBB cache. DollHarem uses phpBB's file cache. No evidence supports treating Redis as a universal dependency for the other hosted applications.

The most important storage-engine difference is between the forums:

- DollForum: 72 MyISAM tables and four InnoDB tables, approximately 7 GiB.
- DollHarem: 72 InnoDB tables, approximately 108 MiB.

This difference changes backup and migration behavior. A safe database procedure for DollHarem is not automatically safe for DollForum.

## Storage placement

The root filesystem holds the operating system, MariaDB, Plesk backups, logs, application code and DollHarem content. It was 85% occupied during the review.

A second data filesystem holds DollForum attachments and DollAlbum media. These applications are logically separate but share the same storage failure domain.

Plesk's accounting indicates the following approximate application footprints:

| Domain | Recorded size |
|---|---:|
| DollForum | 3.36 TB |
| DollAlbum | 203 GB |
| CoverDoll | 134 GB |
| CoverDoll test environment | 86 GB |
| DollHarem | 20 GB |

These are control-panel accounting values, not the result of a new recursive scan.

## Backup model

One daily Plesk backup processes all domains, databases, mail/configuration data and selected content. The latest reviewed run completed successfully and Plesk validated its generated artifacts. That is useful evidence of backup creation, but it is not evidence that a complete application can be restored.

The backup's database phase processes applications sequentially. DollForum's MyISAM dump is the confirmed source of its nightly lock queue. Other applications still share the database and storage load generated during the overall run.

## Network and Cloudflare

CoverDoll, DollAlbum and DollHarem currently resolve through Cloudflare edge addresses. DollForum and two secondary hosts resolve directly to the shared origin. This means:

- Cloudflare protection is not uniform across the platform;
- a proxied zone must not be assumed to have the same WAF, cache or bot policy as another;
- direct records disclose the common origin, limiting the protection of proxied sites unless origin access is designed server-wide.

Cloudflare dashboard configuration was not available during the assessment. DNS observations alone do not establish WAF or rate-limit behavior.

## Shared change boundaries

The following require server-wide planning:

- MariaDB restart or global configuration change;
- nginx or Apache shared configuration reload;
- PHP service restart affecting multiple applications;
- changes to backup concurrency, compression or retention;
- root-filesystem cleanup or storage relocation;
- origin firewall or Cloudflare-only access controls;
- kernel installation and reboot.

Application code, extension and theme upgrades should remain separate work packages even when they use the same runtime.

# Security Assessment

[Owner decision brief](owner-decision-brief.md) · [Implementation plan](implementation-plan.md) · [Validation and rollback](validation-and-rollback.md)

## Assessment boundary

The review examined deployed versions, non-secret configuration, permissions, application metadata, bounded logs and official security guidance. It did not attempt exploitation, inspect private messages, export user records or scan multi-terabyte upload trees.

No finding in this document establishes that the platform has been compromised.

No security remediation described here has been applied to production under this project.

## Priority findings

### S1 — Potential disclosure through web-served backup artifacts

**Status:** confirmed placement; external retrieval open.

Large backup artifacts exist within web-served directory trees. One archive includes application configuration material. The vhost review did not identify a specific archive-deny rule. External accessibility remains unverified because no HTTP retrieval test was performed.

Operational consequence: if an archive is externally retrievable, it may disclose source, configuration and credentials. Containment should preserve any unique recovery copy, record hashes and determine whether an undocumented process depends on the current location. Exposure assessment should determine whether credential rotation or incident response is necessary.

### S2 — phpBB authentication vulnerability

**Status:** confirmed affected versions; exploitation not established.

DollForum runs phpBB 3.3.7 and DollHarem runs phpBB 3.3.3. phpBB identifies versions 3.1.0 through 3.3.16 as affected by an authentication bypass capable of account hijacking. The vendor's temporary containment state was not present in either installation.

Both sites select database authentication. This reduces their reliance on optional authentication integrations but does not supersede the vendor guidance.

Each forum requires an independent containment and upgrade test. DollForum uses Redis and extensive custom code; DollHarem uses file cache and different extensions. A single shared patch artifact would create unnecessary risk.

References: [phpBB urgent guidance](https://www.phpbb.com/community/viewtopic.php?t=2672425), [phpBB releases](https://www.phpbb.com/downloads/).

### S3 — Unsupported runtimes and applications

**Status:** confirmed.

- CoverDoll production depends on PHP 5.6.
- DollForum, DollHarem and DollAlbum depend on PHP 7.4.
- Drupal 7 community security support ended in January 2025.
- CoverDoll Gallery 2 is a legacy application.
- DollAlbum and DollHarem run Coppermine 1.6.10, behind later security releases.

The appropriate response is application-specific migration, not a global PHP switch. Runtime retirement is complete only when no application, scheduled job or administrative tool still uses the old handler.

References: [PHP lifecycle](https://www.php.net/eol.php), [Drupal 7 end of life](https://www.drupal.org/about/drupal-7/d7eol/partners), [Coppermine releases](https://github.com/coppermine-gallery/cpg1.6.x/releases).

### S4 — Origin and Cloudflare inconsistency

**Status:** confirmed DNS paths; dashboard policy open.

Some primary sites are proxied through Cloudflare while DollForum and secondary hosts resolve directly to the shared origin. Cloudflare nameservers alone do not mean traffic is proxied. Direct origin records also reduce the effectiveness of protecting individual vhosts without server-wide origin controls.

The next step is a read-only export of DNS, proxy state, TLS mode, WAF rules, bot controls, rate limits, cache rules and recent security events for every available zone. Any later origin restriction must account for Plesk, mail, certificate validation, staging and operational access and requires separate approval.

### S5 — Session and transport hardening

**Status:** application-specific.

DollForum is configured for HTTPS but its phpBB secure-cookie flag is disabled. DollHarem has the flag enabled. This difference demonstrates why settings must not be copied globally.

No HSTS policy was found in the reviewed configuration. HSTS should be considered only after every required subdomain and HTTP dependency is validated.

### S6 — Operating-system maintenance

**Status:** confirmed.

The server runs an older installed kernel and core libraries report that a reboot is required. Because every hosted application depends on this host, kernel maintenance needs console access, a verified boot fallback and a full cross-site acceptance checklist.

## Positive controls

- MariaDB and Redis are bound locally rather than exposed as public database services.
- Redis protected mode is enabled.
- SSH password and keyboard-interactive authentication are disabled.
- TLS 1.2 and 1.3 are configured.
- The current Plesk PHP builds contain the vendor's October 2026 PHP2Shell remediation.
- DollHarem's official phpBB core matched the verified 3.3.3 package; DollForum's differences were explainable custom changes rather than an unexplained executable backdoor.

These controls reduce risk but do not cancel the priority findings.

## Open security work

1. Owner-assisted review of privileged forum accounts and direct permissions.
2. Controlled archive reachability and access-history assessment.
3. Source, plugin, authentication and upload review for Drupal, Gallery 2 and Coppermine.
4. Cloudflare zone exports and anonymized security-event correlation.
5. Restricted handling and retention decisions for public staging and abandoned code.
6. Targeted upload review based on recent changes and file type, without recursively scanning all media.

## Publication note

This repository intentionally omits the origin address, credentials, internal administrative URLs, raw logs and detailed exploit paths. The operational audit remains the controlled source I will use as the server administrator when preparing and carrying out approved work.

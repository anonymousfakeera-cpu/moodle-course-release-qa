# Release Gate (`local_releasegate`)

Stage-to-live readiness gate for Moodle courses. It reads course configuration,
applies deterministic rules, and records a verdict (READY / CONDITIONAL / BLOCKED /
INSUFFICIENT DATA).

Audience: Moodle administrators, plugin developers and security reviewers.

## Status

Alpha. Core is in place: capabilities, events, Report Builder entities and system
reports, course page, site overview, access review, trust sheet, audit report with
chain verification, and the base rule batch (10 rules).

Pending: live-site verification, POST-only scan actions, retention/erase flow,
external audit anchoring, policy profiles, further rule batches.

## Requirements

- Moodle 4.5 LTS, 5.0, 5.1 or 5.3 LTS. `version.php` declares
  `$plugin->requires = 2024100700` (Moodle 4.5.0). Developed against the 5.1 source.
- PHP 8.1+ (8.3 recommended; covers all four branches).
- Database: MySQL first. MariaDB and PostgreSQL are prepared in CI but not enabled yet.
- Report Builder must be enabled (standard Moodle core).
- No PHP extensions beyond core Moodle requirements.

## Install

1. Copy the plugin to `<moodledir>/local/releasegate`.
2. Visit Site administration > Notifications, or run the CLI upgrade.
3. The upgrade creates `local_rg_run`, `local_rg_result`, `local_rg_audit` and installs
   the capabilities from `db/access.php`.

## Upgrade

- Bump `$plugin->version` in `version.php` and run the Moodle upgrade.
- Capability changes take effect on the next request after upgrade.

## Uninstall

- Remove via Site administration > Plugins > Plugins overview.
- This drops the three `local_rg_*` tables and the plugin capabilities. No core table is
  written by the scan.

## Where things are

| Concern | Location |
|---|---|
| Capabilities | `db/access.php` |
| Tables | `db/install.xml` |
| Course page | `course.php`, `templates/course_page.mustache` |
| Site overview | `index.php`, `classes/reportbuilder/local/systemreports/site_overview.php` |
| Access review | `accessreview.php`, `.../systemreports/access_review.php` |
| Trust sheet | `trust.php`, `templates/trust_sheet.mustache` |
| Audit + verify | `audit.php`, `.../systemreports/audit_log.php` |
| Access-log events | `classes/event/` |
| Rule engine | `classes/local/` |

## Documentation

- [Admin guide](docs/admin-guide.md) — capability matrix, how to grant access, report locations.
- [Security and data](docs/security-and-data.md) — tables and columns, events, limitations.
- [Verification](docs/verification.md) — manual checklist for the stage site.
- [Changelog](CHANGELOG.md).

## Known limitations

- Not yet run against a live Moodle site.
- `results_exported` and `settings_changed` event classes exist but are not triggered yet.
  Downloads go through core `/reportbuilder/download.php`, which offers no plugin hook;
  there is no settings form yet.
- The site overview builds an `IN (...)` list from `get_user_capability_course()`. A user
  who holds the capability in thousands of courses produces a very large query.
- Scan and verify run over GET with a session key (`sesskey`), not POST.
- The hash chain is tamper-evident against application users only; a database
  administrator can rewrite it. No external anchoring yet.
- The privacy provider declares the audit table only; retention and erase flow are pending.
- The audit verify action reports only "intact" or "not verified"; it never exposes hashes.

## License

GPL v3 or later.

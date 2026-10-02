# Changelog

All notable changes to `local_releasegate` are recorded here.

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versioning: [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Capabilities in `db/access.php`: `view`, `viewresults`, `viewevidence`, `run`, `export`,
  `viewaudit`, `managesettings`, `waive`, `approve`, `viewaccessreview`, `viewaccessusers`.
- Access-log events in `classes/event/`: `gate_viewed`, `evidence_viewed`,
  `results_exported`, `settings_changed`.
- Report Builder entities: `run`, `result`, `role_capability`, `audit`, each exposing an
  explicit column allowlist.
- System reports: `course_results`, `site_overview`, `access_review`, `audit_log`.
- Course page `course.php` with Mustache template `templates/course_page.mustache`.
- Site overview page `index.php`.
- Access review page `accessreview.php`.
- Trust sheet page `trust.php` and template `templates/trust_sheet.mustache`.
- Audit page `audit.php` with a "Verify chain" action.
- Documentation: `README.md`, `CHANGELOG.md`, `docs/admin-guide.md`,
  `docs/security-and-data.md`, `docs/verification.md`.

### Changed

- `version.php`: `$plugin->version` raised to `2026100200`.
- `viewaudit` moved to `CONTEXT_SYSTEM` (the audit chain is site-wide).
- Site overview uses a `LEFT JOIN` so never-scanned courses appear with a "Not scanned"
  verdict.
- Rule-result messages for `ERROR` rows are replaced with a generic localised string in the
  UI; raw exception text is never shown.

### Fixed

- Site overview now adds `1 = 0` when the viewer has no allowed courses, instead of adding no
  condition.
- Course page no longer selects `*` from `local_rg_run`; only allowlisted columns are read.

### Known limitations

- `results_exported` and `settings_changed` are defined but not triggered yet.
- Scan and verify use GET with `sesskey`.
- Large `IN (...)` list for users with the capability in very many courses.
- No external anchoring of the audit chain; no retention/erase flow yet.
- Not yet verified against a live Moodle site.

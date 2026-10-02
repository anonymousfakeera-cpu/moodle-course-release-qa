# Release Gate — Security and Data

What the plugin reads/writes, for anyone reviewing security or privacy.

Audience: security reviewers, administrators, DPOs.

## Data tiers

- **Tier A** — course configuration. No personal data. Read by the scan.
- **Tier B** — aggregates (counts only). Not collected today.
- **Tier C** — personal and learner data. Never read by this plugin.
- **Tier P** — plugin-owned tables. Written by the scan.

## Tables and columns read (Tier A)

Taken from the rule, engine and check source.

| Table | Columns used | Used by |
|---|---|---|
| `course` | `id`, `enablecompletion` | RG-CRS-001 |
| `course_modules` | `id`, `course`, `module`, `instance`, `deletioninprogress`, `availability` | course_context, RG-RST-001 |
| `modules` | `id`, `name` | course_context |
| `course_completion_criteria` | `course`, `criteriatype`, `moduleinstance` | RG-CMP-001/002/003 |
| `enrol` | `courseid`, `status` | RG-ENR-001 |
| `grade_items` | `courseid`, `itemtype`, `itemmodule`, `iteminstance`, `itemnumber`, `gradepass` | RG-GRD-001, RG-QUZ-002 |
| `quiz` | `id`, `grade` | RG-QUZ-002 |
| `quiz_slots` | `quizid` | RG-QUZ-001 |
| `scorm_scoes` | `scorm`, `launch` | RG-SCM-001 |
| `task_scheduled` | `lastruntime` | cron health check |

The activity name shown in messages is read from the activity's own instance table
(for example `quiz.name`), keyed by module type.

The course page, site overview and reports read these core tables through Report Builder
entities or existing queries: `course`, `course_categories`, `context`, `role`,
`role_capabilities`, `role_assignments`.

## Tables never read (Tier C)

`user`, `user_enrolments`, `grade_grades`, `course_modules_completion`,
`course_completions`, `quiz_attempts`, `logstore_standard_log`.

There is no enforced CI check for this yet; it is a code-review property.

## Tables written (Tier P)

Only plugin-owned tables are written.

| Table | What is written | Personal data |
|---|---|---|
| `local_rg_run` | verdict, coverage, ruleset version, fingerprint, `actorref`, time | pseudonymous hash only |
| `local_rg_result` | rule id, area, severity, status, message, evidence (config facts) | none |
| `local_rg_audit` | action, course id, `actorref`, detail, prevhash, hash, time | pseudonymous hash only |

`actorref` is a salted SHA-256 of the user id. It is not reversible without the site salt.
It is never exposed in any UI screen.

## Access-log events

From `classes/event/`.

| Event | Trigger | Status |
|---|---|---|
| `local_releasegate\event\gate_viewed` | course page viewed | triggered |
| `local_releasegate\event\evidence_viewed` | evidence panel opened | triggered |
| `local_releasegate\event\settings_changed` | setting changed | not triggered (no settings form) |
| `local_releasegate\event\results_exported` | export started | not triggered (see limitations) |

## Privacy provider

- The provider declares `local_rg_audit` (`actorref`, `action`, `timecreated`).
- It does not implement export or delete for a user, because refs are one-way and not
  attributable without the site salt.
- Retention and erase flows are not built.

## Trust sheet summary

The in-product trust sheet (`/local/releasegate/trust.php`) shows the same table, denylist,
default-access and event lists, built from `trust.php`.

## Known limitations

- **Large `IN (...)` list.** The site overview calls `get_user_capability_course()` and builds
  an `IN` condition from every returned course id. A user who holds `view` in thousands of
  courses produces a very large query and parameter list.
- **State-changing scan over GET with `sesskey`.** Run scan and verify chain are triggered by
  a GET link carrying `sesskey`, not a POST form. The session key mitigates CSRF but the action
  is still a GET.
- **No download hook.** Downloads go through core `/reportbuilder/download.php`, which offers
  no plugin hook, so `results_exported` is not triggered by plugin code.
- **No settings form.** `settings_changed` is not triggered.
- **Chain anchoring.** The hash chain is tamper-evident against application users only. A
  database administrator can rewrite it; there is no external anchoring.
- **Raw hash secrets.** `prevhash` and `hash` are never shown in the UI. `actorref` is not
  shown either.

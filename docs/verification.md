# Release Gate — Verification Checklist

Purpose: one place for every manual verification step from every milestone, to be run on a
Moodle 5.1 stage site.

Audience: the owner and any tester.

Status legend: **Pending** — run each step on a stage site and mark Pass/Fail.

## Step 0 — fixes

| # | Step | Expected result | Status |
|---|---|---|---|
| 0.1 | Open Define roles, inspect `viewevidence` | Manager archetype only | Pending |
| 0.2 | Inspect `viewresults` | editingteacher and manager allowed | Pending |
| 0.3 | Check `version.php` | `$plugin->version` = `2026100200` | Pending |
| 0.4 | Open Settings > Plugins overview | Plugin listed as upgraded, no error | Pending |

## M1 — capabilities, events, strings

| # | Step | Expected result | Status |
|---|---|---|---|
| 1.1 | Upgrade the plugin | All 11 capabilities appear in Define roles | Pending |
| 1.2 | Inspect capability risks | `managesettings` = Config; `viewaccessusers` = Personal; rest none | Pending |
| 1.3 | Check learner/teacher defaults | No Release Gate capability by default for learner | Pending |
| 1.4 | View a course gate as manager | `gate_viewed` event appears in the log store | Pending |

## M2 — entities

| # | Step | Expected result | Status |
|---|---|---|---|
| 2.1 | Add the course results report to a custom report source | Run/result columns available, limited to the allowlist | Pending |
| 2.2 | Inspect result rule column | Shows localised rule title, not a bare id | Pending |

## M3 — course results report

| # | Step | Expected result | Status |
|---|---|---|---|
| 3.1 | Open the course gate after a scan | Rule results render as a system report with filters severity/status/area | Pending |
| 3.2 | Remove `export` from a role | Download button disappears | Pending |
| 3.3 | Request the report with another course's `runid` | No rows from the other course | Pending |
| 3.4 | Course with no scan | Empty-state notice shows | Pending |

## M4 — site overview

| # | Step | Expected result | Status |
|---|---|---|---|
| 4.1 | Teacher with `view` in course A only, open `/local/releasegate/index.php` | Only course A listed | Pending |
| 4.2 | Request another course's data directly | No rows | Pending |
| 4.3 | User with no `view` anywhere | Permission error, no report | Pending |
| 4.4 | Course never scanned | Row shows "Not scanned" and still links to the course gate | Pending |
| 4.5 | Filter by verdict "Not scanned" | Never-scanned courses returned | Pending |
| 4.6 | Filter by category | Rows limited to that category | Pending |

## M5 — course page

| # | Step | Expected result | Status |
|---|---|---|---|
| 5.1 | Open the gate with a scan | Banner shows verdict, coverage, ruleset version, fingerprint | Pending |
| 5.2 | Open the gate without a scan | Notice plus (if allowed) the Run scan button | Pending |
| 5.3 | As editingteacher | Results report visible, evidence panel absent | Pending |
| 5.4 | As manager, click "Show evidence" | Panel opens; `evidence_viewed` event appears | Pending |
| 5.5 | Force an ERROR result (if reproducible) | Message shows the generic string, not exception text | Pending |
| 5.6 | Click "Run scan now" | New run stored, page reloads | Pending |

## M6 — access review

| # | Step | Expected result | Status |
|---|---|---|---|
| 6.1 | Open `/local/releasegate/accessreview.php` as manager | Rows per capability and context with permission | Pending |
| 6.2 | Without `viewaccessusers` | "Effective users" column hidden | Pending |
| 6.3 | With `viewaccessusers` | Effective user counts shown | Pending |
| 6.4 | Click "Check permissions" | Core `admin/roles/check.php` opens for that context | Pending |
| 6.5 | Filter by capability, context level, permission | Rows filtered accordingly | Pending |

## M7 — trust sheet

| # | Step | Expected result | Status |
|---|---|---|---|
| 7.1 | Open `/local/releasegate/trust.php` | Shows read tables, denylist, default access, events | Pending |
| 7.2 | Compare the read table against this doc and the rules | Lists match | Pending |
| 7.3 | Confirms no claim of testing or certification | Page states facts only | Pending |

## M8 — audit

| # | Step | Expected result | Status |
|---|---|---|---|
| 8.1 | Open `/local/releasegate/audit.php` as manager | Audit rows with localised action, course, time | Pending |
| 8.2 | Inspect columns | No `prevhash`, `hash` or `actorref` shown | Pending |
| 8.3 | Click "Verify chain" on an untouched site | "Audit chain is intact" | Pending |
| 8.4 | Manually alter one audit row in the DB, then verify | "Could not be verified" message, no raw error | Pending |
| 8.5 | Without `viewaudit` | Permission error | Pending |

## Cross-cutting

| # | Step | Expected result | Status |
|---|---|---|---|
| X.1 | Confirm no file outside `releasegate/` changed | `git status` clean outside the plugin | Pending |
| X.2 | Run `php -l` and phpcs on a machine with PHP | No syntax or style errors | Pending |
| X.3 | Confirm no table outside `local_rg_*` is written during a scan | DB audit shows no other writes | Pending |

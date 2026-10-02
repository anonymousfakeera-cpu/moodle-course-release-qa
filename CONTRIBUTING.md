# Contributing

The project is in its alpha phase and run by a single maintainer. Issues and focused pull
requests are welcome once the repository is public.

## Ground rules

- **Docs should describe behaviour accurately.** Do not write "tested" or "works" for anything
  that has not been executed.
- **Data scope.** Rules read only course configuration tables listed in
  `docs/security-and-data.md`. Learner-level tables (for example `user`, `grade_grades`,
  `course_modules_completion`, `quiz_attempts`, log tables) must not be read by rule code.
- **Access control.** Use Moodle capabilities and check them on every entry point. Restrict
  report rows in the query, not only in `can_view()`.
- **Compatibility.** Target Moodle 4.5 LTS, 5.0, 5.1 and 5.3 LTS, PHP 8.1 syntax floor. Check
  any Moodle API against the 4.5 branch before using it.

## Local checks

Requires PHP 8.3 and Composer.

```bash
php -l path/to/file.php                       # syntax, run for every changed file
phpcs --standard=moodle .                     # Moodle coding style, expect no errors or warnings
```

PHPUnit and Behat run in CI with `moodle-plugin-ci` (see `.github/workflows/moodle-ci.yml`);
they need a full Moodle and a database.

## Adding a rule

- [ ] Class in `classes/local/rule/` with `id()`, `area()`, `severity()`, `evaluate()`; skip `deletioninprogress` modules; use `pass()` / `skip()` / `fail()`.
- [ ] Register in `classes/local/engine/registry.php` and bump `RULESET_VERSION`.
- [ ] Lang strings `rule_<ID>` and `rule_<ID>_fail` in `lang/en/local_releasegate.php` in strcmp key order.
- [ ] Test in `tests/rules_integrity_test.php` (PASS, FAIL, SKIP plus edge cases); meta tests cover registration automatically.
- [ ] Catalog status and `CHANGELOG.md` updated.

## Pull request checklist

- [ ] `php -l` and `phpcs --standard=moodle` are clean
- [ ] New strings are in `lang/en/local_releasegate.php`
- [ ] New or changed capabilities are described in `docs/admin-guide.md`
- [ ] Docs should describe behaviour accurately
- [ ] `CHANGELOG.md` updated

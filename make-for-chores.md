# Make for Chores

**Scope:** Make as a task runner — when it beats a
shell script, and a generic backup/check example.

## Why Make for chores

A shell script runs every step, every time. Make runs
only the steps whose inputs changed. For chores with
expensive or slow steps — backups, report generation,
data processing — that difference matters. A Makefile
is also self-documenting: `make backup`, `make check`,
`make report` read as a task list, and `make help`
targets are a proud tradition.

The rule from [How Make Decides](how-make-decides.md)
still applies: if a task's "inputs" aren't files Make
can see, declare it [`.PHONY`](phony-targets.md) so it
always runs.

## A generic backup example

```make
BACKUP_DIR = backups
DATE = $(shell date +%Y-%m-%d)

.PHONY: backup check

backup: $(BACKUP_DIR)/snapshot-$(DATE).tar.gz

$(BACKUP_DIR)/snapshot-$(DATE).tar.gz: backup-src/file.txt
	mkdir -p $(BACKUP_DIR)
	tar czf $@ $^
	@echo "snapshot saved: $@"

check:
	@echo "backup dir: $(BACKUP_DIR)"
	@ls -la $(BACKUP_DIR) 2>/dev/null || echo "(no backups yet)"
```

Notes on the moving parts:

- `$(shell date +%Y-%m-%d)` runs once when the
  Makefile is read, giving every snapshot a date stamp.
  (Shell command substitution, Make-style.)
- The snapshot file is a *real* target with a real
  prerequisite, so the second run is free:

```text
$ make backup
mkdir -p backups
tar czf backups/snapshot-2026-10-16.tar.gz backup-src/file.txt
snapshot saved: backups/snapshot-2026-10-16.tar.gz
$ make backup
make: Nothing to be done for 'backup'.
```

- `check` is phony — it always runs, because "are
  there backups?" isn't a file.

## Ideas worth stealing

- **`make help`**: a target that greps its own
  Makefile for `##` comments and prints them. Every
  shared Makefile deserves one.
- **Date-stamped artifacts** as real targets, so
  reruns are no-ops.
- **Guard targets**: `deploy: test` — the deploy
  recipe only runs if the tests pass, because Make
  stops on the first failing recipe.

## Honest limits

Make is a poor fit when steps must *always* run in
full (then a script is simpler), when the dependency
graph is really a pipeline with streaming data, or
when you need Windows/macOS/Linux portability without
caveats — recipes are shell commands, and shells
differ. For one-machine chores with skippable steps,
though, it's hard to beat.

**Check:** run your chore target twice. The second
run should do visibly less — if it doesn't, something
that should be a file target got marked `.PHONY`, or
vice versa.

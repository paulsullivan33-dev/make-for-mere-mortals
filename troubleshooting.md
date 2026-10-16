# Troubleshooting

**Scope:** the failures every Make beginner hits,
symptom → cause → fix. Every error below was
reproduced with GNU Make 4.3; the exact messages are
quoted verbatim.

## `Makefile:2: *** missing separator.  Stop.`

**Cause:** a recipe line indented with spaces instead
of a tab. The `2` is the line number.

**Fix:** delete the leading whitespace on that line
and press Tab. Verify with `cat -A Makefile` — recipe
lines must show `^I`, not spaces. See [Your First
Makefile](your-first-makefile.md).

## `Makefile:1: *** recipe commences before first target.  Stop.`

**Cause:** a tab-indented line appears before any
`target:` line — usually a recipe that lost its
target during editing, or a stray indented line at
the top of the file.

**Fix:** find the indented line before the first
target and either attach it to a rule or delete it.

## `make: *** No rule to make target 'frobnicate'.  Stop.`

**Cause:** you asked for a target Make doesn't know —
a typo (`make claen`), or a target that was never
defined.

**Fix:** check the spelling; `make -p | grep '^frobnicate:'`
confirms whether the target exists. With a prerequisite
variant — `No rule to make target 'missing-file.txt',
needed by 'hello'` — the prerequisite file doesn't
exist and nothing knows how to build it: create the
file or add a rule for it.

## `make: Circular b <- a dependency dropped.`

```text
$ make a
make: Circular b <- a dependency dropped.
make: Nothing to be done for 'a'.
```

**Cause:** `a` needs `b` and `b` needs `a`. Make
drops one leg of the circle and carries on — which
usually means nothing gets built.

**Fix:** break the cycle. One of the two dependencies
is wrong: figure out which file is *really* produced
from which, and remove the backwards edge.

## `make: 'clean' is up to date.`

**Cause:** the classic missing-`.PHONY` trap. A file
named `clean` exists, so Make treats your task target
as a satisfied file target and skips the recipe —
silently, with exit code 0.

**Fix:** declare `.PHONY: clean` (and every other
task target). See [.PHONY Targets](phony-targets.md).

## `Makefile:2: warning: undefined variable 'OUTDIR'`

**Cause:** a misspelled (or never-set) variable —
`$(OUTDIR)` expanded to nothing, so files landed in
`/file` instead of `$OUTDIR/file`. Without
`--warn-undefined-variables`, there is no message at
all; the build just does the wrong thing quietly.

**Fix:** fix the name, or give it a default with
`OUTDIR ?= somewhere`. Run with
`--warn-undefined-variables` while developing.

## The build does the wrong thing, quietly

No error, but stale results — the header-dependency
bug from [A Real C Build](a-real-c-build.md). A
prerequisite is missing, so Make doesn't rebuild when
it should.

**Fix:** `make --trace` shows why each target did or
didn't rebuild. Add the missing prerequisite. When
in doubt, `make clean && make` tells you whether the
Makefile is wrong or the build directory is stale.

## Debugging order

`make -n` first (what would it do?), then `--trace`
(why?), then `--warn-undefined-variables` (typo?),
then `make -p` (what does Make believe?). See
[Debugging Makefiles](debugging-makefiles.md).

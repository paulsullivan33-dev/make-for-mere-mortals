# Debugging Makefiles

**Scope:** the four tools that answer "why did Make do
that?" — dry run, trace, the database dump, and the
undefined-variable warning.

## `-n`: the dry run

`make -n` (or `--dry-run`) prints every command it
*would* run, without running anything:

```text
$ touch main.c
$ make -n
gcc -Wall -Wextra -g -c main.c -o main.o
gcc -Wall -Wextra -g -o hello main.o greet.o
```

This is the first tool for every Makefile problem.
Wrong command? The variable or rule is wrong. Right
commands but wrong order? A prerequisite is missing.
No output at all? Make thinks everything is up to
date — check with `--trace` why.

## `--trace`: why this target

`--trace` prints, for each target, *why* Make decided
to rebuild it:

```text
$ make --trace
Makefile:8: update target 'main.o' due to: main.c
gcc -Wall -Wextra -g -c main.c -o main.o
Makefile:5: update target 'hello' due to: main.o
gcc -Wall -Wextra -g -o hello main.o greet.o
```

"Due to: main.c" — the file that was newer. When a
build does something surprising, `--trace` names the
cause.

## `-p`: the whole database

`make -p` prints everything Make knows: every
variable (with where it was defined), every rule,
every implicit pattern, every phony target. It's
hundreds of lines — pipe it through grep:

```text
$ make -p | grep -B1 'Phony target'
$ make -p | grep -A2 '^CFLAGS ='
```

Use it to answer "what is this variable *really* set
to?" and "which rule is Make actually using for this
target?" — the two questions behind most confusion.

## `--warn-undefined-variables`

Typos in variable names fail silently — `$(OUTDIR)`
with `OUTDIR` misspelled as `OUTDR` expands to
nothing, and your files land in `/`. This flag turns
those into warnings:

```text
$ make --warn-undefined-variables show
Makefile:2: warning: undefined variable 'OUTDIR'
out=/file
```

The build continues (exit 0), but the warning names
the file, line, and variable. Consider leaving this
on while developing a Makefile.

## The debugging order

1. `make -n` — what would it do?
2. `--trace` — why did it choose that?
3. `--warn-undefined-variables` — is a typo
   expanding to nothing?
4. `make -p` — what does Make actually believe?

Nine times out of ten, step 1 or 2 ends the mystery.

**Check:** reproduce any confusing behavior with `-n`
first. If the dry run looks right but the real run
fails, the problem is in the commands themselves, not
the Makefile.

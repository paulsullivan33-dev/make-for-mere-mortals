# Parallel Builds

**Scope:** `-j`, what it does, and the one rule that
makes it safe.

## The flag

By default Make runs one recipe at a time. `-jN` runs
up to N in parallel:

```text
$ make -j4
```

On a multi-core machine, a large build with `-j$(nproc)`
finishes dramatically faster — compiling fifty files
uses fifty cores instead of one. (Omit the number —
bare `-j` — and Make runs *everything* at once, which
can exhaust memory on big projects. Give it a number.)

## The one rule

Parallel Make is safe **iff your prerequisites are
honest**. Make parallelizes everything it believes is
independent. If `link` really needs `gen-header` to
finish first but the Makefile doesn't say so, `-j`
will run them together and fail intermittently — the
worst kind of bug, because serial `make` works fine.

```make
# honest: link waits for the header
program: main.o gen-header.h
	$(CC) -o $@ main.o

gen-header.h: gen.py
	python3 gen.py > $@
```

The discipline from [How Make
Decides](how-make-decides.md) — list every real input
— is what makes `-j` trustworthy. If `-j` breaks a
build that works serially, the Makefile is lying about
some dependency. That's a bug worth fixing anyway.

## What doesn't parallelize

Recipes within a single rule always run in order, one
after another — `-j` parallelizes *rules*, not lines.
And some recipes can't run concurrently at all (two
rules writing the same file). For those, `.NOTPARALLEL`
disables parallelism for the whole Makefile, or order
dependencies (`|`) serialize specific pairs.

**Check:** `make -j4 --trace` shows the interleaved
order Make chose. If two recipes that must be ordered
ran together, a prerequisite is missing.

# .PHONY Targets

**Scope:** targets that are tasks, not files — and the
silent failure when you forget `.PHONY`.

## Tasks vs files

So far every target has been a file. But `clean`
doesn't build anything — it *does* something:

```make
clean:
	rm -f hello *.o
```

Targets like `clean`, `test`, `install`, `backup` are
**phony**: they name a task, not a file. Tell Make with
the special `.PHONY` target:

```make
.PHONY: clean
clean:
	rm -f hello *.o
```

## The trap

Without `.PHONY`, Make treats `clean` like a file. If
no file named `clean` exists, the recipe runs — fine.
But the day a file named `clean` appears (a stray
download, a typo'd redirect), Make sees an up-to-date
"target" and silently skips the recipe:

```text
$ touch clean
$ make clean
make: 'clean' is up to date.
```

Your cleanup didn't run, Make reported success (exit
0), and nothing looks wrong. This actually happened
while testing this guide. With `.PHONY: clean`, the
recipe always runs:

```text
$ make clean
rm -f hello *.o
```

**Rule of thumb:** if a target doesn't produce a file
with its own name, declare it `.PHONY`. List them all
in one place near the top of the file:

```make
.PHONY: all clean test install backup
```

`all` is the conventional first target — the thing
plain `make` builds — and it's phony too, since it
produces no file itself:

```make
.PHONY: all clean
all: hello
```

## What `.PHONY` does not do

It doesn't change dependency order, and it doesn't make
prerequisites run "more." A phony target's prerequisites
still build normally; only the phony target itself is
always considered out of date.

**Check:** `make -p | grep -B1 'Phony target'` lists the
phony targets Make knows about. If your task target
isn't there, you forgot the declaration:

```text
$ make -p | grep -B1 'Phony target'
clean:
#  Phony target (prerequisite of .PHONY).
--
all: hello
#  Phony target (prerequisite of .PHONY).
```

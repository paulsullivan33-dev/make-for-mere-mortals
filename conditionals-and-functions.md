# Conditionals and Functions

**Scope:** just enough `ifeq`, `wildcard`, and
`$(shell ...)` for real Makefiles — not the whole
function catalog.

## Conditionals

Sometimes a Makefile needs to vary by platform or by a
flag. `ifeq` compares two values when the Makefile is
read:

```make
UNAME_S = $(shell uname -s)

ifeq ($(UNAME_S),Linux)
    PLATFORM = linux
else
    PLATFORM = other
endif

show:
	@echo "platform: $(PLATFORM)"
```

```text
$ make show
platform: linux
```

Notes:

- The `ifeq` line and its branches are **not** recipe
  lines — no tabs. They're Makefile syntax, evaluated
  while reading.
- Indenting the branches (as above) is cosmetic but
  conventional.
- Siblings: `ifneq` (not equal), `ifdef` (variable is
  defined), `ifndef`.

Conditionals are for decisions Make can make *before*
running anything. For decisions *during* a recipe, use
the shell's own `if`:

```make
check:
	if [ -f hello ]; then echo "built"; fi
```

## Functions

Make's built-in functions transform text. The handful
worth knowing:

```make
FILES = $(wildcard *.c)
```

`$(wildcard PATTERN)` expands to the matching files —
empty if none match. This is how Makefiles collect
sources without naming each one:

```make
SRCS = $(wildcard *.c)
OBJS = $(SRCS:.c=.o)
```

The second line is a **substitution reference**: every
`.c` becomes `.o`. (`$(patsubst %.c,%.o,$(SRCS))` is
the general form.)

```make
show:
	@echo "c files here: $(if $(FILES),$(FILES),(none))"
```

`$(if CONDITION,THEN,ELSE)` picks a value — the real
output from testing:

```text
$ make show
platform: linux
c files here: a.c b.c
```

And `$(shell ...)` runs a shell command and captures
its output, as the `DATE` and `UNAME_S` examples show.

## When to stop

If your Makefile is growing nested conditionals and
string surgery, that's a sign the logic belongs in a
script the Makefile *calls*, not in Make itself. Make
is a dependency tracker with a macro language, not a
programming language.

**Check:** `make -n` on a conditional Makefile shows
which branch's recipes would run — a quick way to
confirm the condition evaluated the way you expected.

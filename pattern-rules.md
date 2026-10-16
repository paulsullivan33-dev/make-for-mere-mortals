# Pattern Rules

**Scope:** one rule that builds every `.o` from its
`.c` — how `%` works and when patterns don't apply.

## The repetition problem

The C build has two nearly identical compile rules:

```make
main.o: main.c greet.h
	$(CC) $(CFLAGS) -c $< -o $@

greet.o: greet.c greet.h
	$(CC) $(CFLAGS) -c $< -o $@
```

Add ten more source files and you have ten more copies.
A **pattern rule** replaces them all:

```make
%.o: %.c greet.h
	$(CC) $(CFLAGS) -c $< -o $@
```

`%` matches any **stem** — the part that's the same on
both sides. Asking for `main.o` matches with stem
`main`, so the prerequisite becomes `main.c` (plus
`greet.h`). One rule, any number of source files.

This is the rule the real build in [A Real C
Build](a-real-c-build.md) uses, and the output is
identical:

```text
$ make
gcc -Wall -Wextra -g -c main.c -o main.o
gcc -Wall -Wextra -g -c greet.c -o greet.o
gcc -Wall -Wextra -g -o hello main.o greet.o
```

## Built-in patterns

Make ships with pattern rules for the common cases —
compiling C, C++, Fortran, assembling, even TeX. Watch
what happens with a Makefile that has *only* the
dependency line:

```make
hello: main.o greet.o
```

```text
$ make
cc    -c -o main.o main.c
cc    -c -o greet.o greet.c
```

The compiles happened with no recipe at all — Make's
built-in `%.o: %.c` rule handled them (the odd spacing is
Make echoing the exact command). But notice what *didn't*
happen: no linked `hello` binary. The built-in link rule
only covers the single-object case (`hello` from
`hello.o`), so multi-object programs still need their
own link recipe:

```make
hello: main.o greet.o
	$(CC) -o $@ $^
```

Built-ins are why tiny Makefiles work at all — and why
defining your own `CC` and `CFLAGS` customizes every
built-in rule at once.

**Check:** `make -p | grep -A1 '^%.o:'` lists the
built-in pattern rules. There are more than you'd guess.

## Limits

Patterns match **file names**, not arbitrary text —
`%.o: %.c` can't express "object files in `build/`
from sources in `src/`" without help (that's what
`$(patsubst ...)` and `vpath` are for; look them up when
you need them). And a pattern rule only fires if Make
can't find an explicit rule first — explicit always
beats pattern.

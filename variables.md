# Variables

**Scope:** naming values instead of repeating them —
the three assignment flavors and command-line overrides.

## Why variables

The C build repeats `gcc -Wall -Wextra -g` in every
recipe. Name it once:

```make
CC = gcc
CFLAGS = -Wall -Wextra -g

hello: main.o greet.o
	$(CC) $(CFLAGS) -o hello main.o greet.o
```

Reference with `$(NAME)` (or `${NAME}`). Change the
flags in one place, and every recipe follows.

## The three flavors

```make
A = one
B = $(A) two
A = three
C := $(A) four
```

```text
$ make show
B=three two
C=three four
```

- **`=` (recursive):** the value is re-expanded every
  time it's used. `B` picks up the *later* value of `A`
  — `three`, not `one`. This is the default and the
  source of most "why did that change?" surprises.
- **`:=` (simple):** expanded once, when the line is
  read. `C` froze `A` as `three`.
- **`?=` (conditional):** assign only if the variable is
  not already set. The standard way to write a default
  the user may override:

```make
BUILD_DIR ?= build
```

## Command-line override

Any variable can be overridden when you run Make:

```text
$ make show A=ZERO
B=ZERO two
C=ZERO four
```

Command-line definitions beat Makefile assignments —
even `:=` lines expand with the command-line value, as
the real output above shows. (One exception: `override`
directives in the Makefile, which are rare enough to
look up when you meet them.)

This is the everyday customization mechanism: `make
BUILD_DIR=/tmp/build`, `make CFLAGS="-O2"`. Design your
Makefiles so the things people change are variables.

## Environment variables

Make imports the shell's environment as variables — but
a Makefile assignment wins over the environment, while
the command line beats both. Precedence, highest first:

1. Command line: `make VAR=value`
2. Makefile assignment
3. Environment: `export VAR=value`
4. Make's built-in defaults (like `CC = cc`)

So `CFLAGS` in your environment is a suggestion; `CFLAGS`
in the Makefile is a decision.

**Check:** `make -p | grep -A2 '^CFLAGS'` shows where a
variable got its value (among several pages of output —
`make -p` prints the entire database).

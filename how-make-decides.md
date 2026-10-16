# How Make Decides What to Do

**Scope:** the timestamp rule — the three questions Make
asks about every target, and the three answers you'll
see.

## The three questions

For each target, Make asks:

1. **Does the target file exist?** If not, run the
   recipe.
2. **Is any prerequisite newer than the target?** If so,
   the target is out of date — run the recipe.
3. **Otherwise, skip it.**

That's it. Make knows nothing about what your commands
do; it only compares file modification times. From the
[C build](a-real-c-build.md):

```text
$ make
gcc -Wall -Wextra -g -c main.c -o main.o
gcc -Wall -Wextra -g -c greet.c -o greet.o
gcc -Wall -Wextra -g -o hello main.o greet.o
$ make
make: 'hello' is up to date.
```

The second run does nothing: `hello` exists and is
newer than both `.o` files. Now touch the header — which
both `.c` files include:

```text
$ touch greet.h
$ make
gcc -Wall -Wextra -g -c main.c -o main.o
gcc -Wall -Wextra -g -c greet.c -o greet.o
gcc -Wall -Wextra -g -o hello main.o greet.o
```

Everything rebuilds, because `greet.h` is newer than the
`.o` files, which makes them newer than `hello`. One
`touch`, and Make traced the whole chain.

## The three answers

You'll see these messages constantly:

| Message | Meaning |
|---|---|
| *(commands run)* | Target was missing or out of date; Make rebuilt it. |
| `make: 'hello' is up to date.` | Target exists and is newer than everything it needs. Nothing to do. |
| `make: Nothing to be done for 'backup'.` | The target's prerequisites are all satisfied; typically seen for `.PHONY` task targets. |

## The limitation

Timestamps are all Make sees. If a recipe's output
depends on something invisible to Make — an environment
variable, a database, a file the recipe reads but isn't
listed as a prerequisite — Make won't rebuild when it
should. The fix is honesty: list every real input as a
prerequisite, or mark the target [`.PHONY`](phony-targets.md)
so it always runs.

**Check:** after any build, run `make` again immediately.
If it rebuilds things you didn't touch, a prerequisite
is missing or a recipe isn't writing the file it claims
to — both are worth fixing before the Makefile grows.

# What Make Is

**Scope:** the one idea behind Make, why a 1976 tool is
still everywhere, and what it is not.

## The one idea

Every project has steps that depend on other steps. To
build a program you compile each source file, then link
the results. To make a report you fetch the data, then
format it. To back up a server you snapshot the files,
then copy the snapshot offsite.

Make's one idea: **describe the dependencies, and let the
tool figure out the minimum work.** You say "the program
needs these object files, each object file needs its
source file." Make checks what's already done and what's
out of date, and runs only what's needed. Edit one file
out of fifty, and Make recompiles one file — not fifty.

The description lives in a file called `Makefile` (or
`makefile`), and it looks like this:

```make
hello: main.o greet.o
	gcc -o hello main.o greet.o

main.o: main.c greet.h
	gcc -c main.c -o main.o

greet.o: greet.c greet.h
	gcc -c greet.c -o greet.o
```

Read the first block as: "to make `hello`, I need
`main.o` and `greet.o`; here's the command that does it."
That's the whole mental model. The rest of this guide is
details.

## Why it's still here

Make was written in 1976 for building C programs, and it
is still the default build tool for an enormous share of
the world's C and C++ code — including the Linux kernel.
It survives because the idea is small and the tool does
exactly one thing: it knows nothing about your language,
your compiler, or your project. It only knows
dependencies and timestamps.

That generality is why people use Make for things that
have nothing to do with compiling: generating documents,
running test suites, deploying websites, processing data.
If your chore is "do step B after step A, but skip it if
it's already done," Make already knows how. See [Make
for Chores](make-for-chores.md).

## What Make is not

- **Not a scripting language.** Recipes are shell
  commands, and Make's own syntax is minimal on purpose.
  For complex logic, call a script from a recipe.
- **Not a package manager.** It doesn't download
  dependencies or manage versions. (Tools like CMake
  *generate* Makefiles; that's a different layer.)
- **Not a task runner with a memory.** Make decides what
  to do from file timestamps alone. If a command's output
  depends on something Make can't see — an environment
  variable, a database — Make won't know to rerun it.
  (The `.PHONY` chapter covers the workaround.)

## The variants

This guide covers **GNU Make**, the version on virtually
every Linux system and available for macOS (via Xcode or
Homebrew) and Windows (via WSL, MinGW, or Chocolatey).
BSD systems ship BSD Make, which is similar in spirit but
different in syntax — the two are not interchangeable.

**Check:**

```text
$ make --version | head -1
GNU Make 4.3
```

Any 4.x version behaves the way this guide describes.

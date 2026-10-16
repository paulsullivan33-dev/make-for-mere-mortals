# Glossary

**Automatic variables**
`$@`, `$<`, `$^` and friends — set by Make inside
each recipe to the target, first prerequisite, and
all prerequisites. See [Automatic
Variables](automatic-variables.md).

**Built-in rules**
Pattern rules that ship with Make (compiling C,
linking single objects, ...). Your own rules
override them; your `CC` and `CFLAGS` customize
them.

**Default goal**
The target built by plain `make`: the first
explicit rule in the file (unless `.DEFAULT_GOAL`
is set).

**Explicit rule**
A rule you wrote with a literal target name, as
opposed to a pattern rule.

**Parallel build**
`make -jN` — up to N recipes at once. Safe only
with honest prerequisites.

**Pattern rule**
A rule with `%` matching a stem, e.g. `%.o: %.c`
builds every object file from its source. See
[Pattern Rules](pattern-rules.md).

**.PHONY**
A declaration that a target is a task, not a file —
it always runs. See [.PHONY
Targets](phony-targets.md).

**Prerequisite**
A file (or target) that must be up to date before
the target builds. Listed after the colon.

**Recipe**
The shell commands under a rule — each line
starting with a tab. Echoed before running
unless prefixed with `@`.

**Recursive variable (`=`)**
Re-expanded on every use; picks up later changes
to variables it references.

**Rule**
Target + prerequisites + recipe. The unit of a
Makefile.

**Simple variable (`:=`)**
Expanded once, when the line is read.

**Stem**
The part `%` matched in a pattern rule: for
`%.o: %.c` building `main.o`, the stem is `main`.

**Target**
The thing a rule builds — a file, or a task name
declared `.PHONY`. Written before the colon.

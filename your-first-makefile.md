# Your First Makefile

**Scope:** the three parts of a rule — target,
prerequisites, recipe — and the tabs rule that trips up
every beginner exactly once.

## The three parts

A Makefile is a list of **rules**. Each rule has three
parts:

```make
hello:
	echo "Hello, Make!"
```

- **Target** (`hello:`) — the thing you want to make, or
  the name of a task.
- **Prerequisites** — files the target depends on. This
  rule has none yet.
- **Recipe** — the shell commands that build the target,
  one per line, each **starting with a tab**.

Run it:

```text
$ make
echo "Hello, Make!"
Hello, Make!
```

Two things to notice. First, Make **echoes** each command
before running it — that's the `echo "Hello, Make!"`
line, then the actual output. (The `@` prefix silences
the echo; see [Automatic Variables](automatic-variables.md).)
Second, running `make` with no arguments builds the
**first** target in the file — the default goal.

Now add a prerequisite:

```make
greeting.txt:
	echo "Hello, Make!" > greeting.txt

hello: greeting.txt
	cat greeting.txt
```

```text
$ make hello
echo "Hello, Make!" > greeting.txt
cat greeting.txt
Hello, Make!
```

Make built `greeting.txt` first (because `hello` needs
it), then ran `hello`'s recipe. That's dependency order,
and it's the whole engine. (Plain `make` would have built
only `greeting.txt` — the first rule is the default
goal — so the target is named explicitly here.)

## The tabs rule

Recipe lines **must start with a tab character** — not
spaces, not "looks indented." This is Make's most famous
quirk, and it bites everyone once. With four spaces
instead of a tab:

```text
$ make
Makefile:2: *** missing separator.  Stop.
```

"Missing separator" means "I expected a tab and didn't
find one." The fix is always the same: delete the leading
whitespace and press Tab. Most editors can be told to
keep tabs in Makefiles — and the surest defense is the
one in this guide's README: **type** your recipes.

**Check:** `cat -A Makefile` shows tabs as `^I`:

```text
$ cat -A Makefile
hello:$
^Iecho "Hello, Make!"$
```

If you see spaces instead of `^I` before a recipe line,
that's your bug.

## Naming the file

Make looks for `GNUmakefile`, then `makefile`, then
`Makefile`, in that order, and uses the first one it
finds. `Makefile` with a capital M is the convention.
To use a different name:

```text
$ make -f my-rules.mk
```

# Automatic Variables

**Scope:** the `$@`, `$<`, `$^` shortcuts — what they
expand to, and the `@` that quiets echoing.

## The big three

Inside a recipe, Make sets variables automatically for
the rule being run:

| Variable | Expands to | Mnemonic |
|---|---|---|
| `$@` | the target | "target" — think `@` as in "at the target" |
| `$<` | the first prerequisite | "first" |
| `$^` | all prerequisites | "all" |

The link rule from the C build:

```make
hello: main.o greet.o
	$(CC) $(CFLAGS) -o $@ $^
```

`$@` is `hello`, `$^` is `main.o greet.o`. The recipe
says what it means without repeating the filenames —
rename the target or add an object file, and the recipe
still works.

`$<` shines in compile rules, where only one source file
is compiled at a time:

```make
main.o: main.c greet.h
	$(CC) $(CFLAGS) -c $< -o $@
```

`$<` is `main.c` (the first prerequisite), `$@` is
`main.o`. Using `$^` here would be wrong — it would try
to compile the header as a source file.

## The rest of the family

- `$?` — prerequisites *newer* than the target. Handy
  for "archive only what changed" recipes.
- `$(@D)` / `$(@F)` — directory and file parts of the
  target. `$(@D)` of `build/hello` is `build`.
- `$*` — the stem in pattern rules (see [Pattern
  Rules](pattern-rules.md)): for `%.o: %.c` building
  `main.o`, `$*` is `main`.

## Quieting the echo

A recipe line starting with `@` isn't echoed before it
runs:

```make
clean:
	rm -f hello *.o
	@echo "cleaned"
```

```text
$ make clean
rm -f hello *.o
cleaned
```

The `rm` line echoes (no `@`); the `@echo` line doesn't.
Use `@` for chatter, leave it off the real commands so
readers can see what's actually running.

**Check:** if a recipe misbehaves, remove the `@` (or
run `make -n`) to see the exact command.

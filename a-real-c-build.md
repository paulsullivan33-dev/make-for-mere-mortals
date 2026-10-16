# A Real C Build

**Scope:** the canonical multi-file example, end to
end — every command below was actually run.

## The program

Three small files. `greet.h` declares a function,
`greet.c` defines it, `main.c` calls it:

```c
/* greet.h */
#ifndef GREET_H
#define GREET_H
void greet(const char *name);
#endif
```

```c
/* greet.c */
#include <stdio.h>
#include "greet.h"

void greet(const char *name)
{
    printf("Hello, %s! Built with Make.\n", name);
}
```

```c
/* main.c */
#include "greet.h"

int main(void)
{
    greet("Mere Mortal");
    return 0;
}
```

## The Makefile

```make
CC = gcc
CFLAGS = -Wall -Wextra -g

.PHONY: all clean
all: hello

hello: main.o greet.o
	$(CC) $(CFLAGS) -o $@ $^

%.o: %.c greet.h
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f hello *.o
```

Every idea from the previous chapters is here:
variables for the compiler and flags, [automatic
variables](automatic-variables.md) (`$@`, `$<`, `$^`),
one [pattern rule](pattern-rules.md) for both object
files, and [`.PHONY`](phony-targets.md) for the task
targets.

## The build

```text
$ make
gcc -Wall -Wextra -g -c main.c -o main.o
gcc -Wall -Wextra -g -c greet.c -o greet.o
gcc -Wall -Wextra -g -o hello main.o greet.o
$ ./hello
Hello, Mere Mortal! Built with Make.
```

Run `make` again and nothing happens — `make: 'hello'
is up to date.` Edit the header, and Make rebuilds both
object files and relinks, because the pattern rule
lists `greet.h` as a prerequisite:

```text
$ touch greet.h
$ make
gcc -Wall -Wextra -g -c main.c -o main.o
gcc -Wall -Wextra -g -c greet.c -o greet.o
gcc -Wall -Wextra -g -o hello main.o greet.o
```

## Why the header is listed

The pattern rule says `%.o: %.c greet.h`. Both object
files depend on the header, so a header change
rebuilds everything that includes it. Forget the
header, and Make happily links stale object files
against your new declarations — the classic "but I
changed it!" bug. (Large projects generate these
header dependencies automatically with `gcc -MM`; look
it up when your project outgrows a hand-written list.)

**Check:** `make clean && make` rebuilds from scratch.
If any step fails, the error names the file and line —
C errors are refreshingly specific compared to
Makefile errors.

# Make for Mere Mortals

**Make** is the 50-year-old build tool that still runs the
world. You describe what depends on what, and Make figures
out the minimum work needed: recompile only the files that
changed, skip everything else. It was built for compiling C
in 1976, but the idea — *don't redo work that's already
done* — turns out to be useful for almost any repetitive
chore: backups, reports, deployments, data pipelines.

This guide assumes you can use a terminal and have heard of
a compiler. Everything Make adds on top — targets,
prerequisites, recipes, variables, pattern rules — is
explained from zero, with every example actually run.

## What this guide covers

- [**What Make is**](what-make-is.md) — the one-idea pitch,
  and what Make is *not*
- [**Your first Makefile**](your-first-makefile.md) —
  targets, prerequisites, recipes, and the famous tabs rule
- [**How Make decides what to do**](how-make-decides.md) —
  timestamps, "up to date," and "nothing to be done"
- [**Variables**](variables.md) — `=`, `:=`, `?=`, and
  overriding from the command line
- [**Automatic variables**](automatic-variables.md) — `$@`,
  `$<`, `$^`: the shortcuts that keep recipes short
- [**Pattern rules**](pattern-rules.md) — one rule for every
  `.c` → `.o` build
- [**.PHONY targets**](phony-targets.md) — tasks that aren't
  files, and the trap when you skip it
- [**A real C build**](a-real-c-build.md) — a small
  multi-file program, built the Make way
- [**Make for chores**](make-for-chores.md) — backups,
  checks, and reports: Make as a task runner
- [**Conditionals and functions**](conditionals-and-functions.md) —
  just enough `ifeq`, `wildcard`, and `$(shell ...)`
- [**Parallel builds**](parallel-builds.md) — `-j` and why
  your dependencies must be honest
- [**Debugging Makefiles**](debugging-makefiles.md) —
  `-n`, `--trace`, `-p`, and the undefined-variable warning
- [**Troubleshooting**](troubleshooting.md) — symptom → fix,
  with the real error messages
- [**Glossary**](glossary.md) — the terms, collected in one
  place

## What it doesn't cover

Autotools, CMake, and other build systems (Make's
successors and competitors), writing portable Makefiles
for every platform under the sun, and the full GNU Make
manual — which is excellent and enormous. This guide is
about the 20% of Make that does 80% of the work.

## How to use this guide

Every Makefile in these chapters was run with GNU Make
4.3 — **Check:** blocks show what success looks like. The
tabs rule means you should *type* the recipes, not
copy-paste them: editors and web pages love to turn tabs
into spaces, and Make will reject spaces with the famous
`missing separator` error. Typing teaches the shape.

## Validation notes

- GNU Make 4.3 on Linux; `gcc` 13.3.0 for the C examples.
- Command outputs shown are the actual outputs.
- Quoted error messages in [Troubleshooting](troubleshooting.md)
  were reproduced with GNU Make 4.3 unless marked otherwise.
- Version-sensitive facts were verified in October 2026;
  treat them as "check again in a year."

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [C for Mere Mortals](https://paulsullivan33-dev.github.io/c-for-mere-mortals/)
- [Bash for Mere Mortals](https://paulsullivan33-dev.github.io/bash-for-mere-mortals/)
- [GitHub Actions for Mere Mortals](https://paulsullivan33-dev.github.io/github-actions-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/make-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/make-for-mere-mortals/blob/main/LICENSE-CODE).

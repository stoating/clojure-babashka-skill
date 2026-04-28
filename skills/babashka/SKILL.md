---
name: babashka
description: Use when working with Babashka - the fast-starting native Clojure interpreter and scripting runtime. Activate when the user asks about bb scripts, bb.edn tasks, babashka tasks, shell scripting in Clojure, built-in namespaces, pods, babashka.process, babashka.fs, babashka.deps, command-line CLIs, shebang scripts, SCI compatibility, classpath/deps issues, or replacing Bash scripts with Clojure.
version: 1.0.0
---

# Babashka

Babashka (`bb`) is a native Clojure interpreter for scripting with fast startup. Use it when the task looks like shell scripting, project automation, CI glue, small CLIs, file/process automation, or quick data transformation where JVM Clojure startup would be too heavy.

## Quick Decision: What do you need?

| Task | Go to |
|------|-------|
| Write an executable script, parse args, read stdin, print output | [scripts.md](scripts.md) |
| Add or fix `bb.edn` tasks, task dependencies, `shell`, `clojure`, `run` | [tasks.md](tasks.md) |
| Use built-in namespaces, add deps/paths/classpath, load pods | [deps-and-pods.md](deps-and-pods.md) |
| Work with files, processes, HTTP, JSON/EDN, shell commands, portability | [interop.md](interop.md) |
| Something differs from JVM Clojure, is slow, cannot load, or behaves oddly | [anti-patterns.md](anti-patterns.md) |

## Core Mental Model

Babashka runs Clojure through SCI, not JVM bytecode compilation. It starts quickly and ships as a self-contained native binary with a curated set of Clojure namespaces, useful libraries, and selected Java classes. It can interpret compatible Clojure source from the classpath, but it cannot load arbitrary Java classes at runtime.

Choose the form based on audience:

- One-off expression: `bb -e '(println (+ 1 2 3))'`
- Pipeline transform: `bb -i -o '(map clojure.string/upper-case *input*)'`
- Executable script: `#!/usr/bin/env bb`
- Project automation: `bb.edn` with `:tasks`
- External capability not built in: dependency, classpath, CLI subprocess, or pod

Useful defaults:

- `*command-line-args*` contains script or task arguments.
- `*in*`, `*out*`, and `*err*` work like Clojure.
- `babashka.fs` is the preferred cross-platform filesystem API.
- `babashka.process` is the preferred subprocess API for non-trivial process work.
- Use `clojure.tools.cli` for option parsing when scripts grow beyond positional args.

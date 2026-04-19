# Agent instructions

This repository contains the **`babashka`** skill - a structured set of Markdown files that teach an AI coding agent how to work with Babashka, the fast-starting native Clojure interpreter for scripts, project automation, and small CLIs.

**Any agent that understands this `AGENTS.md` convention should:**

1. Treat `babashka/SKILL.md` as the entry point - it contains a decision table pointing to the right reference file for the task.
2. Load reference files on demand based on that table:
   - `scripts.md` - executable `.bb`/`.clj` scripts, command-line args, stdin/stdout, CLI parsing
   - `tasks.md` - `bb.edn`, `:tasks`, dependencies, `shell`, `clojure`, `run`, docs, and task organization
   - `deps-and-pods.md` - built-in namespaces, `:deps`, `:paths`, `BABASHKA_CLASSPATH`, `babashka.deps`, and pods
   - `interop.md` - file/process/HTTP/JSON/EDN/shell interop and cross-platform scripting patterns
   - `anti-patterns.md` - SCI limitations, portability traps, slow workloads, dependency mistakes, and unsafe shelling
3. Prefer `bb.edn` tasks for project automation and standalone executable scripts for user-facing CLIs.
4. Check whether a namespace or Java class is built into Babashka before adding dependencies or suggesting JVM-only code.
5. Use `babashka.process` or task `shell` intentionally for subprocesses; avoid string-concatenated shell commands when arguments can be passed as data.

This file follows the [agents.md](https://agents.md/) convention and is honored by OpenAI Codex CLI, Cursor, Aider, Zed, Amp, Gemini CLI, Google Jules, Windsurf, Factory, RooCode, and many others.

For Claude Code, the richer native format is `.claude-plugin/` + `babashka/SKILL.md`.

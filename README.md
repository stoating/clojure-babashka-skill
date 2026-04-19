# Clojure Babashka Skill

A structured Markdown skill that teaches AI coding agents how to work with Babashka - the fast-starting native Clojure interpreter for scripts, project automation, and small CLIs. Covers executable scripts, `bb.edn` tasks, built-in namespaces, deps, pods, subprocess interop, filesystem work, command-line parsing, and SCI compatibility.

Built in [Claude Code's Agent Skill format](https://github.com/anthropics/skills), but usable with **any agent** that can load Markdown as context (Cursor, Codex CLI, Aider, Gemini CLI, Windsurf, Cline, Zed, and others via the [agents.md](https://agents.md/) convention).

---

## Read this before installing anything

**Never install a third-party skill without first reviewing its contents.**

Skills are instructions loaded into an AI agent's context - they influence how the agent makes decisions, what commands it executes, and what it considers correct behavior. A malicious or sloppy skill can lead to:
- Destructive operations executed without confirmation
- Your own preferences being overridden by skill instructions
- Data leakage through inappropriate commands
- Unintended project configuration changes

**Before installing:**
1. Read every `.md` file in the `babashka/` directory
2. Ask your agent to perform a security review
3. Only then install

## Installation

### Claude Code via plugin marketplace

```text
/plugin marketplace add stoating/clojure-babashka-skill
/plugin install clojure-babashka@clojure-babashka-skill
```

The skill is then discovered automatically. Restart the session if it is not picked up immediately. Once installed, invoke it with:

```text
/babashka
```

To update later:

```text
/plugin marketplace update clojure-babashka-skill
```

### Claude Code via the stoating marketplace

```text
/plugin marketplace add stoating/plugins
/plugin install clojure-babashka@stoating
```

### Manual or Other Agents

Agents that honor `AGENTS.md` can use this repository directly. Otherwise attach or load `babashka/SKILL.md`, then load the referenced Markdown files as needed.

## Repository layout

```text
.
├── .claude-plugin/
│   ├── marketplace.json
│   └── plugin.json
├── AGENTS.md
├── README.md
└── babashka/
    ├── SKILL.md
    ├── scripts.md
    ├── tasks.md
    ├── deps-and-pods.md
    ├── interop.md
    └── anti-patterns.md
```

## What the skill covers

| File | Contents |
|---|---|
| `SKILL.md` | Entry point, decision table, core mental model |
| `scripts.md` | Shebang scripts, args, stdin/stdout, CLI parsing, exits |
| `tasks.md` | `bb.edn`, tasks, dependencies, `shell`, `clojure`, `run`, organization |
| `deps-and-pods.md` | Built-ins, deps, paths, classpath, runtime deps, pods |
| `interop.md` | Filesystem, processes, data formats, HTTP, portability |
| `anti-patterns.md` | SCI limits, dependency mistakes, shell safety, task complexity |

## Sources

- [babashka/babashka](https://github.com/babashka/babashka) - source, README, docs, tests, and examples
- [Babashka book](https://book.babashka.org)
- [Babashka pods](https://github.com/babashka/pods)

## License

See [LICENSE](LICENSE).

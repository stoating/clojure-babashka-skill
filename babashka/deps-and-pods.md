# Deps and Pods

## Built-In First

Before adding a dependency, check whether Babashka already includes the namespace or feature. Common built-ins include filesystem, process, JSON/EDN, CLI parsing, HTTP client/server options, XML/YAML/transit support depending on build features, Hiccup, core.async support, and many Clojure standard namespaces.

Use built-ins for scripts whenever possible. They avoid startup/network/cache surprises and keep scripts portable.

## `bb.edn` Deps and Paths

Project-local dependencies and source paths belong in `bb.edn`:

```clojure
{:paths ["src" "script"]
 :deps {medley/medley {:mvn/version "1.4.0"}}
 :tasks
 {run (shell "bb" "script/main.bb")}}
```

Babashka uses Clojure deps coordinates for Maven, git, and local deps where compatible.

`--classpath` overrides the classpath from `bb.edn`. Use it deliberately for fully controlled invocations:

```shell
bb --classpath src -e '(require (quote my.ns))'
```

`BABASHKA_CLASSPATH` can inject a classpath from the environment:

```shell
export BABASHKA_CLASSPATH="$(clojure -Spath)"
bb script.clj
```

## Runtime Deps

Use `babashka.deps/add-deps` when a script needs to add deps programmatically:

```clojure
(require '[babashka.deps :as deps])

(deps/add-deps
 '{:deps {medley/medley {:mvn/version "1.4.0"}}})

(require '[medley.core :as m])
```

Prefer static `bb.edn` deps for project automation. Runtime deps are useful for self-contained scripts, demos, and tools that construct deps dynamically.

## Compatible Libraries

Babashka can interpret compatible Clojure source from the classpath. It cannot load arbitrary Java classes at runtime, and not every JVM Clojure library works unchanged.

Before adding a dependency:

1. Check whether it is pure Clojure or Babashka-compatible.
2. Check whether it requires unsupported Java classes, bytecode generation, `deftype`, `gen-class`, or JVM-only internals.
3. Prefer a built-in namespace, small wrapper over an external CLI, or pod when those fit better.

## Pods

Pods are external programs used like libraries from Babashka. Use them when functionality depends on native code, heavy JVM-only libraries, databases, or APIs that do not belong in the main Babashka binary.

Typical shape:

```clojure
(require '[babashka.pods :as pods])

(pods/load-pod "pod-name")
(require '[pod.vendor.namespace :as api])
```

Prefer version-pinned pods for repeatable scripts when the pod supports it.

## Printing Deps

Use `bb print-deps` to inspect the merged deps Babashka sees. `bb print-deps --format classpath` is useful when debugging classpath composition.

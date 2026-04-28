# Interop

## Filesystem

Prefer `babashka.fs` over direct `java.io.File` for cross-platform scripts:

```clojure
(require '[babashka.fs :as fs])

(doseq [path (fs/list-dir ".")]
  (when (fs/regular-file? path)
    (println (fs/file-name path))))
```

`babashka.fs` works with paths, strings, and common filesystem operations while hiding many platform details.

## Processes

For simple commands in `bb.edn` tasks, use task `shell`. In scripts, use `babashka.process`:

```clojure
(require '[babashka.process :as p])

(let [proc (p/process ["git" "status" "--short"]
                      {:out :string
                       :err :inherit})]
  (print (:out @proc)))
```

Prefer vector command arguments when possible. They avoid quoting bugs and accidental shell injection.

Use `p/shell` when shell features are genuinely needed:

```clojure
(p/shell "printf '%s\n' *.clj")
```

## Data Formats

Babashka is well-suited for glue code that reads and writes structured data:

```clojure
(require '[clojure.edn :as edn]
         '[cheshire.core :as json])

(-> (slurp "config.edn")
    edn/read-string
    (assoc :generated true)
    json/generate-string
    println)
```

Use EDN for Clojure-native config and JSON/YAML/transit only when interoperating with external tools or APIs.

## HTTP

Babashka can use built-in HTTP client/server support when available in the installed build. For small servers, `org.httpkit.server` is commonly used in Babashka examples:

```clojure
(require '[org.httpkit.server :as server])

(def stop
  (server/run-server
    (fn [_] {:status 200 :body "ok"})
    {:port 8080}))
```

Always make long-running scripts explicit about ports, shutdown behavior, and signal handling.

## Portability

Babashka is cross-platform, but shell commands are not. When scripts need to run on Windows, macOS, and Linux:

- Prefer `babashka.fs` for file operations.
- Prefer `babashka.process` with argument vectors.
- Avoid `bash -c`, `sed`, `grep`, `find`, `xargs`, and POSIX-only flags unless the target environment guarantees them.
- Branch on the platform only at the smallest possible boundary.

## Environment

Use Java system/env APIs as in Clojure:

```clojure
(System/getenv "HOME")
(System/getProperty "user.dir")
```

When invoking subprocesses, pass env overrides through process options instead of mutating global process state.

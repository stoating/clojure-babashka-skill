# Scripts

## Executable Scripts

Use a shebang for scripts meant to be invoked directly:

```clojure
#!/usr/bin/env bb

(ns tool
  (:require [babashka.fs :as fs]
            [clojure.string :as str]))

(def dir (or (first *command-line-args*) "."))

(->> (fs/list-dir dir)
     (map fs/file-name)
     (sort)
     (str/join "\n")
     println)
```

Make the file executable outside the skill when needed:

```shell
chmod +x script.clj
```

Prefer `.bb` for Babashka-only scripts and `.clj` when the file is intentionally compatible with JVM Clojure too.

## Command-Line Args

For tiny scripts, destructure `*command-line-args*`:

```clojure
(let [[src dest] *command-line-args*]
  (when-not (and src dest)
    (binding [*out* *err*]
      (println "Usage: copy SRC DEST"))
    (System/exit 2))
  (fs/copy src dest {:replace-existing true}))
```

For flags and validation, use `clojure.tools.cli`, which Babashka commonly provides:

```clojure
(require '[clojure.tools.cli :refer [parse-opts]])

(def cli-options
  [["-p" "--port PORT" "Port" :default 8080 :parse-fn parse-long]
   ["-h" "--help"]])

(let [{:keys [options errors summary]} (parse-opts *command-line-args* cli-options)]
  (cond
    (:help options) (println summary)
    (seq errors) (do (binding [*out* *err*] (doseq [e errors] (println e)))
                     (System/exit 2))
    :else (println (:port options))))
```

## Stdin and Pipelines

Read raw stdin from `*in*`:

```clojure
(println (clojure.string/upper-case (slurp *in*)))
```

Babashka also supports pipeline-oriented flags. Use them for compact one-liners; use scripts when behavior needs validation or tests.

```shell
printf 'a\nb\n' | bb -i -o '(map clojure.string/upper-case *input*)'
```

## Exit Codes and Errors

Use `System/exit` for deliberate CLI status codes. Print usage/errors to `*err*`:

```clojure
(binding [*out* *err*]
  (println "Missing --file"))
(System/exit 2)
```

For programmer errors, ordinary exceptions are fine. For user-facing CLIs, catch expected failures and emit concise messages.

## Tests

Keep reusable script logic in functions so it can be tested by `clojure.test` or exercised from `bb.edn` tasks. Avoid putting all behavior at top level unless the script is genuinely tiny.

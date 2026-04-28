# Anti-Patterns

## Treating Babashka as Full JVM Clojure

Babashka uses SCI and a native image. It cannot load arbitrary Java classes at runtime and does not support every JVM Clojure feature.

Before suggesting JVM-oriented code, check for:

- Unsupported Java classes
- Bytecode generation
- `gen-class`
- `deftype` / `definterface`
- Unboxed math assumptions
- Libraries that depend on JVM internals

If the code needs full JVM behavior or long-running numeric performance, use JVM Clojure.

## Adding Dependencies Before Checking Built-Ins

Bad:

```clojure
{:deps {some/fs-wrapper {:mvn/version "..."}}}
```

For filesystem work, start with `babashka.fs`. For subprocesses, start with `babashka.process`. For CLI parsing, start with `clojure.tools.cli` if available. Built-ins make scripts faster and more portable.

## Shell String Construction

Bad:

```clojure
(p/shell (str "rm -rf " user-path))
```

Prefer data arguments and validate destructive paths:

```clojure
(p/process ["rm" "-rf" user-path] {:err :inherit})
```

For file deletion, prefer `babashka.fs/delete` or `delete-tree` after checking the target.

## Using Bashisms in Cross-Platform Scripts

Babashka does not replace the shell; it works inside existing shells. If a script must run on Windows too, avoid embedding POSIX-only command pipelines. Write the file/process logic in Clojure or isolate platform-specific commands.

## Putting Too Much Logic in `bb.edn`

Large forms inside `bb.edn` become hard to test and maintain. Move reusable logic to `script/` or `src/`, then call functions from tasks.

## Dynamic Task Graphs

Task dependencies are intentionally static. Do not try to pass arbitrary arguments through `:depends` or generate task names as a control-flow mechanism. Use functions for dynamic behavior.

## Ignoring Non-Zero Exit Codes

Task `shell` throws on non-zero exit by default. Keep that behavior for CI and automation. Use `:continue true` only when a failure is expected and handled.

## Premature Pods

Pods are useful, but they are an extra runtime artifact. Prefer a built-in namespace, compatible Clojure dependency, or external CLI subprocess when that is simpler and more reliable for the deployment environment.

## Slow Hot Loops

Babashka startup is fast, but interpreted hot loops are slower than compiled JVM Clojure. If the script spends seconds in CPU-heavy loops, benchmark and consider JVM Clojure or moving the hot path to an external tool/library.

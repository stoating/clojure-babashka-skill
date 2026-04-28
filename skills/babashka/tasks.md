# Tasks

## `bb.edn`

Use `bb.edn` for project-local automation:

```clojure
{:paths ["src" "script"]
 :tasks
 {:requires ([babashka.fs :as fs])

  clean
  {:doc "Remove build output"
   :task (fs/delete-tree "target")}

  test
  {:doc "Run tests"
   :task (shell "bb" "test-runner.clj")}}}
```

Babashka keeps this separate from `deps.edn` so projects can use Babashka tasks even when they are not Clojure projects.

## Task Shapes

A task can be a form:

```clojure
{:tasks {hello (println "hello")}}
```

Or a map:

```clojure
{:tasks
 {lint {:doc "Run clj-kondo"
        :task (shell "clj-kondo" "--lint" "src" "test")}}}
```

Map tasks support useful keys:

- `:doc` - shown by `bb tasks` / task docs
- `:depends` - task names that run before this task
- `:task` - the form or function call to run
- `:requires` - task-local requires
- `:extra-paths` - paths added for the task
- `:extra-deps` - deps added for the task
- `:enter` / `:leave` - hooks around task execution
- `:private true` or a leading `-` in the name - hide helper tasks from listings

## Dependencies

Use `:depends` for static task graphs:

```clojure
{:tasks
 {clean (fs/delete-tree "target")
  build {:depends [clean]
         :task (shell "clojure" "-T:build" "jar")}}}
```

Task dependencies do not receive arguments from the dependent task. If a dependency needs dynamic behavior, move the behavior into a function and call it from one task, or explicitly rebind `*command-line-args*` when that is truly intended.

## Shell and Clojure Helpers

Inside tasks, `shell` runs external commands with inherited stdio by default and throws on non-zero exit:

```clojure
{:tasks {fmt (shell "cljfmt" "fix")}}
```

Pass options as the first argument:

```clojure
{:tasks
 {git-branch
  (-> (shell {:out :string} "git" "branch" "--show-current")
      :out
      clojure.string/trim
      println)}}
```

Use `:continue true` or `:continue fn` only when non-zero exit is expected and handled.

Use `clojure` for invoking the Clojure CLI from a task:

```clojure
{:tasks {jar (clojure "-T:build" "jar")}}
```

## Calling Other Tasks

Use `run` when a task needs another task's result:

```clojure
{:tasks
 {version (-> (slurp "VERSION") clojure.string/trim)
  print-version (println (run 'version))}}
```

Avoid building dynamic task names unless the project really needs a task framework. Plain functions are easier to test.

## Organization

For larger projects, put reusable functions in `script/` or `src/` and list the path in `:paths`. Then keep `bb.edn` as orchestration:

```clojure
{:paths ["script"]
 :tasks
 {:requires ([tasks :as t])
  test t/test
  release {:depends [test]
           :task t/release}}}
```

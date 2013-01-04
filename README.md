# The Clojure Style Guide

This Clojure style guide recommends best practices so that real-world Clojure
programmers can write code that can be maintained by other real-world Clojure
programmers. A style guide that reflects real-world usage gets used, and a
style guide that holds to an ideal that has been rejected by the people it is
supposed to help risks not getting used at all &ndash; no matter how good it is.

The guide is separated into several sections of related rules. I've
tried to add the rationale behind the rules (if it's omitted I've
assumed that is pretty obvious).

I didn't come up with all the rules out of nowhere - they are mostly
based on my extensive career as a professional software engineer,
feedback and suggestions from members of the Clojure community and
various highly regarded Clojure programming resources, such as
["Clojure Programming"](http://www.clojurebook.com/)
and ["The Joy of Clojure"](http://joyofclojure.com/).

The guide is still a work in progress - some sections are missing,
others are incomplete, some rules are lacking examples, some rules
don't have examples that illustrate them clearly enough. In due time
these issues will be addressed - just keep them in mind for now.

You can generate a PDF or an HTML copy of this guide using
[Transmuter](https://github.com/TechnoGate/transmuter).

## Table of Contents

* [Source Code Layout & Organization](#source-code-layout--organization)
* [Syntax](#syntax)
* [Naming](#naming)
* [Comments](#comments)
* [Annotations](#annotations)
* [Exceptions](#exceptions)
* [Collections](#collections)
* [Strings](#strings)
* [Regular Expressions](#regular-expressions)
* [Macros](#macros)
* [Misc](#misc)

## Source Code Layout & Organization

> Nearly everybody is convinced that every style but their own is
> ugly and unreadable. Leave out the "but their own" and they're
> probably right... <br/>
> -- Jerry Coffin (on indentation)

* Use two **spaces** per indentation level. No hard tabs.

    ```Clojure
    ;; good
    (when something
      (something-else))

    ;; bad - four spaces
    (when something
        (something-else))
    ```

* Align vertically function arguments.

    ```Clojure
    ;; good
    (filter even?
            (range 1 10))
   
    ;; bad
    (filter even?
      (range 1 10))           
    ```
   
* Align let bindings and map keywords.

    ```Clojure
    ;; good
    (let [thing1 "some stuff"
          thing2 "other stuff"]
      {:thing1 thing1
       :thing2 thing2})
     
    ;; bad
    (let [thing1 "some stuff"
      thing2 "other stuff"]
      {:thing1 thing1
      :thing2 thing2})
    ```

* Indent each line of multi-line docstrings.

    ```Clojure
    ;; good
    (defn foo
      "Hello there. This is
      a multi-line docstring."
      []
      (bar))

    ;; bad
    (defn foo
      "Hello there. This is
    a multi-line docstring."
      []
      (bar))
    ```

* Use Unix-style line endings. (*BSD/Solaris/Linux/OSX users are covered by default,
  Windows users have to be extra careful.)
    * If you're using Git you might want to add the following
    configuration setting to protect your project from Windows line
    endings creeping in:

        ```bash
        $ git config --global core.autocrlf true
        ```

* Don't keep spaces around `(`, `)`, `[`, `]`, `{` and `}`.

* Limit the use of commas in collection literals.

    ```Clojure
    ;; bad
    {:name "Bruce Wayne", :alter-ego "Batman"}
    
    ;; good
    {:name "Bruce Wayne" :alter-ego "Batman"}
    
    ;; good and arguably a bit more readable
    {:name "Bruce Wayne"
     :alter-ego "Batman"}
    ```

* Place all trailing parentheses on a single line.

    ```Clojure
    ;; good
    (when something
      (something-else))

    ;; bad
    (when something
      (something-else)
    )
    ```

* Use empty lines between `def`s.

    ```Clojure
    (def x ...)
    
    (def y ...)
    ```

* Keep lines up to 80 characters.
* Avoid trailing whitespace.
* Use one file per namespace.
* Use underscores in filenames when namespaces contain dashes.
* Start every namespace with a comprehensive `ns` form, comprised of `import`s, `require`s, `refer`s and `use`s.

    ```Clojure
    (ns examples.ns
      (:refer-clojure :exclude [next replace remove])
      (:require (clojure [string :as string]
                         [set :as set])
                [clojure.java.shell :as sh])
      (:use (clojure zip xml))
      (:import java.util.Date 
               java.text.SimpleDateFormat
               (java.util.concurrent Executors
                                     LinkedBlockingQueue)))
    ```

* Avoid single-segment namespaces.

## Syntax

* Avoid the use of namespace-manipulating functions like `require` and
  `refer`. They are entirely unnecessary outside of a REPL
  environment.
* Use `declare` to enable forward references.
* Prefer higher-order functions like `map` to `loop/recur`.
* Use `when` instead of `(if ... (do ...)`.

    ```Clojure
    ;; good
    (when pred
      (foo)
      (bar))
      
    ;; bad
    (if pred
      (do
        (foo)
        (bar)))
    ```

* Use `if-let` instead of `let` + `if`.

    ```Clojure
    ;; good
    (if-let [result :foo]
      (do-something-with result)
      (do-something-else))

    ;; bad
    (let [result :foo]
      (if result
        (do-something-with result)
        (do-something-else)))
    ```    

* Use `when-let` instead of `let` + `when`.

    ```Clojure
    ;; good
    (when-let [result :foo]
      (do-something-with result)
      (do-something-else-with result))

    ;; bad
    (let [result :foo]
      (when result
        (do-something-with result)
        (do-something-else-with result)))
    ```    

* Use `if-not` instead of `(if (not ...) ...)`.

    ```Clojure
    ;; good
    (if-not (pred)
      (foo))
      
    ;; bad
    (if (not pred)
      (foo))
    ```

* Use `when-not` instead of `(when (not ...) ...)`.

    ```Clojure
    ;; good
    (when-not pred
      (foo)
      (bar))
      
    ;; bad
    (when (not pred)
      (foo)
      (bar))
    ```

* Don't wrap functions in anonymous functions when you don't need to.

    ```Clojure
    ;; good
    (filter even? (range 1 10))
    
    ;; bad
    (filter #(even? %) (range 1 10))
    ```

* Favor the use of `complement` versus the use of an anonymous function. 

    ```Clojure
    ;; good
    (filter (complement some-pred?) coll)
    
    ;; bad
    (filter #(not (some-pred? %)) coll)
    ```

    This rule should obviously be ignored if the complementing predicate
    exists in the form of a separate function (e.g. `even?` and `odd?`).
    
* Leverage `comp` when it would yield simpler code.

    ```Clojure
    ;; good
    (map #(capitalize (trim %)) ["top " " test "])

    ;; better
    (map (comp capitalize trim) ["top " " test "])
    ```
    
* Leverage `partial` when it would yield simpler code.

    ```Clojure
    ;; good
    (map #(+ 5 %) (range 1 10))
    
    ;; (arguably) better
    (map (partial + 5) (range 1 10))
    ```

## Naming

> The only real difficulties in programming are cache invalidation and
> naming things. <br/>
> -- Phil Karlton

* Use `lisp-case` for function and variable names.
* Use `CamelCase` for protocols, records, structs and types. (Keep acronyms like HTTP,
  RFC, XML uppercase.)
* The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `even?`).
* The names of functions/macros that are not safe in STM transactions
  should end with an exclamation mark. (i.e. `reset!`)
* Use `*earmuffs*` for things intended for rebinding.
* Don't use a special notation for constants; everything is assumed a constant
  unless specified otherwise.
* Use `_` for destructuring targets and formal arguments names whose
  value will be ignored by the code at hand.
* Follow `clojure.core`'s example for idiomatic names like `pred` and `coll`.
    * in functions:
        * `f`, `g`, `h` - function input
        * `n` - integer input usually a size
        * `index` - integer index
        * `x`, `y` - numbers
        * `s` - string input
        * `coll` - a collection
        * `pred` - a predicate closure
        * `& more` - variadic input
    * in macros:
        * `expr` - an expression
        * `body` - a macro body
        * `binding` - a macro binding vector

## Macros

* Don't write a macro if a function will do.
* Create an example of a macro usage first and the macro afterwards.
* Break complicated macros into smaller functions whenever possible.
* Prefer syntax quoted forms over building lists manually.

## Comments

> Good code is its own best documentation. As you're about to add a
> comment, ask yourself, "How can I improve the code so that this
> comment isn't needed?" Improve the code and then document it to make
> it even clearer. <br/>
> -- Steve McConnell

* Write self-documenting code and ignore the rest of this section. Seriously!
* Comments longer than a word are capitalized and use punctuation. Use [one
  space](http://en.wikipedia.org/wiki/Sentence_spacing) after periods.
* Avoid superfluous comments.

    ```Clojure
    ;; bad
    (inc counter) ; increments counter by one
    ```

* Keep existing comments up-to-date. An outdated is worse than no comment
at all.

> Good code is like a good joke - it needs no explanation. <br/>
> -- Russ Olsen

* Avoid writing comments to explain bad code. Refactor the code to
  make it self-explanatory. (Do or do not - there is no try. --Yoda)

## Annotations

* Annotations should usually be written on the line immediately above
  the relevant code.
* The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
* If multiple lines are required to describe the problem, subsequent
  lines should be indented two spaces after the `;;`.

    ```Clojure
    (defn some-fun
      []
      ;; FIXME: This has crashed occasionally since v1.2.3. It may
      ;; be related to the BarBazUtil upgrade.
      (baz))
    ```

* In cases where the problem is so obvious that any documentation would
  be redundant, annotations may be left at the end of the offending line
  with no note. This usage should be the exception and not the rule.

    ```Clojure
    (defn bar
      []
      (sleep 100)) ; OPTIMIZE
    ```

* Use `TODO` to note missing features or functionality that should be
  added at a later date.
* Use `FIXME` to note broken code that needs to be fixed.
* Use `OPTIMIZE` to note slow or inefficient code that may cause
  performance problems.
* Use `HACK` to note "code smells" where questionable coding practices
  were used and should be refactored away.
* Use `REVIEW` to note anything that should be looked at to confirm it
  is working as intended. For example: `REVIEW: Are we sure this is how the
  client does X currently?`
* Use other custom annotation keywords if it feels appropriate, but be
  sure to document them in your project's `README` or similar.

## Misc

* Avoid functions longer than 10 LOC (lines of code). Ideally, most
  functions will be shorter than 5 LOC. Empty lines do not contribute
  to the relevant LOC.
* Avoid parameter lists longer than three or four parameters.
* Code in a functional way, avoiding mutation when that makes sense.
* Avoid more than three levels of block nesting.
* Be consistent. In an ideal world, be consistent with these guidelines.
* Use common sense.

# Contributing

Nothing written in this guide is set in stone. It's my desire to work
together with everyone interested in Clojure coding style, so that we could
ultimately create a resource that will be beneficial to the entire Clojure
community.

Feel free to open tickets or send pull requests with improvements. Thanks in
advance for your help!

# Spread the Word

A community-driven style guide is of little use to a community that
doesn't know about its existence. Tweet about the guide, share it with
your friends and colleagues. Every comment, suggestion or opinion we
get makes the guide just a little bit better. And we want to have the
best possible guide, don't we?

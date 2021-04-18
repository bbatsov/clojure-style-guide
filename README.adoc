= The Clojure Style Guide
:idprefix:
:idseparator: -
:sectanchors:
:sectlinks:
:toclevels: 1
ifndef::backend-pdf[]
:toc-title: pass:[<h2>Table of Contents</h2>]
endif::[]
:source-highlighter: rouge

== Introduction

[quote, Officer Alex J. Murphy / RoboCop]
____
Role models are important.
____

ifdef::env-github[]
TIP: You can find a beautiful version of this guide with much improved navigation at https://guide.clojure.style.
endif::[]

This Clojure style guide recommends best practices so that real-world Clojure
programmers can write code that can be maintained by other real-world Clojure
programmers. A style guide that reflects real-world usage gets used, and a
style guide that holds to an ideal that has been rejected by the people it is
supposed to help risks not getting used at all -- no matter how good it is.

The guide is separated into several sections of related rules. I've
tried to add the rationale behind the rules (if it's omitted, I've
assumed that it's pretty obvious).

I didn't come up with all the rules out of nowhere; they are mostly
based on my extensive career as a software engineer,
my work on many Clojure open-source projectsfootnote:[CIDER, nREPL, Orchard, etc.],
feedback and suggestions from numerous members of the Clojure community, and
various highly regarded Clojure programming resources, such as
https://www.clojurebook.com/["Clojure Programming"]
and https://joyofclojure.com/["The Joy of Clojure"].

Nothing written here is set in stone.
This style guide evolves over time as additional conventions are
identified and past conventions are rendered obsolete by changes in
Clojure itself.

NOTE: Clojure's developers also maintain a list of
https://clojure.org/community/contrib_howto#_coding_guidelines[coding
guidelines for libraries].footnote:[Those guidelines are meant to
be applied to Clojure itself and to all the Clojure Contrib libraries.]
They were one of the sources of inspiration for the document, you're
currently reading.

ifdef::env-github[]
You can generate a PDF copy of this guide using https://asciidoctor.org/docs/asciidoctor-pdf/[AsciiDoctor PDF], and an HTML copy https://asciidoctor.org/docs/convert-documents/#converting-a-document-to-html[with] https://asciidoctor.org/#installation[AsciiDoctor] using the following commands:

[source,shell]
----
# Generates README.pdf
asciidoctor-pdf -a allow-uri-read README.adoc

# Generates README.html
asciidoctor
----

[TIP]
====
Install the `rouge` gem to get nice syntax highlighting in the generated document.

[source,shell]
----
gem install rouge
----
====
endif::[]

=== Guiding Principles

[quote, Harold Abelson, Structure and Interpretation of Computer Programs]
____
Programs must be written for people to read, and only incidentally for machines to execute.
____

It's common knowledge that code is read much more often than it is written.
The guidelines provided here are intended to improve the readability of code and make it consistent across the wide spectrum of Clojure code.
They are also meant to reflect real-world usage of Clojure instead of a random ideal. When I had to choose between a very established practice
and a subjectively better alternative I've opted to recommend the established practice.footnote:[Occasionally I might suggest to the reader to consider some alternatives, though.]

There are some areas in which there is no clear consensus in the Clojure community regarding a particular style (like semantic indentation vs fixed indentation, semantic comments vs uniform comments, etc).
In such scenarios all popular styles are acknowledged and it's up to you to pick one and apply it consistently.

Fortunately Clojure is a Lisp, and Lisps are fundamentally
simple. Even though this guide was created a few years after Clojure
(I published the first version in early 2013), you could see that most
Clojure code in the wild was fairly uniform. I attribute this to
both the simplicity I already mentioned and to the fact that since day
1 Clojurists adopted many of the style conventions of other
established Lisp dialects (e.g. Common Lisp and Scheme).  This made
the work on this guide fairly easy and straight-forward, especially compared to
the massive exercise in frustration that was the
https://rubystyle.guide[Community Ruby Style Guide].footnote:[You'll notice that the Clojure style guide is pretty similar in structure to the Ruby style guide, which served as its main source of inspiration. You'll also notice that the Ruby style guide is much longer, mostly because of the complexity of the Ruby language.]

Clojure is famously optimized for simplicity and clarity. I'd like to believe that this guide is going to help you optimize for maximum
simplicity and clarity.

=== A Note About Consistency

[quote, Ralph Waldo Emerson]
____
A foolish consistency is the hobgoblin of little minds, adored by little statesmen and philosophers and divines.
____

A style guide is about consistency.footnote:[This section is heavily inspired by Python's PEP-8]
Consistency with this style guide is important.
Consistency within a project is more important.
Consistency within one class or method is the most important.

However, know when to be inconsistent -- sometimes style guide recommendations just aren't applicable. When in doubt, use your best judgment.
Look at other examples and decide what looks best. And don't hesitate to ask!

In particular: do not break backwards compatibility just to comply with this guide!

Some other good reasons to ignore a particular guideline:

* When applying the guideline would make the code less readable, even for someone who is used to reading code that follows this style guide.
* To be consistent with surrounding code that also breaks it (maybe for historic reasons) -- although this is also an opportunity to clean up someone else's mess (in true XP style).
* Because the code in question predates the introduction of the guideline and there is no other reason to be modifying that code.
* When the code needs to remain compatible with older versions of Clojure that don't support the feature recommended by the style guide.

=== Translations

Translations of the guide are available in the following languages:

* https://github.com/geekerzp/clojure-style-guide/blob/master/README-zhCN.md[Chinese]
* https://github.com/totakke/clojure-style-guide/blob/ja/README.adoc[Japanese]
* https://github.com/kwakbab/clojure-style-guide/blob/master/README-koKO.md[Korean]
* https://github.com/theSkilled/clojure-style-guide/blob/pt-BR/README.md[Portuguese] (Under progress)
* https://github.com/Nondv/clojure-style-guide/blob/master/ru/README.md[Russian]
* https://github.com/jeko2000/clojure-style-guide/blob/master/README.md[Spanish]
* https://github.com/LeaveNhA/clojure-style-guide/blob/master/README.adoc[Turkish]

NOTE: These translations are not maintained by our editor team, so their quality
and level of completeness may vary. The translated versions of the guide often
lag behind the upstream English version.

== Source Code Layout & Organization

[quote, Jerry Coffin (on indentation)]
____
Nearly everybody is convinced that every style but their own is
ugly and unreadable. Leave out the "but their own" and they're
probably right...
____

[#80-character-limits]
=== Maximum Line Length [[line-length]]

Where feasible, avoid making lines longer than 80 characters.

.Why Bother with 80 characters in a World of Modern Widescreen Displays?
****

A lot of people these days feel that a maximum line length of 80 characters is
just a remnant of the past and makes little sense today. After all - modern
displays can easily fit 200+ characters on a single line.  Still, there are some
important benefits to be gained from sticking to shorter lines of code.

First, and foremost - numerous studies have shown that humans read much faster
vertically and very long lines of text impede the reading process. As noted
earlier, one of the guiding principles of this style guide is to optimize the
code we write for human consumption.

Additionally, limiting the required editor window width makes it possible to
have several files open side-by-side, and works well when using code review
tools that present the two versions in adjacent columns.

The default wrapping in most tools disrupts the visual structure of the code,
making it more difficult to understand. The limits are chosen to avoid wrapping
in editors with the window width set to 80, even if the tool places a marker
glyph in the final column when wrapping lines. Some web based tools may not
offer dynamic line wrapping at all.

Some teams strongly prefer a longer line length. For code maintained exclusively
or primarily by a team that can reach agreement on this issue, it is okay to
increase the line length limit up to 100 characters, or all the way up
to 120 characters. Please, restrain the urge to go beyond 120 characters.
****

=== Tabs vs Spaces [[spaces]]

Use *spaces* for indentation. No hard tabs.

=== Body Indentation [[body-indentation]]

Use 2 spaces to indent the bodies of
forms that have body parameters.  This covers all `def` forms, special
forms and macros that introduce local bindings (e.g. `loop`, `let`,
`when-let`) and many macros like `when`, `cond`, `+as->+`, `+cond->+`, `case`,
`with-*`, etc.

[source,clojure]
----
;; good
(when something
  (something-else))

(with-out-str
  (println "Hello, ")
  (println "world!"))

;; bad - four spaces
(when something
    (something-else))

;; bad - one space
(with-out-str
 (println "Hello, ")
 (println "world!"))
----

=== Function Arguments Alignment [[vertically-align-fn-args]]

Vertically align function (macro) arguments spanning multiple lines.

[source,clojure]
----
;; good
(filter even?
        (range 1 10))

;; bad
(filter even?
  (range 1 10))
----

The reasoning behind this guideline is pretty simple - the arguments are
easier to process by the human brain if they stand out and stick together.

=== Function Arguments Indentation [[one-space-indent]]

NOTE: Generally, you should stick to the formatting outlined in the previous
guideline, unless you're limited by the available horizontal space.

Use a single space indentation for function (macro) arguments
when there are no arguments on the same line as the function name.

[source,clojure]
----
;; good
(filter
 even?
 (range 1 10))

(or
 ala
 bala
 portokala)

;; bad - two-space indent
(filter
  even?
  (range 1 10))

(or
  ala
  bala
  portokala)
----

This may appear like some weird special rule to people without Lisp background, but the
reasoning behind it is quite simple. Function calls are
nothing but regular list literals and normally those are aligned in the same way as
other collection type literals when spanning multiple lines:

[source,clojure]
----
;; list literal
(1
 2
 3)

;; vector literal
[1
 2
 3]

;; set literal
#{1
  2
  3}
----

Admittedly, list literals are not very common in Clojure, that's why it's understandable
that for many people lists are nothing but an invocation syntax.

As a side benefit, function arguments are still aligned in this scenario as well. They
just happen to accidentally be aligned with the function name as well.

.Semantic Indentation vs Fixed Indentation
****
The guidelines to indent differently macros with body forms from
all other macro and function calls are collectively known as
"semantic indentation". Simply put, this means that the code
is indented differently, so that the indentation would give the
reader of the code some hints about its meaning.

The downside of this approach is that requires Clojure code formatters to be
smarter. They either need to process `macro` arglists and rely on the fact
that people named their parameters consistently, or process some additional
indentation metadata.

Some people in the Clojure community have argued that's not worth it and
that everything should simply be indented in the same fashion. Here are
a few examples:

[source,clojure]
----
;;; Fixed Indentation
;;
;; macros
(when something
  (something-else))

(with-out-str
  (println "Hello, ")
  (println "world!"))

;; function call spanning two lines
(filter even?
  (range 1 10))

;; function call spanning three lines
(filter
  even?
  (range 1 10))
----

This suggestion has certainly gotten some ground in the community, but it also
goes against the entire Lisp tradition and the primary goal of this style guide -
namely to optimize code for human consumption.

There's also one small caveat with fixed indentation that's rarely discussed and that's
how to indent list literals, as function calls are simply list literals. As those
are not very common in Clojure, outside the context of providing structure for the Clojure
code itself, that matter is usually omitted from consideration:

[source,clojure]
----
;;; Fixed Indentation
;;
;; list literals
(1 2 3
  4 5 6)

(1
  2
  3
  4
  5
  6)
----

That looks a bit weird and happens to be inconsistent with how other collection types are normally indented.

****

=== Bindings Alignment [[bindings-alignment]]

Vertically align `let` (and `let`-like) bindings.

[source,clojure]
----
;; good
(let [thing1 "some stuff"
      thing2 "other stuff"]
  (foo thing1 thing2))

;; bad
(let [thing1 "some stuff"
  thing2 "other stuff"]
  (foo thing1 thing2))
----

=== Map Keys Alignment [[map-keys-alignment]]

Align vertically map keys.

[source,clojure]
----
;; good
{:thing1 thing1
 :thing2 thing2}

;; bad
{:thing1 thing1
:thing2 thing2}

;; bad
{:thing1 thing1
  :thing2 thing2}
----

=== Line Endings [[crlf]]

Use Unix-style line endings.footnote:[*BSD/Solaris/Linux/macOS users are
covered by default, Windows users have to be extra careful.]

[TIP]
====
If you're using Git you might want to add the following
configuration setting to protect your project from Windows line
endings creeping in:

[source,shell]
----
$ git config --global core.autocrlf true
----
====

=== Terminate Files With a Newline [[terminate-files-with-a-newline]]

End each file with a newline.

TIP: This should be done by through editor configuration, not manually.

=== Bracket Spacing [[bracket-spacing]]

If any text precedes an opening bracket(`(`, `{` and
`[`) or follows a closing bracket(`)`, `}` and `]`), separate that
text from that bracket with a space. Conversely, leave no space after
an opening bracket and before following text, or after preceding text
and before a closing bracket.

[source,clojure]
----
;; good
(foo (bar baz) quux)

;; bad
(foo(bar baz)quux)
(foo ( bar baz ) quux)
----

=== No Commas in Sequential Collection Literals [[no-commas-for-seq-literals]]

[quote, Alan Perlis]
____
Syntactic sugar causes semicolon cancer.
____

Don't use commas between the elements of sequential collection literals.

[source,clojure]
----
;; good
[1 2 3]
(1 2 3)

;; bad
[1, 2, 3]
(1, 2, 3)
----

=== Optional Commas in Map Literals [[opt-commas-in-map-literals]]

Consider enhancing the readability of map literals via judicious use
of commas and line breaks.

[source,clojure]
----
;; good
{:name "Bruce Wayne" :alter-ego "Batman"}

;; good and arguably a bit more readable
{:name "Bruce Wayne"
 :alter-ego "Batman"}

;; good and arguably more compact
{:name "Bruce Wayne", :alter-ego "Batman"}
----

=== Gather Trailing Parentheses [[gather-trailing-parens]]

Place all trailing parentheses on a single line instead of distinct lines.

[source,clojure]
----
;; good; single line
(when something
  (something-else))

;; bad; distinct lines
(when something
  (something-else)
)
----

=== Empty Lines Between Top-Level Forms [[empty-lines-between-top-level-forms]]

Use a single empty line between top-level forms.

[source,clojure]
----
;; good
(def x ...)

(defn foo ...)

;; bad
(def x ...)
(defn foo ...)

;; bad
(def x ...)


(defn foo ...)
----

An exception to the rule is the grouping of related ``def``s together.

[source,clojure]
----
;; good
(def min-rows 10)
(def max-rows 20)
(def min-cols 15)
(def max-cols 30)
----

=== No Blank Lines Within Definition Forms [[no-blank-lines-within-def-forms]]

Do not place blank lines in the middle of a function or
macro definition.  An exception can be made to indicate grouping of
pairwise constructs as found in e.g. `let` and `cond`, in case those don't
fit on the same line.

[source,clojure]
----
;; good
(defn fibo-iter
  ([n] (fibo-iter 0 1 n))
  ([curr nxt n]
   (cond
     (zero? n) curr
     :else (recur nxt (+' curr nxt) (dec n)))))

;; okay - the line break delimits a cond pair
(defn fibo-iter
  ([n] (fibo-iter 0 1 n))
  ([curr nxt n]
   (cond
     (zero? n)
     curr

     :else
     (recur nxt (+' curr nxt) (dec n)))))

;; bad
(defn fibo-iter
  ([n] (fibo-iter 0 1 n))

  ([curr nxt n]
   (cond
     (zero? n) curr

     :else (recur nxt (+' curr nxt) (dec n)))))
----

Occasionally, it might seem like a good idea to add a blank line
here and there in a longer function definition, but if you get to this
point you should also consider whether this long function isn't doing
too much and could potentially be broken down.

=== No Trailing Whitespace [[no-trailing-whitespace]]

Avoid trailing whitespace.

=== One File per Namespace [[one-file-per-namespace]]

Use one file per namespace and one namespace per file.

----
;; good
(ns foo.bar)

;; bad
(ns foo.bar)
(ns baz.qux)

;; bad
(in-ns quux.quuz)
(in-ns quuz.corge)

;; bad
(ns foo.bar) or (in-ns foo.bar) in multiple files
----

== Namespace Declaration

=== No Single Segment Namespaces [[no-single-segment-namespaces]]

Avoid single-segment namespaces.

[source,clojure]
----
;; good
(ns example.ns)

;; bad
(ns example)
----

Namespaces exist to disambiguate names. Using a single segment
namespace puts you in direct conflict with everyone else using single
segment namespaces, thus making it more likely you will conflict with
another code base.

In practice this means that libraries should never use single-segment
namespace to avoid namespace conflicts with other libraries.
Within your own private app of course, you can do whatever you like.

TIP: It's common practice to use the convention `domain.library-name`
or `library-name.core` for libraries with a single namespace in them.
Read on for more coverage of the namespace naming topic.

There are https://github.com/bbatsov/clojure-style-guide/pull/100[other reasons]
why might want to avoid single-segment namespaces, so you should
think long and hard before making any use of them.

=== Namespace Segments Limit [[namespace-segments-limit]]

Avoid the use of overly long namespaces (i.e., more than 5 segments).

=== Comprehensive `ns` Form [[comprehensive-ns-declaration]]

Start every namespace with a comprehensive `ns` form, comprised of
``refer``s, ``require``s, and ``import``s, conventionally in that order.

[source,clojure]
----
(ns examples.ns
  (:refer-clojure :exclude [next replace remove])
  (:require [clojure.string :as s :refer [blank?]])
  (:import java.util.Date))
----

=== Line Breaks in `ns` [[line-break-ns-declaration]]

When there are multiple dependencies, you may want give each one its own line.
This facilitates sorting, readability, and cleaner diffs for dependency changes.

[source,clojure]
----
;; better
(ns examples.ns
  (:require
   [clojure.string :as s :refer [blank?]]
   [clojure.set :as set]
   [clojure.java.shell :as sh])
  (:import
   java.util.Date
   java.text.SimpleDateFormat
   [java.util.concurrent Executors
                         LinkedBlockingQueue]))

;; good
(ns examples.ns
  (:require [clojure.string :as s :refer [blank?]]
            [clojure.set :as set]
            [clojure.java.shell :as sh])
  (:import java.util.Date
           java.text.SimpleDateFormat
           [java.util.concurrent Executors
                                 LinkedBlockingQueue]))

;; bad
(ns examples.ns
  (:require [clojure.string :as s :refer [blank?]] [clojure.set :as set] [clojure.java.shell :as sh])
  (:import java.util.Date java.text.SimpleDateFormat [java.util.concurrent Executors LinkedBlockingQueue]))
----

=== Prefer `:require` Over `:use` [[prefer-require-over-use]]

In the `ns` form prefer `:require :as` over `:require :refer` over `:require
:refer :all`.  Prefer `:require` over `:use`; the latter form should be
considered deprecated for new code.

[source,clojure]
----
;; good
(ns examples.ns
  (:require [clojure.zip :as zip]))

;; good
(ns examples.ns
  (:require [clojure.zip :refer [lefts rights]]))

;; acceptable as warranted
(ns examples.ns
  (:require [clojure.zip :refer :all]))

;; bad
(ns examples.ns
  (:use clojure.zip))
----

=== Sort Requirements and Imports [[sort-requirements-and-imports]]

In the `ns` form, sort your requirements and imports. This facilitates readability and avoids duplication, especially when the list of required / imported namespaces is very long.

[source,clojure]
----
;; good
(ns examples.ns
  (:require
   [baz.core :as baz]
   [clojure.java.shell :as sh]
   [clojure.set :as set]
   [clojure.string :as s :refer [blank?]]
   [foo.bar :as foo]))

;; bad
(ns examples.ns
  (:require
   [clojure.string :as s :refer [blank?]]
   [clojure.set :as set]
   [baz.core :as baz]
   [foo.bar :as foo]
   [clojure.java.shell :as sh]))
----

=== Use Idiomatic Namespace Aliases

Many core Clojure namespaces have idiomatic aliases that you're
encouraged to use within your projects - e.g. the most common way to
require `clojure.string` is: `[clojure.string :as str]`.

NOTE: This may appear to mask
`clojure.core.str`, but it doesn't. It's expected that
`clojure.core/str` and `clojure.string/*` to be used in a namespace as
`str` and `str/whatever` without conflict.

[source,clojure]
----
;; good
(ns ... (:require [clojure.string :as str] ...)

(str/join ...)

;; not as good - just be idiomatic and use as `str/`
(ns ... (:require [clojure.string :as string] ...)

(string/join ...)
----

Bellow are some common idiomatic aliases:

|===
| Namespace | Idiomatic Alias
| clojure.java.io
| io
| clojure.set
| set
| clojure.string
| str
| clojure.walk
| walk
| clojure.zip
| zip
| clojure.data.xml
| xml
| clojure.core.async
| as
| clojure.core.matrix
| mat
| clojure.edn
| edn
| clojure.pprint
| pp
| clojure.spec.alpha
| spec
| clojure.data.csv
| csv
| cheshire.core
| json
| java-time
| time
| clj-http.client
| http
| clojure.tools.logging
| log
| hugsql.core
| sql
| clj-yaml.core
| yaml
| clojure.java.shell
| sh
|===

=== A Recipe for Good Namespace Aliases

Above we covered a handful of popular namespaces and their idiomatic aliases.
You might have noticed that those are a bit inconsistent:

* `clojure.string` becomes `str`
* `clojure.pprint` becomes `pp`
* `clojure.walk` becomes `walk`
* `clojure.spec.alpha` becomes `spec`

I guess it's clear that the one thing they have in common is that they aim to be concise, but still carry some meaning (aliasing `clojure.walk` to `w` would
be concise, but won't carry much meaning).

But what to do about all the other namespaces out there that don't have idiomatic aliases? Well, you better be consistent in your approach to deriving aliases for them,
otherwise the people working on a shared Clojure codebase are going to experience a great deal of confusion. Here are a few rules that you should follow.footnote:[These guidelines are based on a https://stuartsierra.com/2015/05/10/clojure-namespace-aliases[blog post] by Stuart Sierra.]

1. Make the alias the same as the namespace name with the leading parts removed.

[source,clojure]
----
(ns com.example.application
  (:require
   [clojure.java.io :as io]
   [clojure.string :as string]))
----

2. Keep enough trailing parts to make each alias unique.

[source,clojure]
----
[clojure.data.xml :as data.xml]
[clojure.xml :as xml]
----

TIP: Yes, namespace aliases can have dots in them. Make good use of them.

3. Eliminate redundant words such as "core" and "clj" in aliases.

[source,clojure]
----
[clj-http :as http]
[clj-time.core :as time]
[clj-time.format :as time.format]
----

=== Use Consistent Namespace Aliases

Across a project, it's good to be consistent with namespace aliases; e.g., don't require `clojure.string` as `str` in one namespace but `string` in another.
If you follow the previous two guidelines you're basically covered, but if you opt for custom namespace aliasing scheme it's still important to apply it
consistently within your projects.

== Naming

[quote, Phil Karlton]
____
The only real difficulties in programming are cache invalidation and
naming things.
____

=== Namespace Naming Schemas [[naming-ns-naming-schemas]]

When naming namespaces favor the following two schemas:

* `project.module`
* `organization.project.module`

When you're following the `project.module` naming scheme and your project
has a single (implementation) namespace it's common to name it `project.core`.
Avoid the `project.core` name in all other cases, as more informative names
are always a better idea.

=== Composite Word Namespace Segments [[naming-namespace-composite-segments]]

Use `lisp-case` in composite namespace segments (e.g. `bruce.project-euler`).

NOTE: Many non-Lisp programming communities refer to `lisp-case` as
`kebab-case`, but we all know that Lisp has existed way before kebab
was invented.

=== Functions and Variables [[naming-functions-and-variables]]

Use `lisp-case` for function and variable names.

[source,clojure]
----
;; good
(def some-var ...)
(defn some-fun ...)

;; bad
(def someVar ...)
(defn somefun ...)
(def some_fun ...)
----

=== Protocols, Records, Structs and Types [[naming-protocols-records-structs-and-types]]

Use `CapitalCase` for protocols, records, structs, and types. (Keep
acronyms like HTTP, RFC, XML uppercase.)

NOTE: `CapitalCase` is also known as `UpperCamelCase, `CapitalWords`
and `PascalCase`.

=== Predicate Methods [[naming-predicates]]

The names of predicate methods (methods that return a boolean value)
should end in a question mark
(e.g., `even?`).

[source,clojure]
----
;; good
(defn palindrome? ...)

;; bad
(defn palindrome-p ...) ; Common Lisp style
(defn is-palindrome ...) ; Java style
----

=== Unsafe Functions [[naming-unsafe-functions]]

The names of functions/macros that are not safe in STM transactions
should end with an exclamation mark (e.g. `reset!`).

=== Conversion Functions [[naming-conversion-functions]]

Use `+->+` instead of `to` in the names of conversion functions.

[source,clojure]
----
;; good
(defn f->c ...)

;; not so good
(defn f-to-c ...)
----

=== Dynamic Vars [[naming-dynamic-vars]]

Use `*earmuffs*` for things intended for rebinding (ie. are dynamic).

[source,clojure]
----
;; good
(def ^:dynamic *a* 10)

;; bad
(def ^:dynamic a 10)
----

=== Constants [[naming-constants]]

Don't use a special notation for constants; everything is assumed a constant
unless specified otherwise.

[source,clojure]
----
;; good
(def max-size 10)

;; bad
(def MAX-SIZE 10) ; Java style
(def +max-size+ 10) ; Common Lisp style, global constant
(def *max-size* 10) ; Common Lisp style, global variable
----

NOTE: Famously `\*clojure-version*` defies this convention, but you should
treat this naming choice is a historical oddity and not as an example to
follow.

=== Unused Bindings [[naming-unused-bindings]]

Use `+_+` for destructuring targets and formal argument names whose
value will be ignored by the code at hand.

[source,clojure]
----
;; good
(let [[a b _ c] [1 2 3 4]]
  (println a b c))

(dotimes [_ 3]
  (println "Hello!"))

;; bad
(let [[a b c d] [1 2 3 4]]
  (println a b d))

(dotimes [i 3]
  (println "Hello!"))
----

However, when it can help the understanding of your code, it can be useful to explicitly name unused arguments or maps you're destructuring from. In this case, prepend the name with an underscore to explicitly signal that the variable is supposed to be unused.

[source,clojure]
----
;; good
(defn myfun1 [context _]
 (assoc context :foo "bar"))

(defn myfun2 [context {:keys [id]}]
 (assoc context :user-id id))

;; better
(defn myfun1 [context _user]
 (assoc context :foo "bar"))

(defn myfun2 [context {:keys [id] :as _user}]
 (assoc context :user-id id))
----

=== Idiomatic Names [[idiomatic-names]]

Follow ``clojure.core``'s example for idiomatic names like `pred` and `coll`.

* in functions:
 ** `f`, `g`, `h` - function input
 ** `n` - integer input usually a size
 ** `index`, `i` - integer index
 ** `x`, `y` - numbers
 ** `xs` - sequence
 ** `m` - map
 ** `s` - string input
 ** `re` - regular expression
 ** `sym` - symbol
 ** `coll` - a collection
 ** `pred` - a predicate closure
 ** `& more` - variadic input
 ** `xf` - xform, a transducer
 ** `ns` - namespacefootnote:[Technically this will shadow the `ns` macro, but it's extremely unlikely you'll ever need it in the body of a function.]
* in macros:
 ** `expr` - an expression
 ** `body` - a macro body
 ** `binding` - a macro binding vector
* in methods (when specified in `defprotocol`, `deftype`, `defrecord`, `reify`, etc):
 ** `this` - for the first argument, indicating a reference to the object - or alternatively, a consistent name which describes the object

== Functions

=== Optional New Line After Function Name [[optional-new-line-after-fn-name]]

Optionally omit the new line between the function name and argument
vector for `defn` when there is no docstring.

[source,clojure]
----
;; good
(defn foo
  [x]
  (bar x))

;; good
(defn foo [x]
  (bar x))

;; bad
(defn foo
  [x] (bar x))
----

=== Multimethod Dispatch Val Placement [[multimethod-dispatch-val-placement]]

Place the `dispatch-val` of a multimethod on the same line as the
function name.

[source,clojure]
----
;; good
(defmethod foo :bar [x] (baz x))

(defmethod foo :bar
  [x]
  (baz x))

;; bad
(defmethod foo
  :bar
  [x]
  (baz x))

(defmethod foo
  :bar [x]
  (baz x))
----

=== One-line Functions

Optionally omit the new line between the argument vector and a short
function body.

[source,clojure]
----
;; good
(defn foo [x]
  (bar x))

;; good for a small function body
(defn foo [x] (bar x))

;; good for multi-arity functions
(defn foo
  ([x] (bar x))
  ([x y]
   (if (predicate? x)
     (bar x)
     (baz x))))

;; bad
(defn foo
  [x] (if (predicate? x)
        (bar x)
        (baz x)))
----

=== Multiple Arity Indentation [[multiple-arity-indentation]]

Indent each arity form of a function definition vertically aligned with its
parameters.

[source,clojure]
----
;; good
(defn foo
  "I have two arities."
  ([x]
   (foo x 1))
  ([x y]
   (+ x y)))

;; bad - extra indentation
(defn foo
  "I have two arities."
  ([x]
    (foo x 1))
  ([x y]
    (+ x y)))
----

=== Multiple Arity Order [[multiple-arity-order]]

Sort the arities of a function
from fewest to most arguments. The common case of multi-arity
functions is that some K arguments fully specifies the function's
behavior, and that arities N < K partially apply the K arity, and
arities N > K provide a fold of the K arity over varargs.

[source,clojure]
----
;; good - it's easy to scan for the nth arity
(defn foo
  "I have two arities."
  ([x]
   (foo x 1))
  ([x y]
   (+ x y)))

;; okay - the other arities are applications of the two-arity
(defn foo
  "I have two arities."
  ([x y]
   (+ x y))
  ([x]
   (foo x 1))
  ([x y z & more]
   (reduce foo (foo x (foo y z)) more)))

;; bad - unordered for no apparent reason
(defn foo
  ([x] 1)
  ([x y z] (foo x (foo y z)))
  ([x y] (+ x y))
  ([w x y z & more] (reduce foo (foo w (foo x (foo y z))) more)))
----

=== Function Length [[function-length]]

Avoid functions longer than 10 LOC (lines of code). Ideally, most
functions will be shorter than 5 LOC.

=== Function Positional Parameters Limit [[function-positional-parameter-limit]]

Avoid parameter lists with more than three or four positional parameters.

=== Pre and Post Conditions [[pre-post-conditions]]

Prefer function pre and post conditions to checks inside a function's body.

[source,clojure]
----
;; good
(defn foo [x]
  {:pre [(pos? x)]}
  (bar x))

;; bad
(defn foo [x]
  (if (pos? x)
    (bar x)
    (throw (IllegalArgumentException. "x must be a positive number!")))
----

== Idioms

=== Dynamic Namespace Manipulation [[ns-fns-only-in-repl]]

Avoid the use of namespace-manipulating functions like `require` and
`refer`. They are entirely unnecessary outside of a REPL
environment.

=== Forward References [[forward-references]]

Avoid forward references.  They are occasionally necessary, but such occasions
are rare in practice.

=== Declare [[declare]]

Use `declare` to enable forward references when forward references are
necessary.

=== Higher-order Functions [[higher-order-fns]]

Prefer higher-order functions like `map` to `loop/recur`.

=== Vars Inside Functions [[dont-def-vars-inside-fns]]

Don't define vars inside functions.

[source,clojure]
----
;; very bad
(defn foo []
  (def x 5)
  ...)
----

=== Shadowing `clojure.core` Names [[dont-shadow-clojure-core]]

Don't shadow `clojure.core` names with local bindings.

[source,clojure]
----
;; bad - clojure.core/map must be fully qualified inside the function
(defn foo [map]
  ...)
----

=== Alter Var Binding [[alter-var]]

Use `alter-var-root` instead of `def` to change the value of a var.

[source,clojure]
----
;; good
(def thing 1) ; value of thing is now 1
; do some stuff with thing
(alter-var-root #'thing (constantly nil)) ; value of thing is now nil

;; bad
(def thing 1)
; do some stuff with thing
(def thing nil)
; value of thing is now nil
----

=== Nil Punning [[nil-punning]]

Use `seq` as a terminating condition to test whether a sequence is
empty (this technique is sometimes called _nil punning_).

[source,clojure]
----
;; good
(defn print-seq [s]
  (when (seq s)
    (prn (first s))
    (recur (rest s))))

;; bad
(defn print-seq [s]
  (when-not (empty? s)
    (prn (first s))
    (recur (rest s))))
----

=== Converting Sequences to Vectors [[to-vector]]

Prefer `vec` over `into` when you need to convert a sequence into a vector.

[source,clojure]
----
;; good
(vec some-seq)

;; bad
(into [] some-seq)
----

=== Converting Something to Boolean

Use the `boolean` function if you need to convert something to an actual boolean value (`true` or `false`).

[source,clojure]
----
;; good
(boolean (foo bar))

;; bad
(if (foo bar) true false)
----

NOTE: Don't forget that the only values in Clojure that are "falsey" are `false` and `nil`. Everything else
will evaluate to `true` when passed to the `boolean` function.

You'll rarely need an actual boolean value in Clojure, but it's useful to know how to obtain one when you do.

=== `when` vs `if` [[when-instead-of-single-branch-if]]

Use `when` instead of `if` with just the truthy branch, as in `(if condition (something...))` or `(if ... (do ...))`.

[source,clojure]
----
;; good
(when pred
  (foo)
  (bar))

;; bad
(if pred
  (do
    (foo)
    (bar)))
----

=== `if-let` [[if-let]]

Use `if-let` instead of `let` + `if`.

[source,clojure]
----
;; good
(if-let [result (foo x)]
  (something-with result)
  (something-else))

;; bad
(let [result (foo x)]
  (if result
    (something-with result)
    (something-else)))
----

=== `when-let` [[when-let]]

Use `when-let` instead of `let` + `when`.

[source,clojure]
----
;; good
(when-let [result (foo x)]
  (do-something-with result)
  (do-something-more-with result))

;; bad
(let [result (foo x)]
  (when result
    (do-something-with result)
    (do-something-more-with result)))
----

=== `if-not` [[if-not]]

Use `if-not` instead of `(if (not ...) ...)`.

[source,clojure]
----
;; good
(if-not pred
  (foo))

;; bad
(if (not pred)
  (foo))
----

=== `when-not` [[when-not]]

Use `when-not` instead of `(when (not ...) ...)`.

[source,clojure]
----
;; good
(when-not pred
  (foo)
  (bar))

;; bad
(when (not pred)
  (foo)
  (bar))
----

=== `when-not` vs `if-not` [[when-not-instead-of-single-branch-if-not]]

Use `when-not` instead of `(if-not ... (do ...))`.

[source,clojure]
----
;; good
(when-not pred
  (foo)
  (bar))

;; bad
(if-not pred
  (do
    (foo)
    (bar)))
----

=== `not=` [[not-equal]]

Use `not=` instead of `(not (= ...))`.

[source,clojure]
----
;; good
(not= foo bar)

;; bad
(not (= foo bar))
----

=== `printf` [[printf]]

Prefer `printf` over `(print (format ...))`.

[source,clojure]
----
;; good
(printf "Hello, %s!\n" name)

;; ok
(println (format "Hello, %s!" name))
----

=== Flexible Comparison Functions

When doing comparisons, leverage the fact that Clojure's functions `<`,
`>`, etc. accept a variable number of arguments.

[source,clojure]
----
;; good
(< 5 x 10)

;; bad
(and (> x 5) (< x 10))
----

=== Single Parameter Function Literal [[single-param-fn-literal]]

Prefer `%` over `%1` in function literals with only one parameter.

[source,clojure]
----
;; good
#(Math/round %)

;; bad
#(Math/round %1)
----

=== Multiple Parameters Function Literal [[multiple-params-fn-literal]]

Prefer `%1` over `%` in function literals with more than one parameter.

[source,clojure]
----
;; good
#(Math/pow %1 %2)

;; bad
#(Math/pow % %2)
----

=== No Useless Anonymous Functions [[no-useless-anonymous-fns]]

Don't wrap functions in anonymous functions when you don't need to.

[source,clojure]
----
;; good
(filter even? (range 1 10))

;; bad
(filter #(even? %) (range 1 10))
----

=== No Multiple Forms in Function Literals [[no-multiple-forms-fn-literals]]

Don't use function literals if the function body will consist of
more than one form.

[source,clojure]
----
;; good
(fn [x]
  (println x)
  (* x 2))

;; bad (you need an explicit do form)
#(do (println %)
     (* % 2))
----

=== `complement` [[complement]]

Favor the use of `complement` versus the use of an anonymous function.

[source,clojure]
----
;; good
(filter (complement some-pred?) coll)

;; bad
(filter #(not (some-pred? %)) coll)
----

This rule should obviously be ignored if the complementing predicate
exists in the form of a separate function (e.g. `even?` and `odd?`).

=== `comp` [[comp]]

Favor `comp` over anonymous functions for function composition.

[source,clojure]
----
;; Assuming `(:require [clojure.string :as str])`...

;; good
(map #(str/capitalize (str/trim %)) ["top " " test "])

;; better
(map (comp str/capitalize str/trim) ["top " " test "])
----

=== `partial` [[partial]]

Favor `partial` over anonymous functions.

[source,clojure]
----
;; good
(map #(+ 5 %) (range 1 10))

;; (arguably) better
(map (partial + 5) (range 1 10))
----

=== Threading Macros [[threading-macros]]

Prefer the use of the threading macros `+->+` (thread-first) and `+->>+`
(thread-last) to heavy form nesting.

[source,clojure]
----
;; good
(-> [1 2 3]
    reverse
    (conj 4)
    prn)

;; not as good
(prn (conj (reverse [1 2 3])
           4))

;; good
(->> (range 1 10)
     (filter even?)
     (map (partial * 2)))

;; not as good
(map (partial * 2)
     (filter even? (range 1 10)))
----

=== Threading Macros and Optional Parentheses

Parentheses are not required when using the threading macros for functions having no argument specified, so use them only when necessary.

[source,clojure]
----
;; good
(-> x fizz :foo first frob)

;; bad; parens add clutter and are not needed
(-> x (fizz) (:foo) (first) (frob))

;; good, parens are necessary with an arg
(-> x
    (fizz a b)
    :foo
    first
    (frob x y))
----

=== Threading Macros Alignment

The arguments to the threading macros `+->+` (thread-first) and `+->>+`
(thread-last) should line up.

[source,clojure]
----
;; good
(->> (range)
     (filter even?)
     (take 5))

;; bad
(->> (range)
  (filter even?)
  (take 5))
----

=== Default `cond` Branch [[else-keyword-in-cond]]

Use `:else` as the catch-all test expression in `cond`.

[source,clojure]
----
;; good
(cond
  (neg? n) "negative"
  (pos? n) "positive"
  :else "zero")

;; bad
(cond
  (neg? n) "negative"
  (pos? n) "positive"
  true "zero")
----

=== `condp` vs `cond` [[condp]]

Prefer `condp` instead of `cond` when the predicate & expression don't
change.

[source,clojure]
----
;; good
(cond
  (= x 10) :ten
  (= x 20) :twenty
  (= x 30) :thirty
  :else :dunno)

;; much better
(condp = x
  10 :ten
  20 :twenty
  30 :thirty
  :dunno)
----

=== `case` vs `cond/condp` [[case]]

Prefer `case` instead of `cond` or `condp` when test expressions are
compile-time constants.

[source,clojure]
----
;; good
(cond
  (= x 10) :ten
  (= x 20) :twenty
  (= x 30) :forty
  :else :dunno)

;; better
(condp = x
  10 :ten
  20 :twenty
  30 :forty
  :dunno)

;; best
(case x
  10 :ten
  20 :twenty
  30 :forty
  :dunno)
----

=== Short Forms In Cond [[short-forms-in-cond]]

Use short forms in `cond` and related.  If not possible give visual
hints for the pairwise grouping with comments or empty lines.

[source,clojure]
----
;; good
(cond
  (test1) (action1)
  (test2) (action2)
  :else   (default-action))

;; ok-ish
(cond
  ;; test case 1
  (test1)
  (long-function-name-which-requires-a-new-line
    (complicated-sub-form
      (-> 'which-spans multiple-lines)))

  ;; test case 2
  (test2)
  (another-very-long-function-name
    (yet-another-sub-form
      (-> 'which-spans multiple-lines)))

  :else
  (the-fall-through-default-case
    (which-also-spans 'multiple
                      'lines)))
----

=== Set As Predicate [[set-as-predicate]]

Use a `set` as a predicate when appropriate.

[source,clojure]
----
;; good
(remove #{1} [0 1 2 3 4 5])

;; bad
(remove #(= % 1) [0 1 2 3 4 5])

;; good
(count (filter #{\a \e \i \o \u} "mary had a little lamb"))

;; bad
(count (filter #(or (= % \a)
                    (= % \e)
                    (= % \i)
                    (= % \o)
                    (= % \u))
               "mary had a little lamb"))
----

=== `inc` and `dec` [[inc-and-dec]]

Use `(inc x)` & `(dec x)` instead of `(+ x 1)` and `(- x 1)`.

=== `pos?` and `neg?` [[pos-and-neg]]

Use `(pos? x)`, `(neg? x)` & `(zero? x)` instead of `(> x 0)`,
`(< x 0)` & `(= x 0)`.

=== `list*` vs `cons` [[list-star-instead-of-nested-cons]]

Use `list*` instead of a series of nested `cons` invocations.

[source,clojure]
----
;; good
(list* 1 2 3 [4 5])

;; bad
(cons 1 (cons 2 (cons 3 [4 5])))
----

=== Sugared Java Interop [[sugared-java-interop]]

Use the sugared Java interop forms.

[source,clojure]
----
;;; object creation
;; good
(java.util.ArrayList. 100)

;; bad
(new java.util.ArrayList 100)

;;; static method invocation
;; good
(Math/pow 2 10)

;; bad
(. Math pow 2 10)

;;; instance method invocation
;; good
(.substring "hello" 1 3)

;; bad
(. "hello" substring 1 3)

;;; static field access
;; good
Integer/MAX_VALUE

;; bad
(. Integer MAX_VALUE)

;;; instance field access
;; good
(.someField some-object)

;; bad
(. some-object someField)
----

=== Compact Metadata Notation For True Flags [[compact-metadata-notation-for-true-flags]]

Use the compact metadata notation for metadata that contains only
slots whose keys are keywords and whose value is boolean `true`.

[source,clojure]
----
;; good
(def ^:private a 5)

;; bad
(def ^{:private true} a 5)
----

=== Private [[private]]

Denote private parts of your code.

[source,clojure]
----
;; good
(defn- private-fun [] ...)

(def ^:private private-var ...)

;; bad
(defn private-fun [] ...) ; not private at all

(defn ^:private private-fun [] ...) ; overly verbose

(def private-var ...) ; not private at all
----

=== Access Private Var [[access-private-var]]

To access a private var (e.g. for testing), use the `@#'some.ns/var` form.

=== Attach Metadata Carefully [[attach-metadata-carefully]]

Be careful regarding what exactly you attach metadata to.

[source,clojure]
----
;; we attach the metadata to the var referenced by `a`
(def ^:private a {})
(meta a) ;=> nil
(meta #'a) ;=> {:private true}

;; we attach the metadata to the empty hash-map value
(def a ^:private {})
(meta a) ;=> {:private true}
(meta #'a) ;=> nil
----

== Data Structures

[quote, Alan J. Perlis]
____
It is better to have 100 functions operate on one data structure
than to have 10 functions operate on 10 data structures.
____

=== Avoid Lists [[avoid-lists]]

Avoid the use of lists for generic data storage (unless a list is
exactly what you need).

=== Keywords For Hash Keys [[keywords-for-hash-keys]]

Prefer the use of keywords for hash keys.

[source,clojure]
----
;; good
{:name "Bruce" :age 30}

;; bad
{"name" "Bruce" "age" 30}
----

=== Literal Collection Syntax [[literal-col-syntax]]

Prefer the use of the literal collection syntax where
applicable. However, when defining sets, only use literal syntax
when the values are compile-time constants.

[source,clojure]
----
;; good
[1 2 3]
#{1 2 3}
(hash-set (func1) (func2)) ; values determined at runtime

;; bad
(vector 1 2 3)
(hash-set 1 2 3)
#{(func1) (func2)} ; will throw runtime exception if (func1) = (func2)
----

=== Avoid Index Based Collection Access [[avoid-index-based-coll-access]]

Avoid accessing collection members by index whenever possible.

=== Keywords as Functions for Map Values Retrieval [[keywords-as-fn-to-get-map-values]]

Prefer the use of keywords as functions for retrieving values from
maps, where applicable.

[source,clojure]
----
(def m {:name "Bruce" :age 30})

;; good
(:name m)

;; more verbose than necessary
(get m :name)

;; bad - susceptible to NullPointerException
(m :name)
----

=== Collections as Functions [[colls-as-fns]]

Leverage the fact that most collections are functions of their elements.

[source,clojure]
----
;; good
(filter #{\a \e \o \i \u} "this is a test")

;; bad - too ugly to share
----

=== Keywords as Functions [[keywords-as-fns]]

Leverage the fact that keywords can be used as functions of a collection.

[source,clojure]
----
((juxt :a :b) {:a "ala" :b "bala"})
----

=== Avoid Transient Collections [[avoid-transient-colls]]

Avoid the use of transient collections, except for
performance-critical portions of the code.

=== Avoid Java Collections [[avoid-java-colls]]

Avoid the use of Java collections.

=== Avoid Java Arrays [[avoid-java-arrays]]

Avoid the use of Java arrays, except for interop scenarios and
performance-critical code dealing heavily with primitive types.

== Types & Records

=== Record Constructors [[record-constructors]]

Don't use the interop syntax to
construct type and record instances.  `deftype` and `defrecord`
automatically create constructor functions. Use those instead of
the interop syntax, as they make it clear that you're dealing with a
`deftype` or a `defrecord`. See https://stuartsierra.com/2015/05/17/clojure-record-constructors[this
article]
for more details.

[source,clojure]
----
(defrecord Foo [a b])
(deftype Bar [a b])

;; good
(->Foo 1 2)
(map->Foo {:b 4 :a 3})
(->Bar 1 2)

;; bad
(Foo. 1 2)
(Bar. 1 2)
----

Note that `deftype` doesn't define the `+map->Type+`
  constructor. It's available only for records.

=== Custom Record Constructors [[custom-record-constructors]]

Add custom type/record constructors when needed (e.g. to validate
properties on record creation). See https://stuartsierra.com/2015/05/17/clojure-record-constructors[this
article]
for more details.

[source,clojure]
----
(defrecord Customer [id name phone email])

(defn make-customer
  "Creates a new customer record."
  [{:keys [name phone email]}]
  {:pre [(string? name)
         (valid-phone? phone)
         (valid-email? email)]}
  (->Customer (next-id) name phone email))
----

Feel free to adopt whatever naming convention or structure you'd like for such custom constructors.

=== Custom Record Constructors Naming [[custom-record-constructors-naming]]

Don't override the auto-generated type/record constructor functions.
People expect them to have a certain behaviour and changing this behaviour
violates the principle of least surprise. See https://stuartsierra.com/2015/05/17/clojure-record-constructors[this
article]
for more details.

[source,clojure]
----
(defrecord Foo [num])

;; good
(defn make-foo
  [num]
  {:pre [(pos? num)]}
  (->Foo num))

;; bad
(defn ->Foo
  [num]
  {:pre [(pos? num)]}
  (Foo. num))
----

== Mutation

=== Refs [[Refs]]

==== `io!` Macro [[refs-io-macro]]

Consider wrapping all I/O calls with the `io!` macro to avoid nasty
surprises if you accidentally end up calling such code in a
transaction.

==== Avoid `ref-set` [[refs-avoid-ref-set]]

Avoid the use of `ref-set` whenever possible.

[source,clojure]
----
(def r (ref 0))

;; good
(dosync (alter r + 5))

;; bad
(dosync (ref-set r 5))
----

==== Small Transactions [[refs-small-transactions]]

Try to keep the size of transactions (the amount of work encapsulated in them)
as small as possible.

==== Avoid Short Long Transactions With Same Ref [[refs-avoid-short-long-transactions-with-same-ref]]

Avoid having both short- and long-running transactions interacting
with the same Ref.

=== Agents [[Agents]]

==== Agents Send [[agents-send]]

Use `send` only for actions that are CPU bound and don't block on I/O
or other threads.

==== Agents Send Off [[agents-send-off]]

Use `send-off` for actions that might block, sleep, or otherwise tie
up the thread.

=== Atoms [[Atoms]]

==== No Updates Within Transactions [[atoms-no-update-within-transactions]]

Avoid atom updates inside STM transactions.

==== Prefer `swap!` over `reset!` [[atoms-prefer-swap-over-reset]]

Try to use `swap!` rather than `reset!`, where possible.

[source,clojure]
----
(def a (atom 0))

;; good
(swap! a + 5)

;; not as good
(reset! a 5)
----

== Strings

=== Prefer `clojure.string` Functions Over Interop [[prefer-clojure-string-over-interop]]

Prefer string manipulation functions from `clojure.string` over Java interop or rolling your own.

[source,clojure]
----
;; good
(clojure.string/upper-case "bruce")

;; bad
(.toUpperCase "bruce")
----

NOTE: Several new functions were added to `clojure.string` in Clojure
1.8 (`index-of`, `last-index-of`, `starts-with?`, `ends-with?` and
`includes?`). You should avoid using those if you need to support
older Clojure releases.

== Exceptions

=== Reuse Existing Exception Types [[reuse-existing-exception-types]]

Reuse existing exception types. Idiomatic Clojure code -- when it does
throw an exception -- throws an exception of a standard type
(e.g. `java.lang.IllegalArgumentException`,
`java.lang.UnsupportedOperationException`,
`java.lang.IllegalStateException`, `java.io.IOException`).

=== Prefer `with-open` Over `finally` [[prefer-with-open-over-finally]]

Favor `with-open` over `finally`.

== Macros

=== Don't Write a Macro If a Function Will Do [[dont-write-macro-if-fn-will-do]]

Don't write a macro if a function will do.

=== Write Macro Usage before Writing the Macro [[write-macro-usage-before-writing-the-macro]]

Create an example of a macro usage first and the macro afterwards.

=== Break Complicated Macros [[break-complicated-macros]]

Break complicated macros into smaller functions whenever possible.

=== Macros as Syntactic Sugar [[macros-as-syntactic-sugar]]

A macro should usually just provide syntactic sugar and the core of
the macro should be a plain function. Doing so will improve
composability.

=== Syntax Quoted Forms [[syntax-quoted-forms]]

Prefer syntax-quoted forms over building lists manually.

== Common Metadata

In this section we'll go over some common metadata for namespaces and
vars that Clojure development tools can leverage.

=== `:added`

The most common way to document when a public API was added to a
library is via the `:added` metadata.

[source,clojure]
----
(def ^{:added "0.5"} foo
  42)

(ns foo.bar
  "A very useful ns."
  {:added "0.8"})

(defn ^{:added "0.5"} foo
  (bar))
----

TIP: If you're into SemVer, it's a good idea to omit the patch version.
This means you should use `0.5` instead of `0.5.0`. This applies
for all metadata data that's version related.

=== `:changed`

The most common way to document when a public API was changed in a
library is via the `:changed` metadata. This metadata makes sense only for
vars and you should be using it sparingly, as changing the behavior of
a public API is generally a bad idea.

Still, if you decide to do it, it's best to make that clear to the API
users.

[source,clojure]
----
(def ^{:added "0.5"
       :changed "0.6"} foo
  43)
----

=== `:deprecated`

The most common way to mark deprecated public APIs is via the `:deprecated`
metadata. Normally you'd use as the value the version in which something
was deprecated in case of versioned software (e.g. a library) or simply
`true` in the case of unversioned software (e.g. some web application).

[source,clojure]
----
;;; good
;;
;; in case we have a version
(def ^{:deprecated "0.5"} foo
  "Use `bar` instead."
  42)

(ns foo.bar
  "A deprecated ns."
  {:deprecated "0.8"})

(defn ^{:deprecated "0.5"} foo
  (bar))

;; otherwise
(defn ^:deprecated foo
  (bar))

;;; bad
;;
;; using the docstring to signal deprecation
(def foo
  "DEPRECATED: Use `bar` instead."
  42)

(ns foo.bar
  "DEPRECATED: A deprecated ns.")
----

=== `:superseded-by`

Often you'd combine `:deprecated` with `:superseded-by`, as there would be
some newer API that supersedes whatever got deprecated.

Typically for vars you'll use a non-qualified name if the replacement lives
in the same namespace, and a fully-qualified name otherwise.

[source,clojure]
----
;; in case we have a version
(def ^{:deprecated "0.5"
       :superseded-by "bar"} foo
  "Use `bar` instead."
  42)

(ns foo.bar
  "A deprecated ns."
  {:deprecated "0.8"
   :superseded-by "foo.baz"})

(defn ^{:deprecated "0.5"
        :superseded-by "bar"} foo
  (bar))

;; otherwise
(defn ^{:deprecated true
        :superseded-by "bar"} foo
  (bar))
----

TIP: You can also consider adding `:supersedes` metadata to the newer APIs, basically the inverse of `:superseded-by`.

=== `:see-also`

From time to time you might want to point out some related vars/namespaces that the users of your library might be interested in.
The most common way to do so would be via the `:see-also` metadata, which takes a vector of related items.
When talking about vars - items in the same namespace don't need to fully qualified.

[source,clojure]
----
;; refers to vars in the same ns
(def ^{:see-also ["bar" "baz"]} foo
  "A very useful var."
  42)

;; refers to vars in some other ns
(defn ^{:see-also ["top.bar" "top.baz"]} foo
  (bar))
----

NOTE: Many Clojure programming tools will also try to extract references to other vars from the docstring, but it's both
simpler and more explicit to use the `:see-also` metadata instead.

=== `:no-doc`

Documentation tools like https://github.com/weavejester/codox#metadata-options[Codox] like https://github.com/cljdoc/cljdoc/blob/master/doc/userguide/for-library-authors.adoc#hiding-namespaces-vars-in-documentation[cljdoc] recognize `:no-doc` metadata.
When a var or a namespace has `:no-doc` metadata, it indicates to these tools that it should be excluded from generated API docs.

To exclude an entire namespace from API docs:
[source,clojure]
----
(ns ^:no-doc my-library.impl
  "Internal implementation details")

...
----

To exclude vars within a documented namespace:
[source,clojure]
----
(ns my-library.api)

;; private functions do not get documented
(defn- clearly-private []
  ...)

;; nor do public functions with :no-doc metadata
(defn ^:no-doc shared-helper []
  ...)

;; this function will be documented
(defn api-fn1
  "I am useful to the public"
  []
  ...)
----

=== Indentation Metadata

Unlike other Lisp dialects, Clojure doesn't have a standard metadata format to specify the indentation of macros.
CIDER proposed a tool-agnostic https://docs.cider.mx/cider/indent_spec.html[indentation specification] based on metadata in 2015.footnote:[This was first introduced in CIDER 0.10] Here's a simple example:

[source,clojure]
----
;; refers to vars in the same ns
(defmacro with-in-str
  "[DOCSTRING]"
  {:style/indent 1}
  [s & body]
  ...cut for brevity...)
----

This instructs the indentation engine that this is a macro with one ordinary parameter and a body after it.

[source,clojure]
----
;; without metadata (indented as a regular function)
(dop-iin-str some-string
             foo
             bar
             baz)

;; with metadata (indented as macro with one special param and a body)
(with-in-str some-string
  foo
  bar
  baz)
----

Unfortunately, as of 2020 there's still no widespread adoption of `:style/indent` and many editors and IDEs would just
hardcode the indentation rules for common macros.

NOTE: This approach to indentation ("semantic indentation") is a contested topic in the Clojure community, due to the
need for the additional metadata and tooling support. Despite the long tradition of that approach in the Lisp community
in general, some people argue to just stop treating functions and macros differently and simply indent everything with a fixed
indentation. https://tonsky.me/blog/clojurefmt/[This article] is one popular presentation of that alternative approach.

== Comments

[quote, Steve McConnell]
____
Good code is its own best documentation. As you're about to add a
comment, ask yourself, "How can I improve the code so that this
comment isn't needed?" Improve the code and then document it to make
it even clearer.
____

=== Self-Explanatory Code

Endeavor to make your code as self-explanatory as possible.
If you fail to achieve this follow the rest of the guidelines in this section.

=== Heading Comments [[four-semicolons-for-heading-comments]]

Write heading comments with at least four semicolons. Those typically serve to outline/separate
major section of code, or to describe important ideas. Often you'd have a section comment followed
by a bunch of top-level comments.

[source,clojure]
----
;;;; Section Comment/Heading

;;; Foo...
;;; Bar...
;;; Baz...
----

=== Top-Level Comments [[three-semicolons-for-top-level-comments]]

Write top-level comments with three semicolons.

[source,clojure]
----
;;; I'm a top-level comment.
;;; I live outside any definition.

(defn foo [])
----

NOTE: While the classic Lisp tradition dictates the use of `;;;` for
top-level comments, you'll find plenty of Clojure code in the wild
that's using `;;` or even `;`.

=== Code Fragment (Line) Comments [[two-semicolons-for-code-fragment]]

Write comments on a particular fragment of code before that fragment
and aligned with it, using two semicolons.

[source,clojure]
----
(defn foo [x]
  ;; I'm a line/code fragment comment.
  x)
----

NOTE: While the classic Lisp tradition dictates the use of `;;` for
line comments, you'll find plenty of Clojure code in the wild
that's using only `;`.

=== Margin (Inline) Comments [[one-semicolon-for-margin-comments]]

Write margin comments with one semicolon.

[source,clojure]
----
(defn foo [x]
  x ; I'm a line/code fragment comment.
  )
----

Avoid using those in situations that would result in hanging closing parentheses.

=== Semicolon Space [[semicolon-space]]

Always have at least one space between the semicolon
and the text that follows it.

[source,clojure]
----
;;;; Frob Grovel

;;; This section of code has some important implications:
;;;   1. Foo.
;;;   2. Bar.
;;;   3. Baz.

(defn fnord [zarquon]
  ;; If zob, then veeblefitz.
  (quux zot
        mumble             ; Zibblefrotz.
        frotz))
----

=== English Syntax [[english-syntax]]

Comments longer than a word begin with a capital letter and use
punctuation. Separate sentences with
https://en.wikipedia.org/wiki/Sentence_spacing[one space].

[source,clojure]
----
;; This is a good comment.

;; this is a bad comment
----

Obviously punctuation is not the most important thing about
a comment, but a bit of extra effort results in better experience
for the readers of our comments.

=== No Superfluous Comments [[no-superfluous-comments]]

Avoid superfluous comments.

[source,clojure]
----
;; bad
(inc counter) ; increments counter by one
----

=== Comment Upkeep [[comment-upkeep]]

Keep existing comments up-to-date. An outdated comment is worse than no comment
at all.

=== `#_` Reader Macro [[dash-underscore-reader-macro]]

Prefer the use of the `#_` reader macro over a regular comment when
you need to comment out a particular form.

[source,clojure]
----
;; good
(+ foo #_(bar x) delta)

;; bad
(+ foo
   ;; (bar x)
   delta)
----

=== Refactor, Don't Comment [[refactor-dont-comment]]

[quote, Russ Olsen]
____
Good code is like a good joke - it needs no explanation.
____

Avoid writing comments to explain bad code. Refactor the code to
make it self-explanatory. ("Do, or do not. There is no try." --Yoda)

=== Comment Annotations

==== Annotate Above [[annotate-above]]

Annotations should usually be written on the line immediately above
the relevant code.

[source,clojure]
----
;; good
(defn some-fun
  []
  ;; FIXME: Replace baz with the newer bar.
  (baz))

;; bad
;; FIXME: Replace baz with the newer bar.
(defn some-fun
  []
  (baz))
----

==== Annotate Keywords [[annotate-keywords]]

The annotation keyword is followed by a colon and a space, then a note
describing the problem.

[source,clojure]
----
;; good
(defn some-fun
  []
  ;; FIXME: Replace baz with the newer bar.
  (baz))

;; bad - no colon after annotation
(defn some-fun
  []
  ;; FIXME Replace baz with the newer bar.
  (baz))

;; bad - no space after colon
(defn some-fun
  []
  ;; FIXME:Replace baz with the newer bar.
  (baz))
----

==== Indent Annotations [[indent-annotations]]

If multiple lines are required to describe the problem, subsequent
lines should be indented as much as the first one.

[source,clojure]
----
;; good
(defn some-fun
  []
  ;; FIXME: This has crashed occasionally since v1.2.3. It may
  ;;        be related to the BarBazUtil upgrade. (xz 13-1-31)
  (baz))

;; bad
(defn some-fun
  []
  ;; FIXME: This has crashed occasionally since v1.2.3. It may
  ;; be related to the BarBazUtil upgrade. (xz 13-1-31)
  (baz))
----

==== Sign and Date Annotations [[sign-and-date-annotations]]

Tag the annotation with your initials and a date so its relevance can
be easily verified.

[source,clojure]
----
(defn some-fun
  []
  ;; FIXME: This has crashed occasionally since v1.2.3. It may
  ;;        be related to the BarBazUtil upgrade. (xz 13-1-31)
  (baz))
----

==== Rare Margin (EOL) Annotations [[rare-eol-annotations]]

In cases where the problem is so obvious that any documentation would
be redundant, annotations may be left at the end of the offending line
with no note. This usage should be the exception and not the rule.

[source,clojure]
----
(defn bar
  []
  (sleep 100)) ; OPTIMIZE
----

==== `TODO` [[todo]]

Use `TODO` to note missing features or functionality that should be
added at a later date.

==== `FIXME` [[fixme]]

Use `FIXME` to note broken code that needs to be fixed.

==== `OPTIMIZE` [[optimize]]

Use `OPTIMIZE` to note slow or inefficient code that may cause
performance problems.

==== `HACK` [[hack]]

Use `HACK` to note "code smells" where questionable coding practices
were used and should be refactored away.

==== `REVIEW` [[review]]

Use `REVIEW` to note anything that should be looked at to confirm it
is working as intended. For example: `REVIEW: Are we sure this is how the
client does X currently?`

==== Document Custom Annotations [[document-annotations]]

Use other custom annotation keywords if it feels appropriate, but be
sure to document them in your project's `README` or similar.

== Documentation

Docstrings are the primary way to document Clojure code. Many definition forms
(e.g. `def`, `defn`, `defmacro`, `ns`)
support docstrings and usually it's a good idea to make good use of them, regardless
of whether the var in question is something public or private.

If a definition form doesn't support docstrings directly you can still supply them via
the `:doc` metadata attribute.

This section outlines some of the common conventions and best
practices for documenting Clojure code.

=== Prefer Docstrings [[prefer-docstrings]]

If a form supports docstrings directly prefer them over using `:doc` metadata:

[source,clojure]
----
;; good
(defn foo
  "This function doesn't do much."
  []
  ...)

(ns foo.bar.core
  "That's an awesome library.")

;; bad
(defn foo
  ^{:doc "This function doesn't do much."}
  []
  ...)

(ns ^{:doc "That's an awesome library.")
  foo.bar.core)
----

=== Docstring Summary [[docstring-summary]]

Let the first line in the docstring be a complete, capitalized
sentence which concisely describes the var in question. This makes it
easy for tooling (Clojure editors and IDEs) to display a short a summary of
the docstring at various places.

[source,clojure]
----
;; good
(defn frobnitz
  "This function does a frobnitz.
  It will do gnorwatz to achieve this, but only under certain
  circumstances."
  []
  ...)

;; bad
(defn frobnitz
  "This function does a frobnitz. It will do gnorwatz to
  achieve this, but only under certain circumstances."
  []
  ...)
----

=== Leverage Markdown in Docstrings [[markdown-docstrings]]

Important tools such as https://github.com/cljdoc/cljdoc/blob/master/doc/userguide/for-library-authors.adoc#docstrings[cljdoc] support Markdown in docstrings so leverage it for nicely formatted documentation.

[source,clojure]
----
;; good
(defn qzuf-number
  "Computes the [Qzuf number](https://wikipedia.org/qzuf) of the `coll`.
  Supported options in `opts`:

  | key           | description |
  | --------------|-------------|
  | `:finite-uni?`| Assume finite universe; default: `false`
  | `:complex?`   | If OK to return a [complex number](https://en.wikipedia.org/wiki/Complex_number); default: `false`
  | `:timeout`    | Throw an exception if the computation doesn't finish within `:timeout` milliseconds; default: `nil`

  Example:
  ```clojure
  (when (neg? (qzuf-number [1 2 3] {:finite-uni? true}))
    (throw (RuntimeException. \"Error in the Universe!\")))
  ```"
  [coll opts]
  ...)
----

=== Document Positional Arguments [[document-pos-arguments]]

Document all positional arguments, and wrap them them with backticks
(`) so that editors and IDEs can identify them and potentially provide extra
functionality for them.

[source,clojure]
----
;; good
(defn watsitz
  "Watsitz takes a `frob` and converts it to a znoot.
  When the `frob` is negative, the znoot becomes angry."
  [frob]
  ...)

;; bad
(defn watsitz
  "Watsitz takes a frob and converts it to a znoot.
  When the frob is negative, the znoot becomes angry."
  [frob]
  ...)
----

=== Document References [[document-references]]

Wrap any var references in the docstring with ` so that tooling
can identify them. Wrap them with `[[..]]` if you want to link to them.

[source,clojure]
----
;; good
(defn wombat
  "Acts much like `clojure.core/identity` except when it doesn't.
  Takes `x` as an argument and returns that. If it feels like it.
  See also [[kangaroo]]."
  [x]
  ...)

;; bad
(defn wombat
  "Acts much like clojure.core/identity except when it doesn't.
  Takes `x` as an argument and returns that. If it feels like it.
  See also kangaroo."
  [x]
  ...)
----

=== Docstring Grammar [[docstring-grammar]]

Docstrings should be composed of well-formed English sentences. Every sentence
should start with a capitalized word, be gramatically coherent, and end
with appropriate punctuation. Sentences should be separated with a single space.

[source,clojure]
----
;; good
(def foo
  "All sentences should end with a period (or maybe an exclamation mark).
  The sentence should be followed by a space, unless it concludes the docstring.")

;; bad
(def foo
  "all sentences should end with a period (or maybe an exclamation mark).
  The sentence should be followed by a space, unless it concludes the docstring.")
----

=== Docstring Indentation [[docstring-indentation]]

Indent multi-line docstrings by two spaces.

[source,clojure]
----
;; good
(ns my.ns
  "It is actually possible to document a ns.
  It's a nice place to describe the purpose of the namespace and maybe even
  the overall conventions used. Note how _not_ indenting the docstring makes
  it easier for tooling to display it correctly.")

;; bad
(ns my.ns
  "It is actually possible to document a ns.
It's a nice place to describe the purpose of the namespace and maybe even
the overall conventions used. Note how _not_ indenting the docstring makes
it easier for tooling to display it correctly.")
----

=== Docstring Leading Trailing Whitespace [[docstring-leading-trailing-whitespace]]

Neither start nor end your docstrings with any whitespace.

[source,clojure]
----
;; good
(def foo
  "I'm so awesome."
  42)

;; bad
(def silly
  "    It's just silly to start a docstring with spaces.
  Just as silly as it is to end it with a bunch of them.      "
  42)
----

=== Place Docstring After Function Name [[docstring-after-fn-name]]

When adding a docstring -- especially to a function using the above form -- take
care to correctly place the docstring after the function name, not after the
argument vector.  The latter is not invalid syntax and won't cause an error,
but includes the string as a form in the function body without attaching it to
the var as documentation.

[source,clojure]
----
;; good
(defn foo
  "docstring"
  [x]
  (bar x))

;; bad
(defn foo [x]
  "docstring"
  (bar x))
----

[NOTE]
====
Place docstrings for `defprotocol` methods _after_ the argument vector:

[source,clojure]
----
(defprotocol MyProtocol
  "MyProtocol docstring"
  (foo [this x y z]
    "foo docstring")
  (bar [this]
    "bar docstring"))
----
====

== Testing

=== Test Directory Structure [[test-directory-structure]]

Store your tests in a separate directory, typically `test/yourproject/` (as
opposed to `src/yourproject/`). Your build tool is responsible for making
them available in the contexts where they are necessary; most templates
will do this for you automatically.

=== Test Namespace Naming [[test-ns-naming]]

Name your ns `yourproject.something-test`, a file which usually lives in
`test/yourproject/something_test.clj` (or `.cljc`, `cljs`).

=== Test Naming [[test-naming]]

When using `clojure.test`, define your tests
with `deftest` and name them `something-test`.

[source,clojure]
----
;; good
(deftest something-test ...)

;; bad
(deftest something-tests ...)
(deftest test-something ...)
(deftest something ...)
----

== Library Organization

=== Library Coordinates [[lib-coordinates]]

If you are publishing libraries to be used by others, make sure to
follow the https://central.sonatype.org/pages/choosing-your-coordinates.html[Central Repository
guidelines]
for choosing your `groupId` and `artifactId`. This helps to prevent
name conflicts and facilitates the widest possible use. A good
example is https://github.com/stuartsierra/component[Component] - its
coordinates are `com.stuartsierra/component`.

Another approach that's popular in the wild is to use a project (or organization)
name as the `groupId` instead of domain name. Examples of such naming would be:

* `cider/cider-nrepl`
* `nrepl/nrepl`
* `nrepl/drawbridge`
* `clj-commons/fs`

=== Minimize Dependencies [[lib-min-dependencies]]

Avoid unnecessary dependencies. For example, a three-line utility
function copied into a project is usually better than a dependency
that drags in hundreds of vars you do not plan to use.

=== Tool-agnostic [[lib-core-separate-from-tools]]

Deliver core functionality and integration points in separate
artifacts.  That way, consumers can consume your library without
being constrained by your unrelated tooling preferences. For example,
https://github.com/stuartsierra/component[Component] provides
core functionality, and
https://github.com/stuartsierra/reloaded[reloaded] provides leiningen
integration.

== Existential

=== Be Functional [[be-functional]]

Code in a functional way, using mutation only when it makes sense.

=== Be Consistent [[be-consistent]]

Be consistent. In an ideal world, be consistent with these guidelines.

=== Common Sense [[common-sense]]

Use common sense.

== Tools

One problem with style guides is that it's often hard to remember all the guidelines
and to apply them consistently. We're only humans, after all. Fortunately,
there are a bunch of tools that can do most of the work for us.

TIP: It's a great idea run such tools as part of your continuous integration (CI).
This ensure that all the code in one project is consistent with the style you're
aiming for.

=== Lint Tools

There are some lint tools created by the Clojure community that might aid you
in your endeavor to write idiomatic Clojure code.

* https://github.com/jonase/kibit[kibit] is a static code analyzer for
Clojure which uses https://github.com/clojure/core.logic[core.logic] to
search for patterns of code for which there might exist a more idiomatic
function or macro.
* https://github.com/borkdude/clj-kondo[clj-kondo] is a linter that detects
a wide number of discouraged patterns and suggests improvements, based on this
style guide.

=== Code Formatters

While most Clojure editors and IDEs can format the code, according to the layout guidelines
outlined here, it's always handy to have some command-line code formatting tools. There are
a couple of options for Clojure that do a great job when it comes to formatting the code
as suggested in this guide:

* https://github.com/weavejester/cljfmt[cljfmt]
* https://github.com/kkinnear/zprint[zprint] (the documentation for configuring it to use the community formatting rules is https://github.com/kkinnear/zprint/blob/master/doc/options/community.md[here])

NOTE: When it comes to editors - Emacs's `clojure-mode` by default will format the code exactly as outlined in the guide.
Other editors might require some configuration tweaking to produce the same results.

== History

This guide was started in 2013 by https://github.com/bbatsov[Bozhidar Batsov], following the
success of a https://rubystyle.guide/[similar project] he had created in the Ruby community.

Bozhidar was very passionate about both Clojure and good programming style and he wanted to bridge the between what was
covered by the https://clojure.org/community/contrib_howto#_coding_guidelines[Clojure library coding guidelines] and what the style guides for languages like Java, Python and Ruby would typically cover.
Bozhidar still serves as the guide's primary editor, but there's an entire editor team supporting the project.

Since the inception of the guide we've received a lot of feedback from members of the exceptional Clojure community around the world.
Thanks for all the suggestions and the support! Together we can make a resource beneficial to each and every Clojure developer out there.

== Sources of Inspiration

Many people, books, presentations, articles and other style guides influenced the community Clojure style guide. Here are some of them:

* https://en.wikipedia.org/wiki/The_Elements_of_Style["The Elements of Style"]
* https://en.wikipedia.org/wiki/The_Elements_of_Programming_Style["The Elements of Programming Style"]
* https://www.python.org/dev/peps/pep-0008/[Python Style Guide (PEP-8)]
* https://rubystyle.guide/[Community Ruby Style Guide]
* https://google.github.io/styleguide/lispguide.xml[Google's Common Lisp Style Guide]
* http://community.schemewiki.org/?scheme-style[scheme-style]
* https://clojure.org/community/contrib_howto#_coding_guidelines[Clojure Library Coding Guidelines]
* https://www.clojurebook.com/["Clojure Programming"]
* https://joyofclojure.com/["The Joy of Clojure"]
* https://elementsofclojure.com/["Elements of Clojure"]
* https://pragprog.com/titles/vmclojeco/clojure-applied/["Clojure Applied"]
* https://stuartsierra.com/tag/dos-and-donts[Stuart Sierra's "Clojure Dos and Don't" blog series]

== Editor Team

The Clojure style guide is stewarded by an editor team of experienced
Clojurists that aims to reduce all the input we get (e.g. feedback and
suggestions) to a better reference for everyone.

* https://metaredux.com/about/[Bozhidar Batsov]
* https://insideclojure.org/about/[Alex Miller]
* https://danielcompton.net/about[Daniel Compton]
* https://corfield.org/[Sean Corfield]

== Contributing

The guide is still a work in progress - some guidelines are lacking examples, some guidelines don't have examples that illustrate them clearly enough.
Improving such guidelines is a great (and simple way) to help the Clojure community!

In due time these issues will (hopefully) be addressed - just keep them in mind for now.

Nothing written in this guide is set in stone.
It's my desire to work together with everyone interested in Clojure coding style, so that we could ultimately create a resource that will be beneficial to the entire Clojure community.

Feel free to open tickets or send pull requests with improvements.
Thanks in advance for your help!

You can also support the style guide (and all my Clojure projects like
CIDER, nREPL, orchard, etc) with financial contributions via one of the following platforms:

* https://github.com/sponsors/bbatsov[GitHub Sponsors]
* https://ko-fi.com/bbatsov[ko-fi]
* https://www.patreon.com/bbatsov[Patreon]
* https://www.paypal.me/bbatsov[PayPal]

=== How to Contribute?

It's easy, just follow the contribution guidelines below:

* https://help.github.com/articles/fork-a-repo[Fork] https://github.com/bbatsov/clojure-style-guide[bbatsov/clojure-style-guide] on GitHub
* Make your feature addition or bug fix in a feature branch.
* Include a http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html[good description] of your changes
* Push your feature branch to GitHub
* Send a https://help.github.com/articles/using-pull-requests[Pull Request]

== Colophon

This guide is written in https://asciidoc.org/[AsciiDoc] and is published as HTML using https://asciidoctor.org/[AsciiDoctor].
The HTML version of the guide is hosted on GitHub Pages.

Originally the guide was written in Markdown, but was converted to AsciiDoc in 2019.

== License

image:https://i.creativecommons.org/l/by/3.0/88x31.png[Creative Commons License]
This work is licensed under a
https://creativecommons.org/licenses/by/3.0/deed.en_US[Creative Commons Attribution 3.0 Unported License]

== Spread the Word

A community-driven style guide is of little use to a community that
doesn't know about its existence. Tweet about the guide, share it with
your friends and colleagues. Every comment, suggestion or opinion we
get makes the guide just a little bit better. And we want to have the
best possible guide, don't we?

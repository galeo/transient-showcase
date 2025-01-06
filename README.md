<h1 class="title">Transient Showcase</h1>

# Preface

<!-- !!!THIS FILE HAS BEEN GENERATED!!! Edit transient-showcase.org -->

Code examples for interactive explanations of [transient](https://github.com/magit/transient).

This guide assumes you have minimal knowledge of Emacs, some programming experience in elisp and non-lisp languages, and have at least seen [screenshots](https://magit.vc/screenshots/) of<code>magit</code>

These examples serve to **illustrate** the documentation in transient's manual, which probably ships with your Emacs, accessible via<code>M-x info-display-manual transient</code>


## How to use

If you are reading the README.md on Github, this markdown file is exported as a preview for transient-showcase.org and to appear in search results etc.

There are two better ways:

-   Open the transient-showcase.org file in Emacs and run examples as literate org.
-   Install the package to run commands and read their source. Start with the<code>tsc-showcase</code>command.


### Using as literate org document

If you open the org file in Emacs, it will switch to Org mode and you can run individual source blocks with<code>org-babel-execute-src-blk</code>on the block.


### Using as an installable package

If you install the package, you can read source for each example with the normal<code>describe-command</code> All commands are under<code>tsc-*</code>prefix. Somewhat useful suffixes are under<code>tsc-suffix-*</code>while less useful ones under<code>tsc--suffix-*</code> They will come in handy when you are developing new applications.

Installations for straight and elpaca:

```elisp

;; using package-vc (built-in)
(package-vc-install
 '(transient-showcase
   :url "https://github.com/positron-solutions/transient-showcase.git"))
(require 'transient-showcase) ; now you can access the commands

;; using elpaca (recommended to add a rev for reproducibility)
(use-package transient-showcase
  :elpaca (transient-showcase
           :host github
           :repo "positron-solutions/transient-showcase"))

;; using straight use-package with custom recipe
(use-package transient-showcase
  :straight '(transient-showcase
              :type git :host github
              :repo "positron-solutions/transient-showcase"))

```

**Note** While the exported markdown version of this file is also the README for this repository, it's not intended to be used directly or by copy-pasting. Many links will only open in Emacs. Some definitions are included by reference from [Preludes](#orgff38980)


### Running Examples in Org Mode

This is a basic transient, using an anonymous lambda interactive command as its only suffix.

```elisp

(transient-define-prefix tsc-hello ()
  "Prefix that is minimal and uses an anonymous command suffix."
  [("s" "call suffix"
    (lambda ()
      (interactive)
      (message "Called a suffix")))])

;; First, use M-x org-babel-execute-src-blk to cause `tsc-hello' to be
;; defined
;; Second, M-x `eval-last-sexp' with your point at the end of the line below
;; (tsc-hello)

```

After executing the block above, you can<code>execute-extended-command</code>(**M-x**) and select<code>tsc-hello</code>to show this transient. All transient prefixes are also commands that show up in (**M-x**)

**Note** If the example above is hard to read, review some [elisp](info:elisp#Top) [syntax](#org208c2cb) and typical forms.


# Table of Contents

- [Terminology](#org46b577e)
  - [Prefixes and Suffixes](#org1b76aae)
  - [Nesting Prefixes](#orgf0ea3ce)
  - [Infix](#org24ba90c)
  - [Summary](#org3af7416)
- [Declaring - Equivalent Forms](#orga77a6d1)
  - [The Shorthand form](#org004e382)
  - [Keyword Arguments Style](#org9489874)
  - [Macro Child Definition Style](#org18c1f26)
  - [Overriding slots in the prefix definition](#orgecfd83c)
  - [Quoting Note for Vectors](#org6c91e60)
- [Groups & Layouts](#org317f0a8)
  - [Descriptions](#org4ca3ec3)
  - [Layouts](#org7ba8f06)
  - [Manually setting group class](#orgd6b49a7)
  - [Pad Keys](#org2a99aa0)
- [Nesting & Flow Control](#org7844f5c)
  - [Single versus multiple commands](#orga65b543)
  - [Nesting](#org84db863)
  - [Mixing Interactive](#org3e1db44)
  - [Pre-Commands Explained](#org2da15d9)
- [Using & Managing State](#orga2d72fe)
  - [The Magic of Transient](#orgf886f46)
  - [Infixes](#org36e2c5c)
  - [Scope](#orgfb6d9c4)
  - [Prefix Value & History](#orged47b55)
  - [History Keys](#org66d5912)
  - [Disabling Set / Save on a Suffix](#orge041b5d)
  - [Setting or Saving Every Time a Suffix is Used](#org041edbe)
  - [Lisp Variables](#orgf0b743d)
- [Controlling CLI's](#org121c678)
  - [Reading arguments within suffixes](#org57b9691)
  - [Switches & Arguments Again](#org4813e75)
  - [Dispatching args into a process](#org924124d)
- [Controlling Visibility](#orgbfa280a)
  - [Visibility Predicates](#orgcad3129)
  - [Inapt (Temporarily Inappropriate)](#orgd7662b4)
  - [Levels](#org6ebf275)
- [Advanced](#org999e41d)
  - [Dynamically generating layouts](#org6c78b1e)
  - [Modifying layouts](#orgefd72c2)
  - [Using prefix scope in children](#org3d54cdc)
  - [Custom Infix Types](#org47828e0)
- [Appendixes](#org3499af9)
  - [EIEIO - OOP in Elisp](#org8dcf55c)
  - [Debugging](#org871fa38)
  - [Layout Hacking](#org7dc18d1)
  - [Hooks](#org11eebbf)
  - [Preludes](#orgff38980)
  - [Essential Elisp](#org208c2cb)
- [Further Reading](#org8ebe2ac)


<a id="org46b577e"></a>

# Terminology

Transient means temporary. Transient gets its name from the temporary keymap and the popup UI for displaying that keymap. Emacs has a similar idea built-in with [set-transient-map](elisp:(describe-function 'set-transient-map)) for a temporary high-precedence keymap.


<a id="org1b76aae"></a>

## Prefixes and Suffixes

The hello transient user input sequence is:

<code>Prefix -> Suffix</code>

-   The **prefix** is the command you invoke first, such as<code>magit-dispatch</code>
-   A **suffix** is a command displayed in the transient UI, such as<code>magit-stage</code>

```elisp
(magit-dispatch) ; same as pressing 'h' in magit-status buffer
```

The keymap and UI display is frequently referred to as "a transient". "Prefix" and "a transient" are almost the same thing. Invoking a prefix will show a transient. They are inseparable ideas.


### Conceptual similarity to Emacs prefix arguments

**Setting [prefix arguments](https://emacsdocs.org/docs/emacs/Prefix-Keymaps) with<code>universal-argument</code><code>C-u</code> is a distinct, separate behavior that is part of Emacs.**

With prefix arguments, you "call" commands with extra arguments, like you would a function.

A transient prefix can set some states and its suffix can then use these states to tweak its behavior. The difference is that within the lifecycle of a transient UI, and coordinating with transient's state persistence, you can create much more complex input to your commands. You can use commands to construct phrases for other commands.

To see a short example of prefix arguments being used within a transient prefix, see [the scope example](#orgfb6d9c4).


<a id="orgf0ea3ce"></a>

## Nesting Prefixes

A prefix can also be bound as a suffix, enabling *nested* prefixes. A user input sequence with nested transients might look like:

<code>Prefix -> Sub-Prefix -> Sub-Prefix -> Suffix</code>

For example, in the<code>magit-dispatch</code>transient <code>?</code>,<code>l</code>for<code>magit-log</code>is a nested transient.<code>b</code>for<code>all branches</code>is the suffix command<code>magit-log-all-branches</code>

See [Flow Control](#org7844f5c) for nested transient examples with both sub-prefixes and suffixes that do no exit.


<a id="org24ba90c"></a>

## Infix

Some suffixes need to hold state, toggling or storing an argument. Infixes are specialized suffixes to set and hold state. A user input sequence with infixes:

<code>Prefix -> Infix -> Infix -> Suffix</code>

See [Infix examples](#orgf72ad7b) to get a better idea.


<a id="org3af7416"></a>

## Summary

-   **Prefixes** display the pop-up UI and bind the keymap.
-   **Suffixes** are commands bound within a prefix
-   **Infixes** are a specialized suffix for storing and setting state
-   A **Suffix** may be yet another **Prefix**, in which case the transient is nested


<a id="orga77a6d1"></a>

# Declaring - Equivalent Forms

You can declare the same behavior 3-4 ways

-   Shorthand forms within<code>transient-define-prefix</code>macro allow shorthand binding of suffixes & commands or creation of infixes directly within the layout definition.

-   Macros for suffixes and infix definition streamline defining commands while also defining how they will behave in a layout.

-   Keyword arguments<code>(:foo val1 :bar val2)</code>are interpreted by the macros and used to set slots (OOP attributes) on prefix, group, and suffix objects. Similar forms for declaring suffixes can be used to modify them when declaring a layout. Very specific control over layouts also uses these forms.

```elisp
;; slots & methods that can be set / overridden in children
(describe-symbol transient-child)
```

-   Custom classes using EIEIO (basically elisp OOP) can change methods deeper in the implementation than you can reach with slots.<code>describe-symbol</code>is a quick way to look at the methods.

```elisp
;; slots & methods that can be set / overridden in suffixes
(describe-symbol transient-suffix)
```

See the [EIEIO Appendix](#org8dcf55c) for introduction to exploring EIEIO objects and classes.


<a id="org004e382"></a>

## The Shorthand form

Binding suffixes with the<code>("key" "description" suffix-or-command)</code>form within a group is extremely common.

```elisp

(transient-define-prefix tsc-wave ()
  "Prefix that waves at the user"
  [("w" "wave" tsc-suffix-wave)])
;; tsc-suffix-wave is a simple command from wave-prelude

;; (tsc-wave)

```

**Note:** Both commands and suffixes from<code>transient-define-suffix</code>can be used. It's a good reason to use<code>private--namespace</code>style names for suffix actions since these commands don't usually show up in (**M-x**) by default.


<a id="org9489874"></a>

## Keyword Arguments Style

You can customize the slot value (OOP attribute) of the transient, groups, and suffixes by adding extra<code>:foo value</code>style pairs.

Not all behaviors have a shorthand form, so as you use more behaviors, you will see more of the keyword argument style API. Here we use the<code>:transient</code>property, set to true, meaning the suffix won't exit the transient.

```elisp

(transient-define-prefix tsc-wave-keyword-args ()
  "Prefix that waves at the user persistently."
  [("e" "wave eventually & stay" tsc--wave-eventually :transient t)
   ("s" "wave surely & leave" tsc--wave-surely :transient nil)])

;; (tsc-wave-keyword-args)

```

Launch the command, wave several times (note timestamp update) and then exit with (**C-g**).


<a id="org18c1f26"></a>

## Macro Child Definition Style

The<code>transient-define-suffix</code>macro can help if you need to bind a command in multiple places and only override some properties for some prefixes. It makes the prefix definition more compact at the expense of a more verbose command.

```elisp

(transient-define-suffix tsc-suffix-wave-macroed ()
  "Prefix that waves with macro-defined suffix."
  :transient t
  :key "T"
  :description "wave from macro definition"
  (interactive)
  (message "Waves from a macro definition at: %s" (current-time-string)))

;; Suffix definition creates a command
;; (tsc-suffix-wave-macroed)
;; Because that's where the suffix object is stored
;; (get 'tsc-suffix-wave-macroed 'transient--suffix)

```

```elisp

;; tsc-suffix-wave-suffix defined above
(transient-define-prefix tsc-wave-macro-defined ()
  "Prefix to wave using a macro-defined suffix."
  [(tsc-suffix-wave-macroed)])
;; note, information moved from prefix to the suffix.

;; (tsc-wave-macro-defined)

```


<a id="orgecfd83c"></a>

## Overriding slots in the prefix definition

Even if you define a property via one of the macros, you can still override that property in the later prefix definition. The example below overrides the<code>:transient</code><code>:description</code> and<code>:key</code>properties of the<code>tsc-suffix-wave</code>suffix defined above:

```elisp

(defun tsc--wave-override ()
  "Vanilla command used to override suffix's commands."
  (interactive)
  (message "This suffix was overridden.  I am what remains."))

(transient-define-prefix tsc-wave-overridden ()
  "Prefix that waves with overridden suffix behavior."
  [(tsc-suffix-wave-macroed
    :transient nil
    :key "O"
    :description "wave overridingly"
    :command tsc--wave-override)]) ; we overrode what the suffix even does

;; (tsc-wave-overridden)

```

If you just list the key and symbol followed by properties, it is also a supported shorthand suffix form:

<code>("wf" tsc-suffix-wave :description "wave furiously")</code>


<a id="org6c91e60"></a>

## Quoting Note for Vectors

Inside the<code>[ ...vectors... ]</code>in<code>transient-define-prefix</code> you don't need to quote symbols because in the vector, everything is a literal. When you move a shorthand style<code>:property symbol</code>out to the<code>transient-define-suffix</code>form, which is a list, you might need to quote the symbol as<code>:property 'symbol</code>


<a id="org317f0a8"></a>

# Groups & Layouts

To define a transient, you need at least one group. Groups are vectors, delimited as<code>[ ...group... ]</code>

There is basic layout support and you can use it to collect or differentiate commands.

If you begin a group vector with a string, you get a group heading. Groups also support some [properties](https://magit.vc/manual/transient/Group-Specifications.html#Group-Specifications). The [group class](elisp:(describe-symbol transient-group)) also has a lot of information.


<a id="org4ca3ec3"></a>

## Descriptions

Very straightforward. Just make the first element in the vector a string or add a<code>:description</code>property, which can be a function.

In the prefix definition of suffixes, the second string is a description.

The<code>:description</code>key is applied last and therefore wins in ambiguous declarations.

```elisp

(transient-define-prefix tsc-layout-descriptions ()
  "Prefix with descriptions specified with slots."
  ["Let's Give This Transient a Title\n" ; yes the newline works
   ["Group One"
    ("wo" "wave once" tsc-suffix-wave)
    ("wa" "wave again" tsc-suffix-wave)]

   ["Group Two"
    ("ws" "wave some" tsc-suffix-wave)
    ("wb" "wave better" tsc-suffix-wave)]]

  ["Bad title" :description "Group of Groups"
   ["Group Three"
    ("k" "bad desc" tsc-suffix-wave :description "key-value wins")
    ("n" tsc-suffix-wave :description "no desc necessary")]
   [:description "Key Only Def"
                 ("wt" "wave too much" tsc-suffix-wave)
                 ("we" "wave excessively" tsc-suffix-wave)]])

;; (tsc-layout-descriptions)

```


### Dynamic Descriptions

**Note:** The property list style for dynamic descriptions is the same for both prefixes and suffixes. Add<code>:description symbol-or-lambda-form</code>to the group vector or suffix list.

```elisp

(transient-define-prefix tsc-layout-dynamic-descriptions ()
  "Prefix that generate descriptions dynamically when transient is shown."
  ;; group using function-name to generate description
  [:description current-time-string
                ("-s" "--switch" "switch=") ; switch just to cause updates
                ;; single suffix with dynamic description
                ("wa" tsc-suffix-wave
                 :description (lambda ()
                                (format "Wave at %s" (current-time-string))))]
  ;; group with anonymoous function generating description
  [:description (lambda ()
                  (format "Group %s" (org-id-new)))
                ("wu" "wave uniquely" tsc-suffix-wave)])

;; (tsc-layout-dynamic-descriptions)

```

-   Errata

    **Note**, the uuid in the group description is generated on every key read, so multi-key sequences cause updates to the descriptions. This is not likely to be changed because layout re-rendering is necessary to indicate the partially complete key sequence. 🤓


### Displaying Information

The<code>transient-information</code>class can be used to show states that are purely informative, not having any keys. Just pass<code>:info</code>in a suffix declaration to create a display-only element. You can use a constant string or a function for reactivity.

```elisp

(defun tsc--random-info ()
  (format "Temperature outside: %d" (random 100)))

(transient-define-prefix tsc-information ()
    "Prefix that displays some information."
    ["Group Header"
     (:info "Basic info")
     (:info #'tsc--random-info)
     (:info "Use :format to remove whitespace" :format "%d")
     ("k" :info "Keys will be greyed out")
     "" ; empty line
     ("wg" "wave greenishly" tsc-suffix-wave)])

```


<a id="org7ba8f06"></a>

## Layouts

The default behavior treats groups a little differently depending on how they are nested. For most simple groupings, this is sufficient control.


### Groups one on top of the other

Use a vector for each row.

```elisp

(transient-define-prefix tsc-layout-stacked ()
  "Prefix with layout that stacks groups on top of each other."
  ["Top Group" ("wt" "wave top" tsc-suffix-wave)]
  ["Bottom Group" ("wb" "wave bottom" tsc-suffix-wave)])

;; (tsc-layout-stacked)

```


### Groups side by side

Use a vector of vectors for columns.

```elisp

(transient-define-prefix tsc-layout-columns ()
  "Prefix with side-by-side layout."
  [["Left Group" ("wl" "wave left" tsc-suffix-wave)]
   ["Right Group" ("wr" "wave right" tsc-suffix-wave)]])

;; (tsc-layout-columns)

```


### Group on top of groups side by side

Vector on top of vector inside a vector.

```elisp

(transient-define-prefix tsc-layout-stacked-columns ()
  "Prefix with stacked columns layout."
  ["Top Group"
   ("wt" "wave top" tsc-suffix-wave)]

  [["Left Group"
    ("wl" "wave left" tsc-suffix-wave)]
   ["Right Group"
    ("wr" "wave right" tsc-suffix-wave)]])

;; (tsc-layout-stacked-columns)

```

**Note: Groups can have groups or suffixes, but not both. You can't mix suffixes alongside groups in the same vector. The resulting transient will error when invoked.**


### Empty strings make spaces

Groups that are empty or only space have no effect. This situation can happen with layouts that update dynamically. See [dynamic layouts](#org6c78b1e).

```elisp

(transient-define-prefix tsc-layout-spaced-out ()
  "Prefix lots of spacing for users to space out at."
  ["" ; cannot add another empty string because it will mix suffixes with groups
   ["Left Group"
    ""
    ("wl" "wave left" tsc-suffix-wave)
    ("L" "wave lefter" tsc-suffix-wave)
    ""
    ("bl" "wave bottom-left" tsc-suffix-wave)
    ("z" "zone\n" zone)] ; the newline does pad

   [[]] ; empty vector will do nothing

   [""] ; vector with just empty line has no effect

   ;; empty group will be ignored
   ;; (useful for hiding in dynamic layouts)
   ["Empty Group\n"]

   ["Right Group"
    ""
    ("wr" "wave right" tsc-suffix-wave)
    ("R" "wave righter" tsc-suffix-wave)
    ""
    ("br" "wave bottom-right" tsc-suffix-wave)]])

;; (tsc-layout-spaced-out)

```


### A Grid

So, you put columns into rows that are in columns and stuff like that. This can be achieved with or without explicit column settings.

```elisp

(transient-define-prefix tsc-layout-the-grid ()
  "Prefix with groups in a grid-like arrangement."

  [:description
   "The Grid\n" ; must use slot or macro is confused
   ["Left Column" ; note, no newline
    ("ltt" "left top top" tsc-suffix-wave)
    ("ltb" "left top bottom" tsc-suffix-wave)
    ""
    ("lbt" "left bottom top" tsc-suffix-wave)
    ("lbb" "left bottom bottom" tsc-suffix-wave)] ; note, no newline

   ["Right Column\n"
    ("rtt" "right top top" tsc-suffix-wave)
    ("rtb" "right top bottom" tsc-suffix-wave)
    ""
    ("rbt" "right bottom top" tsc-suffix-wave)
    ("rbb" "right bottom bottom\n" tsc-suffix-wave)]])

;; (tsc-layout-the-grid)

```

**Note**, only<code>transient-columns</code> not<code>transient-column</code>can act as a group of groups.


<a id="orgd6b49a7"></a>

## Manually setting group class

If you need to override the class that the<code>transient-define-prefix</code>macro would normally use.

```elisp

(transient-define-prefix tsc-layout-explicit-classes ()
  "Prefix with group class used to explicitly specify layout."
  [:class transient-row "Row"
          ("l" "wave left" tsc-suffix-wave)
          ("r" "wave right" tsc-suffix-wave)]
  [:class transient-column "Column"
          ("t" "wave top" tsc-suffix-wave)
          ("b" "wave bottom" tsc-suffix-wave)])

;; (tsc-layout-explicit-classes)

```


<a id="org2a99aa0"></a>

## Pad Keys

To align descriptions, set the group's :pad-keys to t

```elisp

(transient-define-prefix tsc-layout-padded-keys ()
  "Prefix with padded keys to align descriptions."
  ["Padded Column"
   :class transient-column
   :pad-keys t
   ("t" "wave top" tsc-suffix-wave) ; spaces will be inserted after t
   ("realyongk" "wave bottom" tsc-suffix-wave)])

;; (tsc-layout-padded-keys)

```

Use this if you have different lengths of key sequences or your transient is dynamic and not all keys will have the same length all the time.


<a id="org7844f5c"></a>

# Nesting & Flow Control

Many transients call other transients. This allows you to express similar behaviors as interactive commands that ask you for multiple arguments using the minibuffer.

Transient has more options for retaining some state across several transients, making it easier to compose commands and to retain intermediate states for rapidly achieving series of actions over similar inputs.


<a id="orga65b543"></a>

## Single versus multiple commands

Sometimes you want to execute multiple commands without re-opening the transient. It's the same idea as [god mode](https://github.com/emacsorphanage/god-mode) or Evil repeat.

```elisp

(transient-define-prefix tsc-stay-transient ()
  "Prefix where some suffixes do not exit."
  ["Exit or Not?"

   ;; this suffix will not exit after calling sub-prefix
   ("we" "wave & exit" tsc-wave-overridden)
   ("ws" "wave & stay" tsc-suffix-wave :transient t)])

;; (tsc-stay-transient)

```

**Note**, if<code>tsc-wave</code>was used in both exit & stay, the<code>:transient</code>slot would be clobbered and we would only get one behavior. Beware of re-using the same object instances in the same layout. Move the<code>:transient</code>slot override between the two suffixes to see the change in behavior.


<a id="org84db863"></a>

## Nesting

Nesting is putting transients inside other transients, creating user-input sequences like:

<code>Prefix -> Sub-Prefix -> Suffix</code>


### Binding a Sub-Prefix

This is the most simple way to create nesting.

```elisp

(transient-define-prefix tsc--simple-child ()
  ["Simple Child"
   ("wc" "wave childishly" tsc-suffix-wave)])

(transient-define-prefix tsc-simple-parent ()
  "Prefix that calls a child prefix."
  ["Simple Parent"
   ("w" "wave parentally" tsc-suffix-wave)
   ("b" "become child" tsc--simple-child)])

;; (tsc--simple-child)
;; (tsc-simple-parent)

```

-   Nesting with multiple commands

    Declaring a nested prefix that "returns" to its parent has a convenient shorthand form.

    ```elisp

    (transient-define-prefix tsc-simple-parent-with-return ()
      "Prefix with a child prefix that returns."
      ["Parent With Return"
       ("w" "wave parentally" tsc-suffix-wave)
       ("b" "become child with return" tsc--simple-child :transient t)])

    ;; Child does not "return" when called independently
    ;; (tsc--simple-child)
    ;; (tsc-simple-parent-with-return)

    ```


### Setting up another transient manually

If you call<code>(transient-setup 'transient-command-symbol)</code> you will activate a replacement transient.

This form is useful if you want a command to *perhaps* load yet another transient in some situation. You may even just want to load the same transient with different context, such as passing in a new [scope](#orgfb6d9c4).

```elisp

(transient-define-suffix tsc-suffix-setup-child ()
  "A suffix that uses `transient-setup' to manually load another transient."
  (interactive)
  ;; note that it's usually during the post-command side of calling the
  ;; command that the actual work to set up the transient will occur.
  ;; This is an implementation detail because it depends if we are calling
  ;; `transient-setup' while already transient or not.
  (transient-setup 'tsc--simple-child))

(transient-define-prefix tsc-parent-with-setup-suffix ()
  "Prefix with a suffix that calls `transient-setup'."
  ["Simple Parent"
   ("wp" "wave parentally" tsc-suffix-wave :transient t) ; remain transient

   ;; You may need to specify a different pre-command (the :transient) key
   ;; because we need to clean up this transient or create some conditions
   ;; to trigger the following transient correctly.  This example will
   ;; work with `transient--do-replace' or no custom pre-command

   ("bc" "become child" tsc-suffix-setup-child
    :transient transient--do-replace)])

;; (tsc-parent-with-setup-suffix)

```

⚠️ When the child is calling<code>transient-setup</code> it will not be possible to use<code>transient--do-return</code>or<code>transient--do-recurse</code>to get back to the parent unless you explicitly cooperate with the transient state implementation, which may not be stable between versions.


<a id="org3e1db44"></a>

## Mixing Interactive

You can mix normal Emacs completion flows with transient UI's.

See [Interactive codes](info:elisp#Interactive Codes) are listed in the Elisp manual.

**Note**, this also works when binding existing commands that recieve user input.

```elisp

(transient-define-suffix tsc--suffix-interactive-string (user-input)
  "An interactive suffix that obtains string input from the user."
  (interactive "sPlease just tell me what you want!: ")
  (message "I think you want: %s" user-input))

(transient-define-suffix tsc--suffix-interactive-buffer-name (buffer-name)
  "An interactive suffix that obtains a buffer name from the user."
  (interactive "b")
  (message "You selected: %s" buffer-name))

(transient-define-prefix tsc-interactive-basic ()
  "Prefix with interactive user input."
  ["Interactive Command Suffixes"
   ("s" "enter string" tsc--suffix-interactive-string)
   ("b" "select buffer" tsc--suffix-interactive-buffer-name)])

;; (tsc-interactive-basic)

```


### Early return

Sometimes you can complete your work without asking the user for more input. In the custom body for a prefix, if you decline to call<code>transient-setup</code> then the command will just exit with no problems.

Below is a nested transient.

-   The body form of the nested child can return early without loading a new transient
-   The parent uses<code>transient--do-recurse</code>to make it's child "return" to it
-   The "radiations" command in the child explicitly overrides this, using<code>transient--do-exit</code>so that it *does not* return to the parent

These possible values for<code>:transient</code>change between transient versions. See the [transient](info:transient#Transient State) manual.

```elisp

(defvar tsc--complex nil "Show complex menu or not.")

(transient-define-suffix tsc--toggle-complex ()
  "Toggle `tsc--complex'."
  :transient t
  :description (lambda () (format "toggle complex: %s" tsc--complex))
  (interactive)
  (setf tsc--complex (not tsc--complex))
  (message (propertize (concat "Complexity set to: "
                               (if tsc--complex "true" "false"))
                       'face 'success)))

(transient-define-prefix tsc-complex-messager ()
  "Prefix that sends complex messages, unles `tsc--complex' is nil."
  ["Send Complex Messages"
   ("s" "snow people"
    (lambda () (interactive)
      (message (propertize "snow people! ☃" 'face 'success))))
   ("k" "kitty cats"
    (lambda () (interactive)
      (message (propertize "🐈 kitty cats! 🐈" 'face 'success))))
   ("r" "radiations"
    (lambda () (interactive)
      (message (propertize "Oh no! radiation! ☢" 'face 'success)))
    ;; radiation is dangerous!
    :transient transient--do-exit)]

  (interactive)
  ;; The command body either sets up the transient or simply returns
  ;; This is the "early return" we're talking about.
  (if tsc--complex
      (transient-setup 'tsc-complex-messager)
    (message "Simple and boring!")))

(transient-define-prefix tsc-simple-messager ()
  "Prefix that toggles child behavior!"
  [["Send Message"
    ;; using `transient--do-recurse' causes suffixes in tsc-child to perform
    ;; `transient--do-return' so that we come back to this transient.
    ("m" "message" tsc-complex-messager :transient transient--do-recurse)]
   ["Toggle Complexity"
    ("t" tsc--toggle-complex)]])

;; (tsc-simple-messager)
;; does not "return" when called independently
;; (tsc-complex-messager)

```


<a id="org2da15d9"></a>

## Pre-Commands Explained

The value in the<code>:transient</code>slot affects the state of variables that the body of your command will see and what will happen after your command, during the post-command.

The<code>:transient</code>slot holds a function symbol called the "pre-command." Before your suffix body forms run, the pre-command is called and creates the conditions that your suffix may use to, for example, read variables that were just set on infixes. If the pre-command calls<code>transient-export</code>then it will add to history.

When using macros to define prefixes, you will often see<code>:transient</code>set to<code>t</code> In<code>transient-define-prefix</code>and<code>transient-define-suffix</code> the<code>t</code>value is actually translated to<code>transient--do-call</code>or<code>transient--do-recurse</code>depending on the situation.

These functions set up some states so that post-command can figure out if it needs to exit, save values, or enter another transient, and what else to do while entering that new transient.

The [official long manual](https://magit.vc/manual/transient.html#Transient-State) has some more detail. These examples should prepare you to visualize the forms used in those explanations.


### Warning! ⚠️

Some of the **trickiest bugs you can introduce** will happen when using the following variables and functions at varying points in command lifecycles:

-   <code>transient-current-command</code>
-   <code>transient--command</code>
-   <code>transient-current-prefix</code>
-   <code>transient--prefix</code>
-   <code>transient-args</code>

During the pre-command and post-command, these can change. When you are overriding the pre-command, you may discover things such as the result of<code>transient-args</code>changing. Calling<code>transient-setup</code>may update things. Even if you call<code>transient-args</code>on on the specific transient, the results change during the lifecycle and depending on the pre-command.

**In particular** it seems like layout predicates should use<code>transient--prefix</code>while suffix bodies should use<code>transient-current-prefix</code>

Not all pre-commands are compatible with all situations and suffixes!

[Debugging](#org871fa38)

-   TODO Errata

    There's definitely some edge cases that are unnecessarily complex for the use case. Think of how life was before<code>transient--do-recurse</code>


<a id="orga2d72fe"></a>

# Using & Managing State

There are several ways to create state. The [flow control](#org7844f5c) examples in the previous section mainly covered how to get from one command to the other. This section covers how to save values and then read them later, sometimes from a completely different transient. **Coupled with [custom infix types](#org47828e0), you can create some seriously rich user expression.**

To spark your imagination, here's a non-exhaustive list of how to get data into your commands:

-   Interactive forms
-   Prefix arguments <code>C-u</code>universal argument)
-   Setting the scope in<code>transient-setup</code>
-   Obtaining a scope in a custom<code>transient-init-scope</code>method
-   Default values in prefix definition
-   Saved values of infixes
-   Saved values in other infixes / prefixes with shared<code>history-key</code>
-   User-set infix values from the current or parent prefix
-   Ad-hoc values in regular<code>defvar</code>and<code>defcustom</code>etc
-   Reading values from another, perhaps distant prefix
-   Arguments passed into interactive commands to call them as normal elisp functions


<a id="orgf886f46"></a>

## The Magic of Transient

Using all of these mechanisms, you can enable users to rapidly construct complex command sentences, sentences with phrases. You can basically make a user interface as expressive as elisp.

A user input sequence like this:

<code>Prefix -> Interactive -> Sub-Prefix -> Infix -> Suffix -> Suffix -> ...</code>

Is basically the same as doing this in elisp:

```elisp

(let ((input (Sub-Prefix (Prefix (Interactive))))
      (infix (Infix)))
  (suffix input infix)
  (suffix input infix))

```

With history, you can remember lots of these states. This allows the user to quickly fire off lots of mostly completed partial expressions. They are scoped, so you can keep state over different contexts.

This is what is meant by "creating user interfaces as expressive as elisp."

Because interactive forms and transients are both still just consuming linear user input, they ultimately have the same capabilities, but if you *think* in terms of partially constructed elisp expressions, you can do more than if the user has to enter in contextless commands over and over or write more commands while managing their own state in ad-hoc fashion.

Transient's UI also provides greater awareness to the user of the current state. This makes it easier for the user to achieve the greater complexity that is intended, without remembering the command language you are designing for your application.


<a id="org36e2c5c"></a>

## Infixes

Functions need arguments. Infixes are specialized suffixes with behavior defaults that make sense for setting and storing values for consumption in suffixes. It's like passing arguments into the suffix. They also have support for persisting state across invocations and Emacs sessions.


### Basic Infixes

Infix classes built-in all descend from<code>transient-infix</code>and can be seen clearly in the<code>eieio-browse</code> View their slots and documentation with<code>(describe-class transient-infix)</code>etc. Here you can see what most infixes look like and how they behave.

```elisp

;; infix defined with a macro
(transient-define-argument tsc--exclusive-switches ()
  "This is a specialized infix for only selecting one of several values."
  :class 'transient-switches
  :argument-format "--%s-snowcone"
  :argument-regexp "\\(--\\(grape\\|orange\\|cherry\\|lime\\)-snowcone\\)"
  :choices '("grape" "orange" "cherry" "lime"))

(transient-define-prefix tsc-basic-infixes ()
  "Prefix that just shows off many typical infix types."
  ["Infixes"

   ;; from macro
   ("-e" "exclusive switches" tsc--exclusive-switches)

   ;; shorthand definitions
   ("-b" "switch with shortarg" ("-w" "--switch-short"))
   ;; note :short-arg != :key

   ("-s" "switch" "--switch")
   ( "n" "no dash switch" "still works")
   ("-a" "argument" "--argument=" :prompt "Let's argue because: ")

   ;; a bit of inline EIEIO in our shorthand
   ("-n" "never empty" "--non-null=" :always-read t  :allow-empty nil
    :init-value (lambda (obj) (oset obj value "better-than-nothing")))

   ("-c" "choices" "--choice=" :choices (foo bar baz))]

  ["Show Args"
   ("s" "show arguments" tsc-suffix-print-args)])

;; (tsc-basic-infixes)

```


### Reading Infix Values

**Reminder** in the section on [pre-commands](#org2da15d9) the discussion about the<code>:transient</code>mentions that the values available in a suffix body depend on whether the pre-command called<code>transient--export</code>before evaluating the suffix body.

There are two basic ways to read infixes:

-   <code>(transient-args transient-current-command)</code>and parse manually
-   <code>(transient-arg-value "--argument-" (transient-args transient-current-command)</code>
-   <code>(transient-suffixes transient-current-command)</code>and retrieve your fully hydrated suffix

-   TODO The `transient-suffixes` option requires filtering

    In my opinion the API should make it easier to get raw values from suffixes, but this is also a matter of custom infixes needing to serialize values correctly so that<code>transient-arg-value</code>will "just work".


<a id="orgfb6d9c4"></a>

## Scope

When you call a function with an argument, you want to know in the body of your function what that argument was. This is the scope. The prefix is initialized with the<code>:scope</code>either when it was set up or by its own body or interactive form.

Suffixes can then read back that scope in their body by calling<code>transient-scope</code> The suffix object is given the scope and can use it to alter its own display or behavior. The layout also can interpret the scope while it is initializing.

```elisp

(transient-define-suffix tsc--read-prefix-scope ()
  "Read the scope of the prefix."
  :transient 'transient--do-call
  (interactive)
  (let ((scope (transient-scope)))
    (message "scope: %s" scope)))

(transient-define-suffix tsc--double-scope-re-enter ()
  "Re-enter the current prefix with double the scope."
  ;; :transient 'transient--do-replace ; builds up the stack
  :transient 'transient--do-exit
  (interactive)
  (let ((scope (transient-scope)))
    (if (numberp scope)
        (transient-setup transient-current-command
                         nil nil :scope (* scope 2))
      (message
       (propertize
        (format "scope was non-numeric! %s" scope) 'face 'warning))
      (transient-setup transient-current-command))))

(transient-define-suffix tsc--update-scope-with-prefix-re-enter (new-scope)
  "Re-enter the prefix with double the scope."
  ;; :transient 'transient--do-replace ; builds up the stack
  :transient 'transient--do-exit ; do not build up the stack
  (interactive "P")
  (message "universal arg: %s" new-scope)
  (transient-setup transient-current-command nil nil :scope new-scope))

(transient-define-prefix tsc-scope (scope)
  "Prefix demonstrating use of scope."

  ;; note!  this is a location where we definitely had to use
  ;; `transient--prefix' or get the transient object from the tsc-scope
  ;; symbol.  `transient-current-prefix' would not be correct here!
  [:description
   (lambda () (format "Scope: %s" (transient-scope)))
   [("r" "read scope" tsc--read-prefix-scope)
    ("d" "double scope" tsc--double-scope-re-enter)
    ("o" "update scope (use prefix argument)"
     tsc--update-scope-with-prefix-re-enter)]]
  (interactive "P")
  (transient-setup 'tsc-scope nil nil :scope scope))

;; Setting an interactive argument for `eval-last-sexp' is a
;; little different
;; (let ((current-prefix-arg 4)) (call-interactively 'tsc-scope))

;; (tsc-scope)
;; Then press "C-u 4 o" to update the scope
;; Then d to double
;; Then r to read
;; ... and so on
;; C-g to exit

```


### Reading Scope Directly

⚠️ **WARNING** When writing predicates, that read or write the scope using<code>oref</code>and<code>oset</code> you will need to determine whether<code>transient--prefix</code>or<code>transient-current-prefix</code>is correct. When called from a parent transient, you might end up picking the wrong prefix to read from at the wrong time. These variables change throughout the lifecycle! Use [edebug](#org4b7029d) you must!


### TODO Errata with prefix arg (`C-u` universal argument).

Key binding sequences, such as<code>wa</code>instead of single-key prefix bindings, will unset the prefix argument (the old-school Emacs<code>C-u</code>prefix argument, not the prefix's scope or other explicit arguments)

**Possibly a bug in transient.**


<a id="orged47b55"></a>

## Prefix Value & History

Briefly, there are three locations for state you need to be aware of for this section:

-   Each transient's prefix object has a<code>:value</code>that is updated by<code>transient-set</code>and<code>transient-save</code>
-   The values obtained from<code>transient-args</code>are usually quite ephemeral and don't even persist beyond the body of form of the suffixes you usually read them in
-   <code>transient-values</code>contains saved values that are used to re-hydrate the prefix<code>:value</code>slot when the prefix is created
-   <code>transient-history</code>is used to make it faster for the user to flip through previous states (which can have independent histories for infixes and prefixes). These are never used unless calling<code>transient-history-prev</code>and<code>transient-history-next</code>

We can get this as a list of strings for any prefix by calling<code>transient-args</code>on<code>transient-current-command</code>in the suffix's interactive form. If you know the command you want the value of, you can use it's symbol instead of<code>transient-current-command</code>

This is related to history keys. If you set the arguments and then save them using <code>C-x s</code> for the command<code>transient-save</code> not only will the transient be updated with the new value, but if you call the child independently, it can still read the value from the suffix.

```elisp

(transient-define-suffix tsc-suffix-eat-snowcone (args)
  "Eat the snowcone!
This command can be called from it's parent, `tsc-snowcone-eater' or independently."
  :transient t
  ;; you can use the interactive form of a command to obtain a default value
  ;; from the user etc if the one obtained from the parent is invalid.
  (interactive (list (transient-args 'tsc-snowcone-eater)))

  ;; `transient-arg-value' can (with varying success) pick out individual
  ;; values from the results of `transient-args'.

  (let ((topping (transient-arg-value "--topping=" args))
        (flavor (transient-arg-value "--flavor=" args)))
    (message "I ate a %s flavored snowcone with %s on top!" flavor topping)))

(transient-define-prefix tsc-snowcone-eater ()
  "Prefix demonstrating set & save infix persistence."

  ;; This prefix has a default value that tsc-suffix-eat-snowcone can see
  ;; even before the prefix has been called.
  :value '("--topping=fruit" "--flavor=cherry")

  ;; always-read is used below so that you don't save nil values to history
  ["Arguments"
   ("-t" "topping" "--topping="
    :choices ("ice cream" "fruit" "whipped cream" "mochi")
    :always-read t)
   ("-f" "flavor" "--flavor="
    :choices ("grape" "orange" "cherry" "lime")
    :always-read t)]

  ;; Definitely check out the =C-x= menu
  ["C-x Menu Behaviors"
   ("S" "save snowcone settings"
    (lambda () (interactive) (message "saved!") (transient-save))
    :transient t)
   ("R" "reset snowcone settings"
    (lambda () (interactive) (message "reset!") (transient-reset))
    :transient t)]

  ["Actions"
   ("m" "message arguments" tsc-suffix-print-args)
   ("e" "eat snowcone" tsc-suffix-eat-snowcone)])

;; First call will use the transient's default value
;; M-x tsc-suffix-eat-snowcone or `eval-last-sexp' below
;; (call-interactively 'tsc-suffix-eat-snowcone)
;; (tsc-snowcone-eater)
;; Eat some snowcones with different flavors
;; ...
;; ...
;; ...
;; Now save the value and exit the transient.
;; When you call the suffix independently, it can still read the saved values!
;; M-x tsc-suffix-eat-snowcone or `eval-last-sexp' below
;; (call-interactively 'tsc-suffix-eat-snowcone)

```

It's worth bringing up the [`transient-show-common-commands`](elisp:(describe-variable 'transient-show-common-commands)) variable. **You may want to set this when working on the history support for your transients.** Otherwise, just remember the <code>C-x</code> menu inside transients.


<a id="org66d5912"></a>

## History Keys

History lets you **set** infixes using prior values. It's per-prefix, per-suffix usually. Using previous examples like<code>tsc-snowcone-eater</code> you can flip through history using:

-   <code>C-x p</code>for<code>transient-history-prev</code>
-   <code>C-x n</code>for<code>transient-history-next</code>

These bindings are revealed when<code>transient-show-common-commands</code>is<code>t</code>or when you hit the<code>C-x</code>prefix.

However, what if you **don't** want a unique history for some infixes or even prefixes?

**Note** As a more advanced example, using EIEIO and dynamic layout techniques to modify the slot of<code>:history-key</code> you can also make unique histories for the same prefix/infix by setting that slot value depending on the context you want unique histories for.

The following example can demonstrate the behavior with some user effort:

```elisp

(transient-define-prefix tsc-ping ()
  "Prefix demonstrating history sharing."

  :history-key 'non-unique-name

  ["Ping"
   ("-g" "game" "--game=")
   ("p" "ping the pong" tsc-pong)
   ("a" "print args" tsc-suffix-print-args :transient nil)])

(transient-define-prefix tsc-pong ()
  "Prefix demonstrating history sharing."

  :history-key 'non-unique-name

  ["Pong"
   ("-g" "game" "--game=")
   ("p" "pong the ping" tsc-ping)
   ("a" "print args" tsc-suffix-print-args :transient nil)])

;; (tsc-ping)
;; Okay here's where it gets weird
;; 1.  Set the value of game to something and remember it
;; 2.  Press a to print the args
;; 3.  Re-open tsc-ping.
;; 4.  C-x p to load the previous history, see the old value?
;; 5.  p to switch to the tsc-pong transient
;; 6.  C-x p to load the previous history, see the old value from tsc-ping???
;; 7. Note that tsc-pong uses the same history as tsc-ping!

```


### Detangling with Initialization, Setting, and Saving

Set values show up in the prefix's<code>:value</code>slot.

```elisp

(oref (plist-get (symbol-plist 'tsc-ping) 'transient--prefix) value)

```

The prefix value will get the last value that was **set** using<code>transient-set</code>

However, the prefix value shown in<code>transient-values</code>is only updated when calling<code>transient-save</code>

Saved values show up in<code>transient-values</code> If you save<code>tsc-ping</code> you can see the saved value here:

```elisp

(assoc 'tsc-ping transient-values)

```

**These two values may be independent.** They are written at the same time when calling<code>transient-save</code> During prefix initialization, the<code>:value</code>is written from<code>transient-values</code>

Play with the<code>tsc-snowcone-eater</code>and<code>tsc-ping</code>and<code>tsc-pong</code>in the<code>C-x</code>menu while also looking at what gets stored in<code>transient-values</code><code>transient-history</code>and the prefix's slots.

When you re-evaluate the prefix or reload Emacs, you will see the result of initialization from<code>transient-values</code>


<a id="orge041b5d"></a>

## Disabling Set / Save on a Suffix

To disable saving and setting values, causing a prefix to always end up using the default value, set the<code>:unsavable</code>slot to<code>t</code>

```elisp

(transient-define-prefix tsc-goldfish ()
  "A prefix that cannot remember anything."
  ["Goldfish"
   ("-r" "rememeber" "--i-remember="
    :unsavable t ; infix isn't saved
    :always-read t ; infix always asks for new value
    ;; overriding the method to provide a starting value
    :init-value (lambda (obj) (oset obj value "nothing")))
   ("a" "print args" tsc-suffix-print-args :transient nil)])

;; (tsc-goldfish)

```

Try to update<code>remember</code>and then set and save it in the<code>C-x</code>menu. Reload it. It will never pay attention to history or setting & saving the transient value.


<a id="org041edbe"></a>

## Setting or Saving Every Time a Suffix is Used

```elisp

(transient-define-suffix tsc-suffix-remember-and-wave ()
  "Wave, and force the prefix to set it's saveable infix values."
  (interactive)

  ;; (transient-reset) ; forget
  (transient-set) ; save for this session
  ;; If you combine reset with set, you get a reset for future sessions only.
  ;; (transient-save) ; save for this and future sessions
  ;; (transient-reset-value some-other-prefix-object)

  (message "Waves at user at: %s.  You will never be forgotten." (current-time-string)))

(transient-define-prefix tsc-elephant ()
  "A prefix that always remembers its infixes."
  ["Elephant"
   ("-r" "rememeber" "--i-remember="
    :always-read t)
   ("w" "remember and wave" tsc-suffix-remember-and-wave)
   ("a" "print args (skips remembering)" tsc-suffix-print-args
    :transient nil)])

;; (tsc-elephant)

```


### TODO Sticky infix support

There needs to be a slot that causes infixes to always be set on export. This would cover cases where the most frequent user input changes just rapidly enough that both setting every time and saving are equally inconvenient. Using<code>transient-set</code>is kind of brute-ish.


### Default Values

Every transient prefix has a value. It's a list. You can set it to create defaults for switches and arguments.

```elisp

(transient-define-prefix tsc-default-values ()
  "A prefix with a default value."

  :value '("--toggle" "--value=5")

  ["Arguments"
   ("t" "toggle" "--toggle")
   ("v" "value" "--value=" :prompt "an integer: ")]

  ["Show Args"
   ("s" "show arguments" tsc-suffix-print-args)])

;; (tsc-default-values)

```

**Note**, after setting or saving a value on this transient using the<code>C-x</code>menu, the next time the transient is set up, it will have a different value. If you want the default to return, use<code>transient-reset</code>in your suffix.


### Readers

Readers are the mechanism to provide completions and to enforce input validity of infixes.

```elisp

(transient-define-prefix tsc-enforcing-inputs ()
  "A prefix with enforced input type."

  ["Arguments"
   ("v" "value" "--value=" :prompt "an integer: " :reader transient-read-number-N+)]

  ["Show Args"
   ("s" "show arguments" tsc-suffix-print-args)])

;; (tsc-enforcing-inputs)

```

Setting the reader can be used to enforce rules of valid input. See [Advanced/Custom Infix Types](#org47828e0) for an example of writing a custom reader that validates input and assigning that reader via the class method instead of the<code>:reader</code>slot.


<a id="orgf0b743d"></a>

## Lisp Variables

Lisp variables are currently at an experimental support level. They way they work is to report and set the value of a lisp symbol variable. Because they aren't necessarilly intended to be printed as crude CLI arguments, they **DO NOT** appear in<code>(transient-args 'prefix)</code>but this is fine because you can just use the variable.

Customizing this class can be useful when working with objects and functions that exist entirely in elisp.

```elisp

(defvar tsc--position '(0 0) "A transient prefix location.")

(transient-define-infix tsc--pos-infix ()
 "A location, key, or command symbol."
 :class 'transient-lisp-variable
 :transient t
 :prompt "An expression such as (0 0), \"p\", nil, 'tsc--msg-pos: "
 :variable 'tsc--position)

(transient-define-suffix tsc--msg-pos ()
 "Message the element at location."
 :transient 'transient--do-call
 (interactive)
 ;; lisp variables are not sent in the usual (transient-args) list.
 ;; Just read `tsc--position' directly.
 (let ((suffix (transient-get-suffix
                transient-current-command tsc--position)))
   (message "%s" (oref suffix description))))

(transient-define-prefix tsc-lisp-variable ()
 "A prefix that updates and uses a lisp variable."
 ["Location Printing"
  [("p" "position" tsc--pos-infix)]
  [("m" "message" tsc--msg-pos)]])

;; (tsc-lisp-variable)

```


<a id="org121c678"></a>

# Controlling CLI's

This section covers more usages of infixes, focused on creating better argument strings for CLI tools.

The section on [flow control](#org7844f5c) & [managing state](#orga2d72fe) has more information about controlling elisp applications.


<a id="org57b9691"></a>

## Reading arguments within suffixes

**Note:** these forms are generic for different prefixes, allowing you to mix and match suffixes within prefixes.


<a id="org4813e75"></a>

## Switches & Arguments Again

The shorthand forms in<code>transient-define-prefix</code>are heavily influenced by the CLI style switches and arguments that transient was built to control. Most shorthand forms look like so:

<code>("key" "description" "argument")</code>

The macro will select the infix's exact class depending on how you write<code>:argument</code> If you write something ending in<code>=</code>such as<code>--value=</code>then you get<code>:class transient-option</code>but if not, the default is a<code>:class transient-switch</code>

Use [`(describe-symbol transient-option)`](elisp:(describe-symbol transient-option)) and [`(describe-symbol transient-switch)`](elisp:(describe-symbol transient-switch)) to see a full document of their slots and methods.

If you need an argument with a space instead of the equal sign, use a space and force the infix to be an argument by setting<code>:class transient-option</code>

```elisp

(transient-define-prefix tsc-switches-and-arguments (arg)
  "A prefix with switch and argument examples."
  [["Arguments"
    ("-s" "switch" "--switch")
    ("-a" "argument" "--argument=")
    ("t" "toggle" "--toggle")
    ("v" "value" "--value=")]

   ["More Arguments"
    ("-f" "argument with forced class" "--forced-class "
     :class transient-option)
    ("I" "argument with inline" ("-i" "--inline-shortarg="))
    ("S" "inline shortarg switch" ("-n" "--inline-shortarg-switch"))]]

  ["Commands"
   ("w" "wave some" tsc-suffix-wave)
   ("s" "show arguments" tsc-suffix-print-args)])
;; use to `tsc-suffix-print-args' to analyze the switch values

;; (tsc-switches-and-arguments)

```


### Argument and Infix Macros

If you need to fine-tune a switch (boolean infix), use<code>transient-define-infix</code> Likewise, use<code>transient-define-argument</code>for fine-tuning an argument. The class definitions can be used as a reference while the [manual](https://magit.vc/manual/transient/Suffix-Slots.html#Slotsc-of-transient_002dinfix) provides more explanation.

```elisp

(transient-define-infix tsc--random-init-infix ()
  "Switch on and off."
  :argument "--switch"
  :shortarg "-s" ; will be used for :key when key is not set
  :description "switch"
  :init-value (lambda (obj)
                (oset obj value
                      (eq 0 (random 2))))) ; write t with 50% probability

(transient-define-prefix tsc-maybe-on ()
  "A prefix with a randomly intializing switch."
  ["Arguments"
   (tsc--random-init-infix)]
  ["Show Args"
   ("s" "show arguments" tsc-suffix-print-args)])

;; (tsc-maybe-on)
;; (tsc-maybe-on)
;; ...
;; Run the command a few times to see the random initialization of
;; `tsc--random-init-infix'
;; It will only take more than ten tries for one in a thousand users.
;; Good luck.

```


### Choices

Choices can be set for an argument. The property API and<code>transient-define-argument</code>are equivalent for configuring choices. You can either hard-code or generate choices.

```elisp

(transient-define-argument tsc--animals-argument ()
  "Animal picker."
  :argument "--animals="
  ;; :multi-value t
  ;; :multi-value t means multiple options can be selected at once, such as:
  ;; --animals=fox,otter,kitten etc
  :class 'transient-option
  :choices '("fox" "kitten" "peregrine" "otter"))

(transient-define-prefix tsc-animal-choices ()
  "Prefix demonstrating selecting animals from choices."
  ["Arguments"
   ("-a" "--animals=" tsc--animals-argument)]
  ["Show Args"
   ("s" "show arguments" tsc-suffix-print-args)])

;; (tsc-animal-choices)

```

-   Choices shorthand in prefix definition

    Choices can also be defined in a shorthand form. Use<code>:class 'transient-option</code>if you need to force a different class to be used.

    ```elisp

    (transient-define-prefix tsc-animal-choices-shorthand ()
      "Prefix demonstrating the shorthand style of defining choices."
      ["Arguments"
       ("-a" "Animal" "--animal=" :choices ("fox" "kitten" "peregrine" "otter"))]
      ["Show Args"
       ("s" "show arguments" tsc-suffix-print-args)])

    ;; (tsc-animal-choices-shorthand)

    ```


### Mutually Exclusive Switches

An argument with<code>:class transient-switches</code>may be used if a set of switches is exclusive. The key will likely *not* match the short argument. Regex is used to tell the interface that you are entering one of the choices. The selected choice will be inserted into<code>:argument-format</code> The<code>:argument-regexp</code>must be able to match any of the valid options.

**The UX on mutually exclusive switches is a bit of a pain to discover. You must repeatedly press<code>:key</code>in order to cycle through the options.**

```elisp

(transient-define-argument tsc--snowcone-flavor ()
  :description "Flavor of snowcone."
  :class 'transient-switches
  :key "f"
  :argument-format "--%s-snowcone"
  :argument-regexp "\\(--\\(grape\\|orange\\|cherry\\|lime\\)-snowcone\\)"
  :choices '("grape" "orange" "cherry" "lime"))

(transient-define-prefix tsc-exclusive-switches ()
  "Prefix demonstrating exclusive switches."
  :value '("--orange-snowcone")

  ["Arguments"
   (tsc--snowcone-flavor)]
  ["Show Args"
   ("s" "show arguments" tsc-suffix-print-args)])

;; (tsc-exclusive-switches)

```


### Incompatible Switches

If you need to prevent arguments in a group from being set simultaneously, you can set the prefix property<code>:incompatible</code>and a list of the long-style argument.

Use a list of lists, where each sub-list is the long argument style. Match the string completely, including use of<code>=</code>in both arguments and switches.

```elisp

(transient-define-prefix tsc-incompatible ()
  "Prefix demonstrating incompatible switches."
  ;; update your transient version if you experience #129 / #155
  :incompatible '(("--switch" "--value=")
                  ("--switch" "--toggle" "--flip")
                  ("--argument=" "--value=" "--special-arg="))

  ["Arguments"
   ("-s" "switch" "--switch")
   ("-t" "toggle" "--toggle")
   ("-f" "flip" "--flip")

   ("-a" "argument" "--argument=")
   ("v" "value" "--value=")
   ("C-a" "special arg" "--special-arg=")]

  ["Show Args"
   ("s" "show arguments" tsc-suffix-print-args)])

;; (tsc-incompatible)

```


### TODO Short Args

**This section is incomplete. Maybe Magit contains better answers.**

Sometimes the<code>:shortarg</code>in a CLI doesn't exactly match the<code>:key:</code>and<code>:argument</code> so it can be specified manually.

The<code>:shortarg</code>concept could be used to help use man-pages or only for [transient-detect-key-conflicts](https://magit.vc/manual/transient.html#index-transient_002ddetect_002dkey_002dconflicts) but it's not clear what behavior it changes.

Shortarg cannot be used for exclusion excluding other options (prefix<code>:incompatible</code> or setting default values (prefix<code>:value</code>.


### Choices from a function

See<code>transient-infix-read</code>for actual code. This method uses the prefix's history and then delecates to<code>completing-read</code>or<code>completing-read-multiple</code> The<code>:choices</code>key coresponds to the<CODE>COLLECTION</CODE>argument passed to completing reads.

**Note**, using a function for completions can appear to require a daunting amount of behavior if you read the manual [section on programmed completions](info:elisp#Programmed Completion). If you however just return a list of options, even when FLAG is not t, everything seems just fine.

```elisp

(defun tsc--animal-choices (_complete-me _predicate flag)
  "Programmed completion for animal choice.
_COMPLETE-ME: whatever the user has typed so far
_PREDICATE: function you should use to filter candidates (only nil seen so far)
FLAG: request for metadata (which can be disrespected)"

  ;; if you want to respect metadata requests, here's what the form might
  ;; look like, but no behavior was observed.
  (if (eq flag 'metadata)
      '(metadata . '((annotation-function . (lambda (c) "an annotation"))))

    ;; when not handling a metadata request from completions, use some
    ;; logic to generate the choices, possibly based on input or some time
    ;; / context sensitive process.  FLAG will be `t' when these are
    ;; reqeusted.
    (if (eq 0 (random 2))
        '("fox" "kitten" "otter")
      '("ant" "peregrine" "zebra"))))

(transient-define-prefix tsc-choices-with-completions ()
  "Prefix with completions for choices."
  ["Arguments"
   ("-a" "Animal" "--animal="
    :always-read t ; don't allow unsetting, just read a new value
    :choices tsc--animal-choices)]
  ["Show Args"
   ("s" "show arguments" tsc-suffix-print-args)])

;; (tsc-choices-with-completions)

```


### TODO multiple instances

Switches and arguments that can be used multiple times are supported. Example needs to be written. This is useful for CLI wrapping or perhaps situations where a command accepts multiple levels of the same setting.


<a id="org924124d"></a>

## Dispatching args into a process

If you want to call a command line application using the arguments, you might need to do a bit of work processing the arguments. The following example uses cowsay.

-   Cowsay doesn't actually have a<code>message</code>argument, So we end up stripping it from the arguments and re-assembling something<code>call-process</code>can use.

-   Cowsay supports more options, but for the sake of keeping this example small (and to refocus effort on transient itself), the set of all CLI options are not fully supported.

There's some errata about this example:

-   The predicates don't update the transient.<code>(transient--redisplay)</code>doesn't do the trick. We could use<code>transient--do-replace</code>and<code>transient-setup</code> but that would lose existing state unless we run<code>transient-set</code>

-   The predicate needs to be exists & not empty (but doesn't matter yet)

✨ If you are working on a CLI tool in order to fit a transient interface, consider a JSON-RPC process because you can build a normal command interface and dispatch it with transient even if you skip the CLI argument handling facilities. CLI's are more fragile than JSON-RPC, and JSON-RPC processes can retain state.

```elisp

(defun tsc--quit-cowsay ()
  "Kill the cowsay buffer and exit."
  (interactive)
  (kill-buffer "*cowsay*"))

(defun tsc--cowsay-buffer-exists-p ()
  "Visibility predicate."
  (not (equal (get-buffer "*cowsay*") nil)))

(transient-define-suffix tsc--cowsay-clear-buffer (&optional buffer)
  "Delete the *cowsay* buffer.  Optional BUFFER name."
  :transient 'transient--do-call
  :if 'tsc--cowsay-buffer-exists-p
  (interactive) ; todo look at "b" interactive code

  (save-excursion
    (let ((buffer (or buffer "*cowsay*")))
      (set-buffer buffer)
      (delete-region 1 (+ 1 (buffer-size))))))

(transient-define-suffix tsc--cowsay (&optional args)
  "Run cowsay."
  (interactive (list (transient-args transient-current-command)))
  (let* ((buffer "*cowsay*")
         ;; TODO ugly
         (cowmsg (if args (transient-arg-value "--message=" args) nil))
         (cowmsg (if cowmsg (list cowmsg) nil))
         (args (if args
                   (seq-filter
                    (lambda (s) (not (string-prefix-p "--message=" s))) args)
                 nil))
         (args (if args
                   (if cowmsg
                       (append args cowmsg)
                     args)
                 cowmsg)))

    (when (tsc--cowsay-buffer-exists-p)
      (tsc--cowsay-clear-buffer))
    (apply #'call-process "cowsay" nil buffer nil args)
    (switch-to-buffer buffer)))

(transient-define-prefix tsc-cowsay ()
  "Say things with animals!"
  ;; only one kind of eyes is meaningful at a time
  :incompatible '(("-b" "-g" "-p" "-s" "-t" "-w" "-y"))

  ["Message"
   ("m" "message" "--message=" :always-read t)]
  ;; always-read, so clear by entering empty string
  [["Built-in Eyes"
    ("b" "borg" "-b")
    ("g" "greedy" "-g")
    ("p" "paranoid" "-p")
    ("s" "stoned" "-s")
    ("t" "tired" "-t")
    ("w" "wired" "-w")
    ("y" "youthful" "-y")]
   ["Actions"
    ("c" "cowsay" tsc--cowsay :transient transient--do-call)
    ""
    ("d" "delete buffer" tsc--cowsay-clear-buffer)
    ("q" "quit" tsc--quit-cowsay)]])

;; (tsc-cowsay)

```


### TODO Cleanup Cowsay

Clean up cowsay example. Check for binary before attempting to run it.


<a id="orgbfa280a"></a>

# Controlling Visibility

At times, you need a prefix to show or hide certain options depending on the context.


<a id="orgcad3129"></a>

## Visibility Predicates

Simple [predicates](https://magit.vc/manual/transient/Predicate-Slots.html#Predicate-Slots) at the group or element level exist to hide parts of the transient when they wouldn't be useful at all in the situation.

```elisp

(defvar tsc-busy nil "Are we busy?")

(defun tsc--busy-p () "Are we busy?" tsc-busy)

(transient-define-suffix tsc--toggle-busy ()
  "Toggle busy."
  (interactive)
  (setf tsc-busy (not tsc-busy))
  (message (propertize (format "busy: %s" tsc-busy)
                       'face 'success)))

```

Open the following example in buffers with different modes (or change modes manually) to see the different effects of the mode predicates.

```elisp

(transient-define-prefix tsc-visibility-predicates ()
  "Prefix with visibility predicates.
Try opening this prefix in buffers with modes deriving from different
abstract major modes."
  ["Empty Groups Not Displayed"
   ;; in org mode for example, this group doesn't appear.
   ("we" "wave elisp" tsc-suffix-wave :if-mode emacs-lisp-mode)
   ("wc" "wave in C" tsc-suffix-wave :if-mode cc-mode)]

  ["Lists of Modes"
   ("wm" "wave multiply" tsc-suffix-wave :if-mode (dired-mode gnus-mode))]

  [["Function Predicates"
    ;; note, after toggling, the transient needs to be re-displayed for the
    ;; predicate to take effect
    ("tb" "toggle busy" tsc--toggle-busy :transient t)
    ("bw" "wave busily" tsc-suffix-wave :if tsc--busy-p)]

   ["Programming Actions"
    :if-derived prog-mode
    ("pw" "wave programishly" tsc-suffix-wave)
    ("pe" "wave in elisp" tsc-suffix-wave :if emacs-lisp-mode)]
   ["Special Mode Actions"
    :if-derived special-mode
    ("sw" "wave specially" tsc-suffix-wave)
    ("sd" "wave dired" tsc-suffix-wave :if-mode dired-mode)]
   ["Text Mode Actions"
    :if-derived text-mode
    ("tw" "wave textually" tsc-suffix-wave)
    ("to" "wave org-modeishly" tsc-suffix-wave :if-mode org-mode)]])

;; (tsc-visibility-predicates)

```


<a id="orgd7662b4"></a>

## Inapt (Temporarily Inappropriate)

"Greyed out" suffixes. Inapt is better if an option is temporarily unavailable but the user should still know it exists.

✨ Adjust this example to use<code>:if</code>instead of<code>:inapt-if</code>to see the difference between visibility and inapt.

```elisp

(defun tsc--switch-on-p ()
  (transient-arg-value
   "--switch"
   (transient-args transient-current-command)))

(transient-define-prefix tsc-inapt ()
  "Prefix that configures child with inapt predicates."
  :refresh-suffixes t ; important for updating inapt! (1)
  ["Options"
   ("-s" "switch" "--switch"
    ;; we want to see the most recent value in `transient-args' (2)
    :transient transient--do-call)]

  ["Appropriate Suffixes"
   ("s" "switched" tsc--wave-switchedly
    :transient t
    :inapt-if-not tsc--switch-on-p)
   ("u" "unswitched" tsc--wave-unswitchedly
    :transient t
    :inapt-if tsc--switch-on-p)]

  ["Appropiate Group"
   :inapt-if-not tsc--switch-on-p
   ("q" "query" tsc--wave-inquisitively)
   ("w" "write" tsc--wave-writingly)])

;; (tsc-inapt)

```

1.  **Note**, like visibility predicates,<code>inapt-</code>predicates do not **by default** take effect until the transient has its layout fully redone. You must set the<code>:refresh-suffixes</code>slot on the **prefix**.

2.  The use of<code>transient--do-call</code>has a nuance. Normally, changing an infix does not update the most recent value seen when calling<code>transient-args</code> However, when the infix uses<code>transient--do-call</code>as it's pre-command, the value seen will be updated. This allows<code>tsc--switch-on-p</code>to see the updated value and for the inapt predicates to function properly.


### Errata

Calling<code>transient-args</code>with the symbol of the current command <code>tsc-inapt</code> in<code>tsc--switch-on-p</code>causes infinite recursion as<code>transient-args</code>attempts to hydrate the transient instead of re-using the value that already is in motion via<code>transient-current-command</code>


<a id="org6ebf275"></a>

## Levels

Levels are another way to control visibility.

-   As a developer, you set levels to optionally expose or hide children in a prefix.
-   As a user, you change the prefix's level and the levels of suffixes to customize what's visible in the transient.

**Lower levels are more visible. Setting the level higher reveals more suffixes.** 1-7 are valid levels.

The user can adjust levels within a transient prefix by using (**C-x l**) for<code>transient-set-level</code> The default active level is 4, stored in<code>transient-default-level</code> The default level for children is 1, stored in<code>transient--default-child-level</code>

Per-suffix and per-group, the user can set the level at which the child will be visible. Each prefix has an active level, remembered per prefix. If the child level is less-than-or-equal to the child level, the child is visible.

A hidden group will hide a suffix even if that suffix is at a low enough level. Issue #153 has some additional information about behavior that might get cleaned up.


### Defining group & suffix levels

Adding default levels for children is as simple as adding integers at the beginning of each list or vector. If some commands are not likely to be used, instead of making the hard choice to include them or not, you can provide them, but tell the user in your README to set higher levels.

```elisp

(transient-define-prefix tsc-levels-and-visibility ()
  "Prefix with visibility levels for hiding rarely used commands."

  [["Setting the Current Level"
    ;; this binding is normally not displayed.  The value of
    ;; `transient-show-common-commands' controls this by default.
    ("C-x l" "set level" transient-set-level)
    ("s" "show level" tsc-suffix-show-level)]

   [2 "Per Group" ;; 1 is the default default-child-level
      ("ws" "wave surely" tsc--wave-surely)
      (3"wn" "wave normally" tsc--wave-normally)
      (5"wb" "wave non-essentially" tsc--wave-non-essentially)]

   [3 "Per Group Somewhat Useful"
      ("wd" "wave definitely" tsc--wave-definitely)]

   [6 "Groups hide visible children"
      (1 "wh" "wave hidden" tsc--wave-hidden)]

   [5 "Per Group Rarely Useful"
      ("we" "wave eventually" tsc--wave-eventually)]])

;; (tsc-levels-and-visibility)

```


### Using the Levels UI

Press (**C-x l**) to open the levels UI for the user. Press (**C-x l**) again to change the active level. Press a key such as "we" to change the level for a child. After you cancel level editing with (**C-g**), you will see that children have either become visible or invisible depending on the changes you made.

**While a child may be visible according to its own level, if it's hidden within the group, the user's level-setting UI for the prefix will contradict what's actually visible. The UI does not allow setting group levels.**


<a id="org999e41d"></a>

# Advanced

The previous sections are designed to go breadth-first so that you can get core ideas first. The following examples expand on combinations of several ideas or subclassing & customizing rarely used slots.

Some of these examples are approaching the complexity of just reading [magit source](elisp:(find-library "magit")).


<a id="org6c78b1e"></a>

## Dynamically generating layouts

While you can cover many cases using predicates, layouts, and visibility, **sometimes you really do want to generate a list of commands.**

**Note**, beware that you could be creating a lot of suffix objects if the forms you use generate unique symbols. These will pollute command completions over time, so probably don't do that.

[transient-setup-children](https://magit.vc/manual/transient.html#index-transient_002dsetup_002dchildren)

This is a group method that can be overridden in order to modify or eliminate some children from display. If you need a central place for children to coordinate some behavior, this may work for you.

```elisp

(transient-define-prefix tsc-generated-child ()
  "Prefix that uses `setup-children' to generate single child."

  ["Replace this child"
   ;; Let's override the group's method
   :setup-children
   (lambda (_) ; we don't care about the stupid suffix

     ;; remember to return a list
     (list (transient-parse-suffix
            'transient--prefix
            '("r" "replacement" (lambda ()
                                  (interactive)
                                  (message "okay!"))))))

   ;; This child will not be visible when you run the example because it is
   ;; replaced dynamically when the transient is set up
   ("s" "haha stupid suffix" (lambda ()
                               (interactive)
                               (message "You should replace me!")))])

;; (tsc-generated-child)

```

<code>transient--parse-child</code>takes the same configuration format as<code>transient-define-prefix</code> You can see the layout format in the [layout hacking appendix](#org7dc18d1).<code>transient--prarse-group</code>works almost exactly the same, just for groups.

The same thing, but parsing an entire group spec:

```elisp

(transient-define-prefix tsc-generated-group ()
  "Prefix that uses `setup-children' to generate a group."

  ["Replace this child"
   ;; Let's override the group's method
   :setup-children
   (lambda (_) ; we don't care about the stupid suffix

     ;; the result of parsing here will be a group
     (transient-parse-suffixes
      'transient--prefix
      ["Group Name" ("r" "replacement" (lambda ()
                                         (interactive)
                                         (message "okay!")))]))

   ;; This child will not be visible when you run the example because it is
   ;; replaced dynamically when the transient is set up
   ("s" "haha stupid suffix" (lambda ()
                               (interactive)
                               (message "You should replace me!")))])

;; (tsc-generated-group)

```

If you need to define a dynamic group class, override<code>transient-setup-children</code> It will work almost entirely the same as the examples above. Set your group class in the prefix using the<code>:class</code>key.

**Note** you don't need to be inside of a layout body to hack around with dynamic layouts. Mess around in [ielm](elisp:(ielm))).

```elisp

(transient--parse-child 'magit-dispatch '("a" "action" (lambda () (interactive) (message "hey"))))

```

**Note** you can replace<code>transient--prefix</code>with<code>tsc-generated-group</code>in the example above.<code>transient--prefix</code>is just a variable that happens to hold the prefix during layout.


### TODO Question

-   These functions do mostly the same job. Why do we need to specify a prefix for<code>transient-parse-suffixes</code> for scope etc?


<a id="orgefd72c2"></a>

## Modifying layouts

In this example, we will make a transient that can add new commands to itself.

```elisp

(defun tsc--self-modifying-add-command (command-symbol sequence)
  (interactive "CSelect a command: \nMkey sequence: ")

  ;; Generate an infix that will call the command and add it to the
  ;; second group (index 1 at the 0th position)
  (transient-insert-suffix
    'tsc-self-modifying
    '(0 1 0) ; set the child in `tsc-inception' for help with this argument
    (list sequence (format "Call %s" command-symbol) command-symbol :transient t))

  ;; we must re-enter the transient to force the layout update
  (transient-setup 'tsc-self-modifying))

(transient-define-prefix tsc-self-modifying ()
  "Prefix that uses `transient-insert-suffix' to add commands to itself."

  [["Add New Commands"
    ("a" "add command" tsc--self-modifying-add-command)]
   ["User Defined"
    ""]]) ; blank line suffix creates an insertion point

;; (tsc-self-modifying)

```

Exercises for the reader:

-   Use the interactive form to read an elisp expression and create an anonymous command
-   Add a command to remove suffixes
-   Create a suffix editor interface, modifying the description, key, command, or other slots

See the<code>transient-insert-suffix</code>for documentation on the<CODE>LOC</CODE>argument.


<a id="org3d54cdc"></a>

## Using prefix scope in children

Basically you are on your own.<code>transient-scope</code>may work. Just call<code>(oref transient--prefix scope)</code>during layout setup or<code>(oref transient-current-prefix scope)</code>during suffix bodies.


### Obtaining Missing Scope

Because suffixes are basically also commands (riding in the same symbol plist), a suffix can be called independently. In this case, if its expecting to read the scope from the prefix when there is no prefix, it might fail.

Therefore, a method called<code>transient-init-scope</code>can be overridden and used at the correct point in the life-cycle for the suffix to correct the issue.

**Note**, the behavior is actually quite ad-hoc. You will read the prefix yourself and then decide if you want to use some fallback.

There is a perfectly short example in [Magit source](https://github.com/magit/magit/blob/40fb3d20026139ad1c3a3d9069b40d7d61bf8786/lisp/magit-transient.el#L56-L61) for the custom<code>magit--git-variable</code>subclass of the<code>transient-variable</code>infix.

Each infix instance is declared in<code>transient-define-infix</code> potentially with a<code>:scope</code>slot, possibly holding a function.

If it's holding a function, that function will be used as a backup during initialization in case there is no prefix or it has nothing in its<code>:scope</code>slot.


<a id="org47828e0"></a>

## Custom Infix Types

Not everything is a string or boolean. You may want to represent complex objects in your transient infixes. If your objects can be re-hydrated from some serialized ID, you may want history support.

If you need to set and display a custom type, use the simple OOP techniques of [EIEIO](#org8dcf55c). Also check the [suffix value methods](info:transient#Suffix Value Methods) section of the transient manual. The following example applies these ideas.

**Essential behaviors for your custom infix:**

-   Defining a reader to set the infix with user input
-   <code>:prompt</code>slot's default form,<code>:initform</code>for asking the user for input
-   <code>transient-init-value</code>to re-hydrate saved values
-   <code>transient-infix-value</code>so that setting & saving persist what you want to rehydrate
-   <code>transient-format-value</code>to display a user-meaningful form for your value

We will also use some layout introspection. This makes the example a bit more complex, but represents a real custom infix type with real serialization and elisp objects backing it:

-   <code>transient-get-suffix</code>To get suffix by **key**, **location**, or **command symbol**
-   Getting a description from raw layout children (not EIEIO objects). See [Layout Hacking](#org7dc18d1).

This example is a bit intimidating because the serialized value we are storing and re-hydrating is a layout child location, the LOC argument seen in transient programming. It maps to an actual layout child, which we introspect and later modify. The point of the example is to let the user handle a simple value that we can also persist but to use a more complex object that might only exist at runtime. If this example makes little sense, try making an example with just a string or number before you start your own data type.

```elisp

;; The children we will be picking can be of several forms.  The
;; transient--layout symbol property of a prefix is a vector of vectors,
;; lists, and strings.  It's not the actual eieio types or we would use
;; `transient-format-description' to just ask them for the descriptions.
(defun tsc--layout-child-desc (layout-child)
  "Get the description from LAYOUT-CHILD.
LAYOUT-CHILD is a transient layout vector or list."
  (let ((description
         (cond
          ((vectorp layout-child)
           (or (plist-get (aref layout-child 2) :description)
               "<group, no desc>")) ; group
          ((stringp layout-child) layout-child) ; plain-text child
          ((listp layout-child)
           (plist-get (elt layout-child 2) :description)) ; suffix
          (t
           (message
            (propertize "You traversed into a child's list elements!"
                        'face 'warning))
             (format "(child's interior) element: %s" layout-child)))))
    (cond
     ;; The description is sometimes a callable function with no arguments,
     ;; so let's call it in that case.  Note, the description may be
     ;; designed for one point in the transient's lifecycle but we could
     ;; call it in a different one, causing its behavior to change.
     ((functionp description) (apply description))
     (t description))))

;; We repeat the read using a lisp expression from `read-from-minibuffer' to
;; get the LOC key for `transient-get-suffix' until we get a valid result.
;; This ensures we don't store an invalid LOC.
(defun tsc-child-infix--reader (prompt initial-input history)
  "Read a location and check that it exists within the current transient.
PROMPT, INITIAL-INPUT, and HISTORY are forwarded to `read-from-minibuffer'."
  (let ((command (oref transient--prefix command))
        (success nil))
    (while (not success)
      (let* ((loc (read (read-from-minibuffer
                         prompt initial-input nil nil history)))
             (child (ignore-errors (transient-get-suffix command loc))))
        (if child (setq success loc)
          (message (propertize
                    (format
                     "Location could not be found in prefix %s"
                     command)
                    'face 'error))
          (sit-for 3))))
    success))

;; Inherit from variable abstract class
(defclass tsc-child-infix (transient-variable)
  ((value-object :initarg value-object :initform nil)
   ;; this is a new slot for storing the hydrated value.  we re-use the
   ;; value infrastructure for storing the serialization-friendly value,
   ;; which is basically a suffix addres or id.

   (reader :initform #'tsc-child-infix--reader)
   (prompt :initform
           "Location, a key \"c\",\ suffix-command-symbol like\ tsc--wave-normally or coordinates like (0 2 0): ")))

;; We have to define this on non-abstract infix classes.  See
;; `transient-init-value' in transient source.  The method on
;; `transient-argument' class was used to make this example, but it
;; does support a lot of behaviors.  In short, the prefix has a value
;; and you rehydrate the infix by looking into the prefix's value to
;; find the suffix value.  Because our stored value is basically a
;; serialization, we rehydrate it to be sure it's a valid value.
;; Remember to handle values you can't rehydrate.
(cl-defmethod transient-init-value ((obj tsc-child-infix))
  "Set the `value' and `value-object' slots using the prefix's value."

  ;; in the prefix declaration, the initial description is a reliable key
  (let ((variable (oref obj description)))
    (oset obj variable variable)

    ;; rehydrate the value if the prefix has one for this infix
    (when-let* ((prefix-value (oref transient--prefix value))
                ;; (argument (and (slot-boundp obj 'argument)
                ;;   (oref obj argument)))
                (value (cdr (assoc variable prefix-value)))
                ;; rehydrate
                (value-object (transient-get-suffix
                               (oref transient--prefix command) value)))
      (oset obj value value)
      (oset obj value-object value-object))))

(cl-defmethod transient-infix-set ((obj tsc-child-infix) value)
  "Update `value' slot to VALUE.
Update `value-object' slot to the value corresponding to VALUE."
  (let* ((command (oref transient--prefix command))
         (child (ignore-errors (transient-get-suffix command value))))
    (oset obj value-object child)
    (oset obj value (if child value nil)))) ; TODO a bit ugly

;; If you are making a suffix that needs history, you need to define
;; this method.  The example here almost identical to the method
;; defined for `transient-option',
(cl-defmethod transient-infix-value ((obj tsc-child-infix))
  "Return our actual value for rehydration later."

  ;; Note, returning a cons for the value is very flexible and will
  ;; work with homoiconicity in persistence.
  (cons (oref obj variable) (oref obj value)))

;; Show user's a useful representation of your ugly value
(cl-defmethod transient-format-value ((obj tsc-child-infix))
  "All transient children have some description we can display.
Show either the child's description or a default if no child is selected."
  (if-let* ((value (and (slot-boundp obj 'value) (oref obj value)))
            (value-object (and (slot-boundp obj 'value-object)
                               (oref obj value-object))))
      (propertize
       (format "(%s)" (tsc--layout-child-desc value-object))
       'face 'transient-value)
    (propertize "¯\_(ツ)_/¯" 'face 'transient-inactive-value)))

;; Now that we have our class defined, we can create an infix the usual
;; way, just specifying our class
(transient-define-infix tsc--inception-child-infix ()
  :class tsc-child-infix)

;; All set!  This transient just tests our or new toy.
(transient-define-prefix tsc-inception ()
  "Prefix that picks a suffix from its own layout."

  [["Pick a suffix"
    ("-s" "just a switch" "--switch") ; makes history value structure apparent
    ("c" "child" tsc--inception-child-infix)]

   ["Some suffixes"
    ("s" "wave surely" tsc--wave-surely)
    ("d" "wave definitely" tsc--wave-definitely)
    ("e" "wave eventually" tsc--wave-eventually)
    ("C" "call & exit normally" tsc--wave-normally :transient nil)]

   ["Read variables"
    ("r" "read args" tsc-suffix-print-args )]])

;; (tsc-inception)
;;
;; Try setting the infix to "e" (yes, include quotes)
;; Try: (1 2)
;; Try: tsc--wave-normally
;;
;; Observe that the LOC you enter is displayed using the description at that
;; point
;;
;; Set the infix and re-open it with C-x s, C-g, and M-x tsc-inception
;; Observe that the set value persists across invocations
;;
;; Save the infix, with C-x C-s, re-evaluate the prefix, and open the
;; prefix again.
;;
;; Try flipping through history, C-x n, C-x p
;; Now do think of doing things like this with org ids, magit-sections,
;; buffers etc.

```

This is a difficult example, but once you understand the pieces, you can see some of the magit variables in action like<code>magit--git-variable</code>and its many subclasses.

Revisit the section on [detangling setting, saving and history](#org80724d9). Watching the values update will make it clear what representations are being stored, where, and when.


### Reading custom infix values

**Note**, however you store and rehydrate will affect how you read, so try to make it just work with<code>transient-read-arg</code> unlike this example (TODO).

```elisp

(transient-define-suffix tsc--inception-update-description ()
  "Update the description of of the selected child."
  (interactive)
  (let* ((args (transient-args transient-current-command))
         (description (transient-arg-value "--description=" args))
         ;; This is the part where we read the other infix.  It's
         ;; similar to how we find the value during rehydration, but
         ;; hard-coding the infix's argument, "child", which is used
         ;; in its `transient-infix-value' method.
         (loc (cdr (assoc "child" args)))
         (layout-child (transient-get-suffix 'tsc-inception-update loc)))

    ;; Once again, do different bodies based on what we found at the
    ;; layout locition.  This complexity is beacuse of the data we
    ;; are operating on, not the transient methods we needed to
    ;; implement.
    (cond
     ((or (listp layout-child) ; child
          (vectorp layout-child) ; group
          (stringp layout-child)) ; string child
      (if (stringp layout-child) ; plain-text child
          (transient-replace-suffix 'tsc-inception-update loc description)

        (plist-put (elt layout-child 2) :description description)))
     (t (message
         (propertize
          (format "Don't know how to modify whatever is at: %s" loc)
                     'face 'warning))))

    ;; re-enter the transient manually to display the modified layout
    (transient-setup transient-current-command)))

(transient-define-prefix tsc-inception-update ()
  "Prefix that picks and updates its own suffix."

  [["Pick a suffix"
    ("c" "child" tsc--inception-child-infix :argument "child")]

   ["Update the description!"
    ("-d" "description" "--description=")
    ("u" "update" tsc--inception-update-description
     :transient transient--do-exit)]

   ["Some suffixes"
    ("s" "wave surely" tsc--wave-surely)
    ("d" "wave definitely" tsc--wave-definitely)
    ("e" "wave eventually" tsc--wave-eventually)
    ("C" "call & exit normally" tsc--wave-normally :transient nil)]

   ["Read variables"
    ("r" "read args" tsc-suffix-print-args )]])

;; (tsc-inception-update)
;;
;; 1. Press 'c' to start picking a suffix.  For example, enter the string "e"
;; 2. Press 'C-x s' to set the values of this transient for the future
;; 3. Then set the description, anything, no quotes
;; 4. Then press 'u' the suffix's you picked with the new description!
;;
;; Using a transient to modify a transient (⊃｡•́‿•̀｡)⊃━✿✿✿✿✿✿
;;
;; Observe that the set values are persisted across invocations.
;; Saving also works.  This makes it easier to set the description
;; multiple times in succession.  The Payoff when building larger
;; applications like magit rapidly adds up.

```


### TODO Errata

Modifying the very outer group doesn't quite work. It's probably a degenerate layout object, meaning setting a description doesn't cause it to behave like a group with a heading. Maybe outer groups have a different data structure? **An exercise left to the reader**

The flow control for re-display is slightly fighting the history implementation. It would be better if we could retain values while triggering a redraw without even more hacking & state manipulation.


<a id="org3499af9"></a>

# Appendixes


<a id="org8dcf55c"></a>

## EIEIO - OOP in Elisp

Emacs lisp ships with eieio, a close cousin to the Common Lisp Object System. It's OOP. There are classes & subclasses. You can inherit into new classes and override methods to customize behaviors.

You can use eieio API's to explore transient objects. Let's look at some transients you have already:

```elisp

;; The plist for a prefix command contains a `transient-prefix' object in the
;; `transient--prefix' key and a vector layout in `transient--layout'
;; symbol-plist
(symbol-plist 'magit-dispatch)

;; getting the values from the symbol plist
(get 'magit-dispatch 'transient--prefix)

;; equivalent but longer
;; (plist-get (symbol-plist 'magit-dispatch) 'transient--prefix)

(let ((prefix-object
       (get 'magit-dispatch) 'transient--prefix))

  ;; printing the current slot values for that object
  (object-write prefix-object)

  ;; ;; Object transient-prefix-20997da
  ;; (transient-prefix "transient-prefix-20997da"
  ;;   :command magit-dispatch  :info-manual "(magit)Top")

  ;; getting the class of an object
  (eieio-object-class prefix-object) ; transient-prefix

  ;; opening the help documents for the class, which shows all methods and
  ;; slot forms
  (describe-symbol transient-prefix))

```


### Typical OOP

Like all OOP, the three things you want to do are:

-   Override methods

    <code>cl-defmethod</code>and sometimes<code>cl-call-next-method</code>

-   Override default values

    Inside the<code>defclass</code>form, you can set slots that you don't like.<code>:initform</code>is a default value.<code>:initarg</code>configures which argument to pick up from the class constructor.

-   Read & Update

    <code>oref</code>and<code>oset</code>

-   Call Methods

    <code>(method-name object arguments)</code>

-   Introspection

    See methods like<code>slot-boundp</code>in the EIEIO [eieio method index](info:eieio#Function Index)


### Transient's defclasses and their inheritance

Here's a list of all of transient's<code>defclass</code>and their ancestry. This is how it is in 2022.

```elisp

(eieio-browse) ; shows all known classes and their ancestry

;;     +--transient-child
;;     |    +--transient-group
;;     |    |    +--transient-subgroups
;;     |    |    +--transient-columns
;;     |    |    +--transient-row
;;     |    |    +--transient-column
;;     |    +--transient-suffix
;;     |         +--magit--git-submodule-suffix
;;     |         +--transient-infix
;;     |              +--transient-variable
;;     |              |    +--magit--git-variable
;;     |              |    |    +--magit--git-branch:upstream
;;     |              |    |    +--magit--git-variable:urls
;;     |              |    |    +--magit--git-variable:choices
;;     |              |    |         +--magit--git-variable:boolean
;;     |              |    +--transient-lisp-variable
;;     |              +--transient-argument
;;     |                   +--transient-switches
;;     |                   +--transient-option
;;     |                   |    +--transient-files
;;     |                   +--transient-switch
;;     +--transient-prefix
;;          +--magit-log-prefix
;;          |    +--magit-log-refresh-prefix
;;          +--magit-diff-prefix
;;               +--magit-diff-refresh-prefix

```


### View Class Methods and Attributes

Using<code>describe-symbol</code>is extremely handy for viewing the class slots and methods.

Classes used in transient that you are likely to want to know the slots for:

[transient-prefix](elisp:(describe-symbol 'transient-prefix)) [transient-suffix](elisp:(describe-symbol 'transient-suffix)) [transient-infix](elisp:(describe-symbol 'transient-infix)) [transient-argument](elisp:(describe-symbol 'transient-argument)) [transient-variable](elisp:(describe-symbol 'transient-variable))

[The eieio docs](https://www.gnu.org/software/emacs/manual/html_mono/eieio.html#Inheritance) have a more wordy treatment. The class system has a lot of behavior that can be faster at times to just understand through description.


<a id="org871fa38"></a>

## Debugging

There is a lot of support for both print-line and step-through debugging.


### Print debug messages

Just set<code>transient--debug</code>to t. [(setq transient-debug t)](elisp:(setq transient--debug t))

You will get a lot of logs visible in<code>*Messages*</code>via [view-echo-message-area](elisp:(view-echo-message-area)) the next time you run a transient.

```text

-- setup              (cmd: tsc-layout-rows-explicit, event: "M-x", exit: nil)
-- stack-zap          (cmd: tsc-layout-rows-explicit, event: "M-x", exit: nil)
-- init-transient     (cmd: tsc-layout-rows-explicit, event: "M-x", exit: nil)
push transient--transient-map
push transient--redisplay-map
-- post-command       (cmd: tsc-layout-rows-explicit, event: "M-x", exit: nil)
-- pre-command        (cmd: transient-update, event: "w", exit: nil)
pop  transient--redisplay-map
-- post-command       (cmd: transient-update, event: "w", exit: nil)
pop  transient--redisplay-map
push transient--redisplay-map
-- pre-command        (cmd: tsc-suffix-wave, event: "w l", exit: nil)
-- stack-zap          (cmd: tsc-suffix-wave, event: "w l", exit: nil)
-- pre-exit           (cmd: tsc-suffix-wave, event: "w l", exit: t)
pop  transient--transient-map
pop  transient--redisplay-map
Waves at the user at: Sat Nov 12 22:38:20 2022.
-- post-command       (cmd: tsc-suffix-wave, event: "w l", exit: t)
-- post-exit          (cmd: tsc-suffix-wave, event: "w l", exit: t)

```


### Watching evaluation in Edebug

**Edebug works with transients. There is much support in transient to facilitate using edebug.**

For watching the flow control around your command, especially helpful for debugging behavior around setup, layout, or suffix dispatch, you might want to watch your transient in Edebug.

[Edebug](https://www.youtube.com/watch?v=odkYXXYOxpo) basic introduction video (10 min).

In short:

-   goto your [transient source](elisp:(find-library "transient"))
-   instrument a function you want to watch with<code>edebug-defun</code>
-   call the transient / suffix that triggers entry of that function
-   use<CODE>SPC</CODE>to step forward,<code>c</code>to continue,<code>i</code>to enter a function call, or<code>h</code>for help etc

First watch the debug output to gain an idea of how your code flows with the transient code. Then instrument transient behaviors such as<code>transient--post-exit</code>and use<code>i</code>to<code>edebug-step-in</code>to calls of interest.

When you are done, remember to use [`edebug-remove-instrumentation`](elisp:(edebug-remove-instrumentation)) so that you can go on without every transient you open trying to call the debugger.

-   Debugging Macro Forms

    Because edebug works on defuns while suffixes are defined with macros, you may need to macro exand in order to come up with something debuggable.


<a id="org7dc18d1"></a>

## Layout Hacking

First you need to export the layout data structures.

```elisp

;; Let's look at the layout
(let ((prefix-layout (plist-get
                      (symbol-plist 'magit-dispatch) 'transient--layout)))

  (type-of prefix-layout) ; cons

  (listp prefix-layout) ; t

  (length prefix-layout) ; 3

  ;; Each group in the list is a vector
  (vectorp (car prefix-layout)) ; t

  ;; the attributes are key-value pairs used to create the class
  ;; instance when the transient is shown.

  ;; the nested contents will be lists of vectors for groups and
  ;; lists of lists for suffixes.

  (elt (car prefix-layout) 0) ; first element is a priority
  (elt (car prefix-layout) 1) ; second is a type name
  (elt (car prefix-layout) 2)) ; contents & attributes

;; A sample layout

;; ([1 transient-column nil
;;     ((1 transient-suffix
;;         (:key "i" :description "Ignore" :command magit-gitignore))
;;      (1 transient-suffix
;;         (:key "I" :description "Init" :command magit-init))
;;      (1 transient-suffix
;;         (:key "j" :description "Jump to section"
;;          :command magit-status-jump :if-mode magit-status-mode))
;;      (1 transient-suffix
;;         (:key "j" :description "Display status"
;;          :command magit-status-quick
;;          :if-not-mode magit-status-mode)))])

```

You might find this helpful when constructing [dynamic layouts](#org6c78b1e).


<a id="org11eebbf"></a>

## Hooks

Just a reminder, some hooks exist. Use<code>describe-variable</code>and complete with<code>transient hook</code>for the most recent list of hooks.


<a id="orgff38980"></a>

## Preludes

Definitions that are not that interesting on their own but are used in examples.


### `tsc-suffix-wave` Command

```elisp

(defun tsc-suffix-wave ()
  "Wave at the user."
  (interactive)
  (message "Waves at the user at: %s." (current-time-string)))

```


### `tsc-suffix-show-level`

```elisp

(transient-define-suffix tsc-suffix-show-level ()
  "Show the current transient's level."
  :transient t
  (interactive)
  (message "Current level: %s" (oref transient-current-prefix level)))

```


### `tsc--define-waver`

```elisp

;; Because command names are used to store and lookup child levels, we have
;; define a macro to generate unqiquely named wavers.  See #153 at
;; https://github.com/magit/transient/issues/153
(defmacro tsc--define-waver (name)
  "Define a new suffix with NAME tsc--wave-NAME."
  `(transient-define-suffix ,(intern (format "tsc--wave-%s" name)) ()
     ,(format "Wave at the user %s" name)
     :transient t
     (interactive)
     (message (format "Waves %s at %s" ,name (current-time-string)))))

;; Each form results in a unique suffix definition.
(tsc--define-waver "surely")
(tsc--define-waver "normally")
(tsc--define-waver "non-essentially")
(tsc--define-waver "definitely")
(tsc--define-waver "eventually")
(tsc--define-waver "hidden")
(tsc--define-waver "inquisitively")
(tsc--define-waver "writingly")
(tsc--define-waver "switchedly")
(tsc--define-waver "unswitchedly")

```


### `tsc-suffix-print-args`

Here's a suffix that reads the transient's infix values, the prefix's scope, and any universal argument <code>C-u 4</code>etc).

```elisp

(transient-define-suffix tsc-suffix-print-args (the-prefix-arg)
  "Report the PREFIX-ARG, prefix's scope, and infix values."
  :transient 'transient--do-call
  (interactive "P")
  (let ((args (transient-args (oref transient-current-prefix command)))
        (scope (transient-scope)))
    (message "prefix-arg: %S \nprefix's scope value: %S \ntransient-args: %S"
             the-prefix-arg scope args)))

;; tsc-suffix-print-args command is incidentally created

```


<a id="org208c2cb"></a>

## Essential Elisp

If you were hit in the face with the first example, you need to learn basic Elisp. This is not an Elisp guide. Here's some starting points:

-   [transient-define-prefix](info:elisp#Macros) is a macro that creates a command and attaches a<code>transient-prefix</code>object to the command symbol's plist.
-   [lambda](info:elisp#Lambda) is a macro to create an anonymous function
-   [interactive](info:elisp#Using Interactive) is a macro that makes the function compatible with the command interface, the<code>M-x</code>or<code>execute-extended-command</code>menu
-   [The brackets](info:elisp#Vector Functions) are just vector syntax.

    Besides the other ways to evaluate elisp used in this README, try<code>ielm</code>

    Use the built-in elisp manual by calling the command<code>elisp-index-search</code> See shortdocs for functions using<code>shortdoc-display-groups</code>

    The EIEIO and CL manuals are independent from the Elisp manual for some reason. EIEIO is pretty short and not used much once you get the hang of it.<code>info-display-manual</code>

    [Common Lisp manual](info:cl#Top) you don't really need the common lisp manual for working with transient. Don't be alarmed when you see EIEIO using functions like<code>cl-call-next-method</code>


<a id="org8ebe2ac"></a>

# Further Reading

-   [**The Transient Manual**](info:transient#Top) ([web link](https://magit.vc/manual/transient.html)) contains more detailed explanation of behavior. The examples here should allow you to visualize what is being described. This guide and the manual should be your first and second sources.

-   [**Transient source**](elisp:(find-library "transient")) ([web link](https://github.com/magit/transient/blob/master/lisp/transient.el)) is all in one file. Source code is always more accurate than manual descriptions, even if some behavior implementations are a bit scattered.

-   [**Magit source**](elisp:(find-library "magit")) ([web link](https://github.com/magit/magit/search?q=transient)) contains numerous examples of transient being used in a big, full-feature application. Search the source for "transient" and you will find many prefixes, suffixes, and custom classes. The smallest examples may be harder to find and most combine many behaviors at once.

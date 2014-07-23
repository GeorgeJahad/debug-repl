# The Clojure debug-repl

Every time I stick a println into some Clojure code to debug it, I
think to myself, "This is Lisp! I should be able to insert a repl
here!"

The problem is of course that Clojure's eval function doesn't know
about the surrounding lexical scope.  How to solve the problem?
Create a macro that passes a copy of the lexical scope in with the
form to be evaled, something like this:

```clojure
(defn eval-with-locals
  [locals form]
    (eval
     `(let ~(generate-local-bindings locals)
        ~form)))
```

## INSTALLATION

Leiningen

```clojure
[debug-repl "0.3.2"]
```

Maven

```xml
<dependency>
  <groupId>debug-repl</groupId>
  <artifactId>debug-repl</artifactId>
  <version>0.3.2</version>
</dependency>
```

## USAGE

The interface is meant to be dead simple. To load and then invoke the code run:

```clojure
(use 'alex-and-georges.debug-repl)
(debug-repl)
```

That's about it.  When you enter the debug-repl, the regular
repl prompt will be replaced with 

    dr =>

An example will make it clearer:

```clojure
user=>   (let [c 1 d 2]
    (defn a [b c]
      (debug-repl)
      d))
#'user/a

user=>   (a "foo" "bar")

dr-1-1001 => c
"bar"

dr-1-1001 => d
2

dr-1-1001 => *locals*
{fn__104 #<user$eval__103 user$eval__103@5f6303>, c "bar", d 2, fn__106 #<user$eval__103$a__105 user$eval__103$a__105@179dce4>, b "foo", counter__56__auto__ 1001}

dr-1-1001 => (str b c)
"foobar"

dr-1-1001 => ()
2
user=> 
```

## LIMITATIONS

The debug-repl doesn't currently integrate properly
with the slime-repl, (I think because of how Slime manages IO
redirection,) so you'll have to invoke it from a regular repl, or
slime's *inferior lisp* buffer.

A version of the debug-repl has been ported to slime.  More details
here: http://hugoduncan.org/post/2010/swank_clojure_gets_a_break_with_the_local_environment.xhtml

https://en.wikipedia.org/wiki/User:Dnalot/PureScript

PureScript is a strict, purely functional, modular (no runtime dependency), programming language inspired by Haskell which compiles to readable JavaScript with a nominal foreign function interface.

## History

PureScript was created by Phil Freeman in 2013. _talk about making a language with Haskell type system features and ex. records, designed for compilation to JS_.

> > The initial goal was to create a small language which could be used to create domain specific languages, using type system features which were unavailable in many other AltJS languages (algebraic data types, extensible records).

> You'd did a much better job explaining on [JavaScript Jabber](#news). Could you adapt the transcript from there? You also make no mention of positive language influences which would provide valuable backlinks.

The PureScript compiler and its standard libraries are currently maintained by Phil Freeman and Gary Burgess.

> Historical note: first SlamData PS commit on May 9, 2014 (https://github.com/slamdata/slamdata-nodewebkit/commit/73e3c9d5710ebf21552699e6bbe87f412171aec8)

The PureScript community maintains a collection of standard libraries, which aim for compatibility with the compiler.

> You mentioned making this its own section. I can add it to Tools.

In 2015, PureScript took part in the Google Summer of Code program, resulting in two successful projects - a database of PureScript libraries and documentation with type-directed search, and the addition of an exhaustivity and redundancy analysis pass to the compiler.

[PureScript Conf 2015](https://github.com/purescript/purescript/wiki/PureScript-Conf-2015) was the first conference devoted to the PureScript language, with the [2016 edition](https://github.com/purescript/purescript/wiki/PureScript-Conf-2016) scheduled for May 2016. A [book](https://leanpub.com/purescript.md) on the language was released in 2015.

## Examples

### Hello World

```purescript
module Main where

import Control.Monad.Eff.Console (log)

main = log "Hello, World!"
```

> I'd prefer two examples (backed up in Gist for http://try.purescript.org):

> 1. Simple HTML output (using either DOM or Thermite instead of console)
> 2. Simple algorithm (like binary search, quick sort, or Fibonacci)

## Features

### Type System

#### Algebraic Data Types

PureScript features _algebraic data types_, using a syntax similar to that of Haskell:

```purescript
data List a = Nil | Cons a (List a)
```

#### Type Inference

PureScript types are _inferred_ by the compiler, based on the types of subexpressions. For example:

```purescript
map f Nil = Nil
map f (Cons x xs) = Cons (f x) (map f xs)
```

Here, the type of `f` does not need to be specified. PureScript will infer the correct type `forall a b. (a -> b) -> List a -> List b`.

The PureScript compiler provides warnings when type signatures are not provided on top-level declarations, encouraging users to provide types as a form of documentation.

#### Type Classes

PureScript supports type classes using a dictionary-passing transformation. 

For example, a type class for types with decidable equality is defined in the Prelude library:

```purescript
class Eq a where
  eq :: a -> a -> Bool
```

PureScript functions which are constrained by the `Eq` class are compiled to JavaScript functions, with an additional function argument which carries the `Eq` implementation as an object.

Type class instances are named in PureScript, using a double colon to separate an instance name from the instance head, as in a type declaration:

```purescript
instance eqList :: Eq a => Eq (List a) where
  eq Nil         Nil         = true
  eq (Cons x xs) (Cons y ys) = x == y && xs == ys
  eq _           _           = false
```

#### Extensible Records

PureScript represents records using Javascript objects at runtime, and implements _extensible records_ using row polymorphism.

For example, it is possible to write a PureScript function which extracts a `name` value from any record with a `name` property:

```purescript
getName rec = rec.name
```

PureScript will infer the following type for `getName`:

```purescript
getName :: forall r a. { name :: a | r } -> a
getName rec = rec.name
```

The type signature indicates that not only the type of the `name` field, but also the _remaining fields_ in the record, are left polymorphic.

Row polymorphism is also used to implement features such as record updates.

#### Extensible Effects

PureScript makes use of row polymorphism to implement its effect system. PureScript's core libraries define the `Eff` type constructor, which is used to represent effectful values. `Eff` is parameterized by a row of effect labels, and row polymorphism is used to combine computations which make use of different combinations of effects.

For example, the PureScript compiler is able to infer the most general type of the following computation which makes use of the `RANDOM` and `CONSOLE` effects, even if the type signature is omitted:

```purescript
printRandom :: forall eff. Eff ( random :: RANDOM
                               , console :: CONSOLE 
                               | eff
                               ) Unit
printRandom = do
  n <- random
  print n
```

Developers are able to define new effect types using the foreign function interface. It is possible to assert that a function only uses a specific set of effects by using a type signature.

### Generic Programming

PureScript supports generic programming using type classes and _generic deriving_. The `Generic` type class can be defined for data types using the `derive` keyword:

```purescript
newtype Person = Person
  { name :: String
  , location :: String
  }

derive instance genericPerson :: Generic Person
```

A `Generic` instance can be used to implement data-type generic functionality such as equality testing, comparison, pretty-printing, and JSON serialization and deserialization.

### Foreign Function Interface

PureScript is designed specifically with compilation to Javascript in mind, and its Javascript interoperability is cited as one of PureScript's design goals, and one of its strengths.

Javascript functions can be used from PureScript by assigning types in `foreign import` declarations:

```purescript
module FFI.Example where

foreign import shout :: String -> String
```

Every module has an optional companion CommonJS module which contains its foreign function implementations. For example, the function defined above might be defined in a CommonJS module as follows:

```javascript
// module FFI.Example

exports.shout = function(s) {
    return s + "!";
};
```

Conversely, PureScript functions and values can be used from Javascript, since PureScript compiles to CommonJS modules. Developers of new foreign types are expected to document the representation of those types in Javascript.

### Code Generation

The PureScript compiler generates CommonJS modules by default, but can also bundle JavaScript modules for use in the browser, including the removal of unused code.

## Tools

The PureScript compiler distribution includes tools for generating documentation in Markdown, Ctags and Etags formats, as well as a custom format which is used by the Pursuit package database.

Plugins are available for many popular text editors, including Emacs, Vim, Atom, and Visual Studio Code, with features such as syntax highlighting, autocompletion, jump to error, and smart replace. The psc-ide project defines a JSON protocol so that editors can query program metadata in a standard way.

### Pursuit

Mention Harry Garrood

### Pulp

Mention Bodil

## [Notable Sources](https://en.wikipedia.org/wiki/Wikipedia:Notability#General_notability_guideline)

### First-Party

- [Homepage](http://www.purescript.org)
- [Compiler repository](https://github.com/purescript/purescript)
- [Pursuit package search](http://pursuit.purescript.org)
- [eBook](https://leanpub.com/purescript)
- [Community Wiki](https://github.com/purescript/purescript/wiki)
- [Google Group](https://groups.google.com/group/purescript)

### [Popular Libraries](https://github.com/search?l=&o=desc&q=language%3APureScript&s=stars)

Descending by rating (>100 stars)

- [Halogen](https://github.com/slamdata/purescript-halogen)
- [Pulp](https://github.com/bodil/pulp)
- [React bindings](https://github.com/purescript-contrib/purescript-react)
- [Signal](https://github.com/bodil/purescript-signal)
- [Thermite](https://github.com/paf31/purescript-thermite)

---
Ascending by date:

### Academic

| Date | Individual | Group | Link |
|--:|---|---|---|
| Apr 28, 2015 | Harry Garrood | Google Summer of Code | [Pursuit enhancements](https://www.google-melange.com/gsoc/project/details/google/gsoc2015/hdgarrood/5717271485874176) |
| Apr 28, 2015 | Nicolas Del Piano | Google Summer of Code | [Exhaustiveness Checker for PureScript](https://www.google-melange.com/gsoc/project/details/google/gsoc2015/ndelpiano/5685265389584384) |
| Jun 12, 2015 | Michal Srb | Imperial College London | [Haskell-Like S-Expression-Based Language Designed for an IDE](http://www.doc.ic.ac.uk/teaching/distinguished-projects/2015/m.srb.pdf) |
| Aug 8, 2015 | Phil Freeman | self-published | [Stack Safety for Free](http://functorial.com/stack-safety-for-free/index.pdf) |

### News

| Date | Individual | Group | Link |
|--:|---|---|---|
| Sep 22, 2014 | Sergio De Simone | InfoQ | [PureScript: A Haskell-like Language that Compiles to JavaScript](http://www.infoq.com/news/2014/09/purescript-haskell-javascript) |
| Sep 15, 2015 | Steven Proctor | Functional Geekery | [Episode 26](https://www.functionalgeekery.com/episode-26-phil-freeman/) |
| Dec 9, 2015 | Joe Eames | JavaScript Jabber | [PureScript with John A. De Goes and Phil Freeman](https://devchat.tv/js-jabber/189-jsj-purescript-with-john-a-de-goes-and-phil-freeman) |

### Conference Videos

| Date | Individual | Group | Link |
|--:|---|---|---|
| Sep 21, 2014 | Bodil Stokke | Strange Loop | [PureScript (Maybe This Time We Get JavaScript Right)](https://youtu.be/yIlDBPiMb0o) |
| Jul 9, 2015 | Bodil Stokke | PolyConf | [Post FRP Frontend Programming](https://youtu.be/X5YBsy6PaDw) |
| Jul 20, 2015 | Boros Márton | Open Academy | [PureScript: the solution to the problem Javascript](https://youtu.be/WjlYD7HreAE) |
| Oct 8, 2015 | Vagmi Mudumbai | FnConf | [Pure functional programming in your browser & server with PureScript](https://youtu.be/fb44xcwHx0U) |
| Oct 17, 2015 | Michael Ficarra | Lambda Jam | [Getting Started With Purescript](https://youtu.be/OdenKPwSPss) |
| Oct 24, 2015 | Luis Vicente | Lambda World | [Purify your Lambdas](https://youtu.be/bsKEAF2FCME) |
| Nov 28, 2015 | Christoph Hegemann | f(by) | [PureScript – Fearless Programming for the Web](https://youtu.be/rigzJSI-FPA) |
| Dec 30, 2015 | John De Goes | LambdaConf | [Learn Functional Programming with PureScript](https://youtu.be/LqYfdmb0eUU) |
| Dec 30, 2015 | Phil Freeman | LambdaConf | [Purely Functional Web Apps using React and PureScript](https://youtu.be/qTYsxou0RE8) |

### Meetup Videos

Do not meet notability criteria:

| Date | Individual | Group | Link |
|--:|---|---|---|
| Jul 22, 2015 | Tim Docker | FP-Syd | [PureScript: An Overview for Haskellers](https://youtu.be/udg01o2M4iY) 
| Oct 22, 2015 | Taylor Fausak | OrgSync Dev Lunch & Learn | [Better know a language: PureScript](https://youtu.be/4bAYr90dkls) |
| Nov 19, 2015 | Michael Trotter | Utah Haskell Meetup | [Intro to PureScript](https://youtu.be/9a57V3bvzaI) |

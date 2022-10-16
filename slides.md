---
theme: seriph
canvasWidth: 1080
title: intro to io-ts
titleTemplate: intro to io-ts - %s
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## intro to io-ts
  Brief io-ts onboarding slides for developers.
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS
css: unocss
# provide downloadable pdf
download: true
layout: default
---

# Navigation

Hover on the bottom-left corner to see the navigation's controls panel, [learn more](https://sli.dev/guide/navigation.html)

### Keyboard Shortcuts

|     |     |
| --- | --- |
| <kbd>right</kbd> / <kbd>space</kbd>| next animation or slide |
| <kbd>left</kbd>  / <kbd>shift</kbd><kbd>space</kbd> | previous animation or slide |
| <kbd>up</kbd> | previous slide |
| <kbd>down</kbd> | next slide |

<!-- https://sli.dev/guide/animations.html#click-animations -->
<img
  v-click
  class="absolute -bottom-9 -left-7 w-80 opacity-50"
  src="https://sli.dev/assets/arrow-bottom-left.svg"
/>
<p v-after class="absolute bottom-23 left-45 opacity-30 transform -rotate-10">Here!</p>

---
layout: cover
# apply any windi css classes to the current slide
class: 'text-center'
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# background: https://source.unsplash.com/collection/94734566/1920x1080
background: https://images.unsplash.com/photo-1638184984605-af1f05249a56?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&h=1080
---

# Intro to io-ts

or: *How my trust issues manifest in code*

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/aejones0/io-ts-intro" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
layout: image-right
image: /img/programming.gif
---

# Before we begin

- 'dudes' and 'guys' have been my preferred collective pronouns (ie when addressing groups) for
  decades, and I'm  probably going to use them during this talk.
  - I don't intend any harm; my apologies if this habit will be uncomfortable for you.
- Who am I?
  - Arthur
  - Just some dude on the internet.
  - I solve other peoples problems, professionally.

---
layout: image-left
image: https://source.unsplash.com/collection/94734566/1920x1080
---

# what we're solving

- type safety
- runtime representation of static types
- shotgun parsing

<!--
todo: memes

or use the section layout
-->

---
layout: image-left
image: /img/as-any.jpeg
---

# What we’re solving: Type safety

```ts
// it’s probably fine
const foo = JSON.parse(scaryInput) as Optimism;
```

- successful `JSON.parse` results in a valid js object, but we don’t know anything else about that object
  - and thus its output type is `any`
- `any` is a highly infectious disease.
- casting an `any` isn’t solving the problem, it’s silencing the compiler.

---
layout: image-left
image: /img/everything_is_great.png
---

# What we’re solving: Runtime representation

```ts
type Optimism = {
  // …
};
```

<v-clicks>

- type systems are fantastic at compile time...
- …but eventually our software has to run, and we need our data to be correct at *runtime*
- we *should* be able to say that anything within our domain is valid
- to reach that point, we need to parse at the domain boundary
</v-clicks>

---
layout: image-left
image: /img/old yeller.gif
---

# What we’re solving: Shotgun parsing

> Shotgun parsing[^1] is a programming antipattern whereby parsing and input-validating code is mixed with and spread across processing code—throwing a cloud of checks at the input, and hoping, without any systematic justification, that one or another would catch all the “bad” cases.

Which
> ...deprives the program of the ability to reject invalid input instead of processing it. Late-discovered errors in an input stream will result in some portion of invalid input having been processed, with the consequence that program state is difficult to accurately predict.

[^1]: [The Seven Turrets of Babel: A Taxonomy of LangSec Errors and How to Expunge Them](http://langsec.org/papers/langsec-cwes-secdev2016.pdf)


---
layout: center
---

# Show me

```ts {1|3-7|9-10|12-13|12-14|14-20|22-23} {maxHeight:'30em'}
import * as t from 'io-ts';

// define a codec to use at runtime
const User = t.type({
  id: t.number,
  name: t.string,
})

// generate a type from that codec
type User = t.TypeOf<typeof User>

// attempt to decode an input
const obj = JSON.parse(scaryInput) // any
const parsed = User.decode(obj)    // Either<Errors, User>

if (isLeft(parsed)) {
  // handle bad data. scaryInput was not a valid User.
  // ...
  return;
}

// we know it's a valid User AND so does the compiler!
const user = parsed.right         // User
```

<style>
.shiki {
  min-width: fit-content;
}
</style>

<!--
todo: example error output
-->

---
layout: center
---

# End...?

<div class="flex justify-center align-center">
  <img
    v-click
    src="/img/understand.png"
    class="rounded-2xl h-md"
  />
</div>

---

# Foundational knowledge

- knowledge of typescript is assumed
- io-ts is part of the fp-ts ecosystem
- because its built on a functional programming foundation, it's useful for you to know at least a
  trivial amount about fp.
- bits we will cover
  - Either type
    - and its refinements
  - Validation type
  - Basic io-ts usage
- bits we won't cover
  - typeclasses, higher kinded types, and so much more

---
layout: section
---

# Foundational knowledge:
# Either type

<div class="flex justify-center">
  <img
    src="/img/fusion-either.gif"
    class="rounded-xl"
  />
</div>

---
layout: two-cols-top
---

::top::
# Foundational knowledge: Either type

- a common algebraic data type (ADT). It does cool stuff.
<v-clicks>

- to start with, think of it as a container
- disjoint union; it is *Either* a `Left` or a `Right`
  ```ts
  type Either<E, T> =
      | Left<E>
      | Right<T>
  ```
  - the ts implementation is specifically a discriminated union
</v-clicks>

<v-click>

by convention:
</v-click>

::left::
<v-click>

### Left
`Left` is used for error or failure states.

Contains a value in its `.left` prop
</v-click>

::right::
<v-click>

### Right
`Right` is used for valid or success states.

Otherwise known as the happy path.

Contains a value in its `.right` prop
</v-click>

---

# Foundational knowledge: Either type

Similar patterns in other modern languages

```rust {all|0}
// Rust has a wonderful type system that includes Result. It uses Ok and Err
// instead of Left and Right, but it's effectively the same thing.
fn foo() -> Result<String, io::Error> { ... }

let bar = foo();
// non-idiomatic, but relevant for this audience
if (bar.is_err()) {
  // ...
}
```

<br>
<v-click>

```go {all}
// Go uses multiple return values. Not a proper Either, but the usage pattern
// has some similarity. It's more of a Pair/Tuple than an Either.
func foo() (string, error) { ... }

msg, err := foo()
if err != nil {
  // ...
}
```
</v-click>

<!--
- While Go is not representative of the actual type, the usage pattern is closest to what actually
  appears in our repos.
- Unwrapping a context is not the norm in fp-like environments, but it's what you'll probably do in
  our codebase.
-->

---

# Foundational knowledge: Either type

- Either is type, not a class. It maintains a clean separation between data
  and behavior.
- fp-ts provides a collection of functions that operate on Either
- we'll only focus on a few of them, namely the `isLeft` and `isRight` refinements

---

# Foundational knowledge: Either type refinements

### isLeft

> Returns `true` if the Either is an instance of `Left`, `false` otherwise.

signature
```ts
export declare const isLeft: <E>(ma: Either<E, unknown>) => ma is Left<E>
```

<v-clicks>

- Using this type refinement lets the compiler (and readers) know that
  - this specific instance of `Either` is a `Left`
  - and has a `.left` property.

```ts
decoded.left; // Error, Property 'left' does not exist on type Either

if (isLeft(decoded)) {
  handleError(decoded.left); // Ok. Decoded is a Left, so it has a left prop
  return;
}
```
</v-clicks>

---

# Foundational knowledge: Either type refinements

### isRight

> Returns `true` if the Either is an instance of `Right`, `false` otherwise.

signature
```ts
export declare const isRight: <A>(ma: Either<unknown, A>) => ma is Right<A>`
```

<v-clicks>

- Using this type refinement lets the compiler (and readers) know that
  - this specific instance of `Either` is a `Right`
  - and has a `.right` property.

```ts
decoded.right; // Error, Property 'right' does not exist on type Either

if (isRight(decoded)) {
  doStuff(decoded.right); // Ok. Decoded is a Right, so it has a right prop
}
```
</v-clicks>

---
layout: section
---

# Foundational knowledge:
# Validation type

<div class="flex justify-center">
  <!-- <img -->
  <!--   src="/img/validation.png" -->
  <!--   class="rounded-xl" -->
  <!-- /> -->
</div>

---
layout: default
---

# Foundational knowledge: Validation type

name brand Eithers

`Validation` is an Either with a specific error type

```ts
type Validation<A> = Either<ValidationError[], A>
```

<v-click>

io-ts codecs will return a Validation

```ts {1|3-6|4|8}
const validation = User.decode(possibleUserData)

if (isLeft(validation)) {
  const errList = validation.left  // ValidationError[]
  // ...
}

const user = validation.right      // User
```
</v-click>

<!--
- attempt to decode what we hope is a user, and we get a Validation
- using our isLeft refinement:
  - within the block, we know we have a Left that contains a list of ValidationError
- outside the block, we have a Right that contains a User
-->

---
layout: two-cols-top
---

::top::
# Defining a codec

finally, parsing!

::left::
io-ts provides codecs for primitive types

```ts
const StringCodec = t.string
const NumberCodec = t.number
// etc...
```

::right::
<v-click>

and combinators that allow you to compose more complex codecs
```ts {1-6|8-16}
// plain old js object.
// type User = { id: number; name: string; }
const User = t.type({
  id: t.number,
  name: t.string,
})

// type Foo = User & { phone: string }
const Foo = t.intersection(
  [
    User,
    t.type({
      phone: t.string
    }),
  ]
)
```
</v-click>

<!--
- t.type is a combinator that takes an object
- that object has props whose values are codecs for the type of that prop
  - these don't have to be primitives, they can be complex type codecs
- there are an assortment of other combinators that allow one to describe anything that the type system is capable of representing
-->

---
layout: image-left
image: /img/fp_mays.png
---

# Defining a codec
but wait, there's more

We can also supply a name for our type.

This provides us with better human readable runtime errors when we leave the happy path.

```ts {all|6}
const User = t.type(
  {
    id: t.number,
    name: t.string,
  },
  'User',
);
```

---
layout: image-left
image: /img/stay_dry.jpg
---

# Extracting a type from a codec

stay DRY

Our codec definition also provides a static type (with a single additional line of boilerplate code).

```ts {6}
const User = t.type({
  id: t.number,
  name: t.string,
});

type User = t.TypeOf<typeof User>
```
<v-click>

If our type or parsing needs to change, we only need to update one location.

There's no need to update the types in `types.ts`, *then* hunt down every instance of validation and
update it as well.
</v-click>

<!--
- Note that you can use the same name for the type and the codec...unless your lint settings
  explicitly forbid that.

Image from one of my notebooks. I get bored.
-->

---
layout: default
---

# Error reporting
if you see something, say something

We can handle `ValidationErrors` in infinitely many ways, but a reasonable starting point is to use
the `PathReporter` included with io-ts.

```ts
import { PathReporter } from 'io-ts/lib/PathReporter'

const result = User.decode({ name: 'Giulio' })

console.log(PathReporter.report(result))
// => [ 'Invalid value undefined supplied to : { userId: number, name: string }/userId: number' ]
```

<v-click>

If we define a name in our codec this output becomes

```ts
// => [ 'Invalid value undefined supplied to : User/userId: number' ]
```

Not a huge difference with such a small type, but increasingly useful as type complexity increases.

</v-click>
<v-click>

It may be worth noting that `PathReporter` takes the entire `Validation`, not the `left` value.
</v-click>

<!--
- Imagine yourself as the consumer of these errors. Getting a type name and the exact prop that has an issue is great. You know exactly what the problem is, and it's easier to fix.
- PathReporter taking the entire validation is more of an issue when using fp tools
  (pipe/flow/fold). May not be so relevant in our procedural style code.
-->

---
layout: two-cols-top
---

::top::
# Further reading
rtfm or gtfo

::left::
## Directly applicable

- io-ts [index docs](https://gcanti.github.io/io-ts/modules/index.ts.html)
- fp-ts [Either docs](https://gcanti.github.io/fp-ts/modules/Either.ts.html)
- [fp-ts Discord](https://discord.gg/HVWmBBXM8A)
  - It's a great place to improve your knowledge by solving other people's problems, or
  to just get help with your own question.

::right::
## Supplementary

- [Parse, don't validate](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/)
- Railway Oriented Programming
  - [recorded talk](http://vimeo.com/97344498)
  - [slides etc](https://fsharpforfunandprofit.com/rop/)
- [Mostly adequate guide to FP](https://github.com/MostlyAdequate/mostly-adequate-guide)
- [Scala cats docs](https://typelevel.org/cats/index.html)
  - I'm not a mathlete; I find it much easier to view many fp concepts in terms of interfaces, and
    I've found the cats docs quite useful in that regard.

<!--
- The fp-ts docs aren't the most friendly thing ever
- They kind of take the approach that a hindly-milner is all you need, so it's all you get
-->

---

# Questions

tell me how I've failed to teach you without saying I've failed to teach you
<div class="flex justify-center align-center">
  <img
    src="/img/questions.gif"
    class="rounded-2xl h-md"
  />
</div>

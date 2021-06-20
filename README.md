# pandoc-for-dummies

Suggestion for beginner track excercise in Zurihac 2021 #pandoc channel.

Goal: Building single-script version of Pandoc for first-time learners of Haskell that 
converts `Hello, * beautiful world!*` form markdown to internal representation to HTML.

Announced here: <https://discord.com/channels/701454007775920128/855117502186913794>

Ideas:

- I would like to make a zero-setup  script that mimics how pandoc works.
- I think one needs to adopt a good mind-model of what the core of the pandoc program is before 
going into all of the project complexity. 
- The project was simplier at version 1.\* before 
ParserMonad was introduced at 2.\*, so as a first step we are moving "back to basics".)
- I assume the readers knows very basic Haskell and perahps still scared of monad type class.

## Links

> There is a brief introduction of Pandoc structure in here: https://github.com/jgm/pandoc/blob/758537ea719b6699b55e0fa73e7717c6b56fe766/doc/short-guide-to-pandocs-sources.md#document-representation

> jgm suggested to write a "howto" guide to certain parts of the sources. What do you think about that appoach? https://github.com/jgm/pandoc/pull/7316#issuecomment-848837733

## TOC:

- [ ] what part of pandoc code is responsible for reading source file? (impure)
- [ ] how does a reader work? (pure)
- [x] what is internal representation of document (pure)
- [ ] how does a writer work? (pure) `<-- point of work now` 
- [ ] how to write the output to a file? (impure)

## The excercise

### What is pandoc?

`pandoc` is a command-line utility that allows to convert document from one format to another,

Pandoc is one of Haskell most successful projects. It is originally written by John MacFarlane, 
a professor of philosophy, and I see it as a good example and encouragement for non-CS people 
to try dive into programming and Haskell in particular. 

### pandoc is modular!

Pandoc is extremely successul because it is modular - there is internal represntation of text, that "reader" modules can convert another format to, and "writer" modules can convert from. Different people can contribute to more readers and writers and enhancement of internal representation, which accelerated code developement.


### The heart of pandoc - the type definitions

A good entry point is into pandoc codebase is looking at internal representation of the document in pandoc. 

Have a look here: https://github.com/jgm/pandoc-types/blob/master/src/Text/Pandoc/Definition.hs

### Internal document representation

[This piece of documentation](https://github.com/jgm/pandoc/blob/758537ea719b6699b55e0fa73e7717c6b56fe766/doc/short-guide-to-pandocs-sources.md#document-representation) says: 

> A document has metadata and a list of "block" elements.

Question: Where do I find defintion of document in pandoc?

Answer: 

```haskell
data Pandoc = Pandoc Meta [Block]
              deriving (Eq, Ord, Read, Show, Typeable, Data, Generic)
```
<https://github.com/jgm/pandoc-types/blob/0158cd0e2a2ca9d6f14389a1a57bc64cab45a7dd/src/Text/Pandoc/Definition.hs#L103-L104>


So the internal representation of a document is just a data structure, 
that holds some document metadata (eg author, creation date) and a list of some things of `Block` type that we know little about yet. 

### What is a `Block`?

So what is Block, can you find it in <https://github.com/jgm/pandoc-types/blob/master/src/Text/Pandoc/Definition.hs>?

Answer: 

```haskell
-- | Block element.
data Block
    -- | Plain text, not a paragraph
    = Plain [Inline]
    -- | Paragraph
    | Para [Inline]
    -- | Multiple non-breaking lines

    -- Hint: check for more constructors in actual code
```

<https://github.com/jgm/pandoc-types/blob/0158cd0e2a2ca9d6f14389a1a57bc64cab45a7dd/src/Text/Pandoc/Definition.hs#L271-L281>

What is the reading of this? Block is data type that is associated with several constructors: `Plain`, `Para` and others. 
These constructors use a list of `Inline` type elements as in `Plain [Inline]` or `Para [Inline]`. Let's dig further and see 
what is `Inline`.

### What is an `Inline`?

It looks like the `Inline` is the root building block of document body representation in pandoc:

```haskell

import Data.Text (Text)     

data Inline
    = Str Text              -- ^ Text (string)
    | Emph [Inline]         -- ^ Emphasized text (list of inlines)
    | Underline [Inline]    -- ^  Underlined text (list of inlines)
    | Strong [Inline]       -- ^ Strongly emphasized text (list of inlines)
    | Space                 -- ^ Inter-word space
    | SoftBreak             -- ^ Soft line break
    | LineBreak             -- ^ Hard line break
```

from `import Data.Text` we imported `Text` type used to represent just a string containing text.
`Text` is somehow better that default `String` type, but I cannot tell you why. 

#### TODO: Text

Tell why is `Text` better than `String` built-in, and why we use `{-# LANGUAGE OverloadedStrings #-}`. 

#### ... section continued

A value of `Inline` type can be something of: 

```
Str "Hello!"             -- single word in plain text, no formatting
Space                    -- represents a space between words
Underline [Str "I", Space, Str "am", Space, Str "underlined" ] -- a list of elements underlined.
```

Excercise: represent the following string using a list of underlines 

> Hello, **beautiful world!**`

Answer:

```haskell
[Str "Hello", Space, Emph [Str "beautiful", Space, Str "world!"]]
```

What is the type of this expression? List of `Inline`, or `[Inline]`.

Remember what we can do with this type? Right - create a `Block`.
`block1` is a function that will return an element of Block type

It does look awfully like variable assignment, but it is a function in Haskell.

```haskell
block1 :: Block
block1 = Block [Str "Hello", Space, Emph [Str "beautiful", Space, Str "world!"]]
```


### Advanced questions

- Do Inline and Block types constrain us in document representation?

### Cross-cutting questions

- why is this baby example is different from real project?
- what is a hard part in this example?

### Need help: how does a writer work?

- What part of pandoc code responsible for converting `block1` to HTML?
- Where is it found in code?
- How to mimic pandoc behavior in simple fucntions?

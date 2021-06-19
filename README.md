# pandoc-for-dummies

Suggestion for beginner track example in Zurihac 2021 #pandoc channel.

The aim of this excercise is to celebrate minimal, trivial achievement
in learning progress of Haskell and study of the pandoc project.

I would like to make a zero-setup, single-script excercise 
with code that mimics how pandoc works that can run on repl.it

---

... (continues for chatroom, 18:26, 19.06)

Answer: 

```haskell
data Pandoc = Pandoc Meta [Block]
              deriving (Eq, Ord, Read, Show, Typeable, Data, Generic)
```

so the internal representation is just a data structure, that holds some 
document metadata (eg author, creation date) and a list of some thing `Block`
type. 

So what is Block, can you find it in <https://github.com/jgm/pandoc-types/blob/master/src/Text/Pandoc/Definition.hs>?

Next:

- what in Inline?
- advanced: does Inline and Block contrain us in document represetnation?

Probably will need help (will ask after I review https://github.com/jgm/pandoc/blob/758537ea719b6699b55e0fa73e7717c6b56fe766/doc/short-guide-to-pandocs-sources.md):

- what part of pandoc code is responsible for reading source file? (impure)
- how does a reader work? (pure)
- how does a writer work? (pure)
- how to write the output to a file? (impure)
- why is this baby example is different from real project?
- what is a hard part in this example?

...


Goal: Building single-script version of Pandoc for first-time learners of Haskell that 
converts `Hello, **this beautiful world!*` form markdown to internal representation to HTML.


Announced here: <https://discord.com/channels/701454007775920128/855117502186913794>

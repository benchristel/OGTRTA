# OGTRTA

The One Grammar... TO RULE THEM ALL!!!

...or OGTRTA, for short, is a meta-grammar for constructed languages. It's kind of like a grammar template that you can customize for your conlang.

It is designed to minimize syntactic ambiguity (think Lojban) without requiring agreement-marking, while remaining at least somewhat naturalistic. You can dial the syntactic precision up or down to your liking.

OGTRTA is a "reversible" grammar: all the production rules can be reversed to get a language with the opposite word order. The basic supported word orders are head-initial VOS and head-final SOV. OGTRTA languages can implement transformations can move the subject to the opposite end of the sentence, so SVO and OVS word orders are also possible.

The other two possible word orders, VSO and OSV, are not directly supported, but can be achieved using clitic subject pronouns or noun incorporation.

## Inspiration

The most interesting thing about OGTRTA is probably its treatment of verbs, prepositions, adjectives, and adverbs.
OGTRTA collapses all of these parts of speech into one, _predicates_. This design decision was inspired by a series of observations about English:

- Many verbs can be either transitive or intransitive. E.g. "The bird ate the rice" &rarr; "The bird ate".
- English prepositions can lose their object and function like adverbs. We can say "Is Josh in the house?" or "Is Josh in?"
- These two derivational processes (if indeed they are derivational) look awfully similar! A word that takes a complement can lose it.
- Participles function syntactically much like prepositions. Some pairs of prepositions and participles are nearly synonymous: "I broke the rock **with** a hammer" (preposition) vs. "I broke the rock **using** a hammer" (participle).
- Adjectives can take complements, much like verbs and prepositions. "The elf is eager" &rarr; "The elf is eager to please."
- Adjectives, prepositions, and verbs can all modify nouns (the latter via relative clauses) and can be the predicate of a sentence (though English requires a copula for non-verbs).

It certainly seems like there are similarities between these parts of speech! So why not unify them?

The Celtic languages also provided a great deal of inspiration. The reference implementation of OGTRTA uses initial consonant mutations, as well as tense-marking particles similar to those in Cornish.

## Description

This section presents a "reference implementation" of OGTRTA—a language designed according to OGTRTA conventions. To avoid confusion, the reference language is called English.

### Noun Phrases

A noun phrase describes a person, place, thing, or idea. The production rule for noun phrases in a head-initial OGTRTA language is:

```
NP = DET? N MP*
```

Where `DET` is a determiner, `N` is a lexical noun, and `MP*` is zero or more modifier phrases (described below).

Examples in English:

- **edhen** "bird"
- **am edhen** "the bird"
- **edhen velen** "yellow bird"
- **am edhen velen** "the yellow bird"
- **am edhen velen pig** "the little yellow bird"

The `DET?` part of the rule is optional; OGTRTA languages need not have determiners at all. I've chosen to include determiners in English to demonstrate more of the range of OGTRTA's capability, but I could just as easily have chosen to leave them out.

Even if you do have determiners in your language, that doesn't mean you have to use them for the same set of things that English does. Possessive pronouns, for example, can be either determiners or modifiers:

- **\*nin edhen** "my bird" (determiner) (the asterisk indicates a counterfactual, since English doesn't actually work this way)
- **am edhen nin** "my bird" (modifier)

Demonstratives, too, can be determiners or modifiers

- **\*hen edhen** "this bird" (determiner)
- **am edhen hen** "this bird" (modifier)

What about head-final languages? If your language is head-final, the production rule is reversed:

```
NP = MP* N DET?
```

This rule would generate:

- **\*edhen am** "the bird"
- **\*velen edhen** "yellow bird"
- **\*velen edhen am** "the yellow bird"
- **\*pig velen edhen am** "the little yellow bird"

Variants of the basic production rule are possible. Since the determiner is essentially a clitic on the noun, we can move it to the other side without ambiguity:

```
NP = N DET? MP*
```

We can also choose to allow "simple" MPs (e.g. adjectives with no complements or modifiers of their own, denoted below as `SMP`) between the noun and its determiner:

```
NP = (DET SMP*)? N MP*
```

Note that we cannot allow SMPs before the noun in the absence of a determiner without creating syntactic ambiguity, since in an actual sentence the NP might be preceded by another NP with modifiers of its own.
There are ways to mitigate this ambiguity, e.g. agreement, but such techniques are out of scope for this document.

## Modifier Phrases

We have already seen a few examples of modifier phrases above: the adjective-like words **pig** and **melen** (sic—the spelling **velen** arises from a consonant mutation). Technically, though, these are not adjectives, but _predicates_.

Predicates in OGTRTA play the roles that verbs, adjectives, and prepositions do in English. The following words are all predicates:

- **melen** "yellow"
- **math** "eating"
- **ga** "of"
- **gavo** "giving"

Some predicates take _complements_. Complements are noun phrases that follow the predicate (in head-first languages) and complete its meaning. Predicates must have all their complement "slots" filled. The number of complement slots a predicate has is called its _valence_.

Adjective-like predicates like **melen** generally don't take any complements, so they have a valence of 0. **Math** is a transitive-verb-like predicate and has a valence of 1. Preposition-like predicates like **ga** also have a valence of 1. **Gavo** is ditransitive, and has a valence of 2.

The production rule for an MP is:

```
MP = P_n MP* NP{n}
```

That is, an MP consists of a predicate (which has some valence `n`), zero or more modifier phrases, and `n` noun phrases to fill the complement slots.

Examples of MPs with complements:

- **math pelt** "eating rice"
- **ga am edhen** "of the bird"
- **gavo pelt am edhen** "giving rice to the bird"

Since the complements are noun phrases, they can contain MPs of their own. With just two production rules, we have a recursive grammar, capable of producing phrases of any length or complexity.

- **gavo pelt am edhen vowr math edhen big** "giving rice to the big bird eating a small bird"

The predicate of an MP may have modifiers of its own. These modifiers are essentially adverbs.

A common adverb is **mowr** (mutated form **vowr**) meaning "big, very, fully"

- **melen vowr** "very yellow"
- **math vowr** "devouring, eating up"
- **gavo vowr pelt (nebban)** "giving all the rice (to someone)"

### Modifier Attachment

In complex phrases, it can sometimes be hard to tell which word an MP is modifying. Consider the phrase **am edhen velen vowr**. Does this mean "the big yellow bird" or "the very yellow bird?" If **vowr** is modifying **edhen**, it means "the big yellow bird". If **vowr** is modifying **velen**, it means "the very yellow bird".

Actually, there is no ambiguity here, because English uses _consonant mutation_ to resolve it. That is, the initial consonant sound of a predicate changes when that predicate _immediately_ follows the word it is modifying. If any other word comes between the modifyee and the predicate, there is no mutation. Thus, we have:

- **am edhen velen vowr** "the very yellow bird"
- **am edhen velen mowr** "the big yellow bird"

I personally like this mechanism; I think it's elegant and fairly naturalistic. However, it doesn't completely resolve all ambiguity about modifier attachment. If there are more than two levels of nested phrases, there is no way to know if an unmutated modifier attaches to the word one level up or two (or more) levels up.

For example, in the sentence **am edhen vath edhen velen mowr**, is the big bird the one eating, or the one being eaten?

It is possible to resolve this ambiguity if we distinguish three kinds of MPs:

- Those that are _first_ among their modifyee's modifiers.
- Those that are _last_ among their modifyee's modifiers.
- Those that are in the middle, i.e. neither first nor last.

The consonant mutation rule in English distinguishes first modifiers from the others, but there is no distinction between last and "middle" modifiers. Let's imagine we used a prefix `ac-` on last modifiers. Then we could construct phrases like:

- **\*am edhen vath edhen ac-velen ac-mowr** "The big bird eating the yellow bird"
- **\*am edhen ac-vath edhen ac-velen ac-vowr** "The bird eating the very yellow bird"
- **\*am edhen ac-vath edhen velen ac-mowr** "The bird eating the big yellow bird"

While this is nice and logical, it has several practical weaknesses.

First, human speakers do not always know when they are about to utter the last modifier of a word. In general, it's a losing battle to try to force humans to parse or generate unambiguous nested structures of arbitrary depth. We just don't have the working memory for it.

Second, the resulting structure is difficult to parse in real time. It's a bit of a logic puzzle to deduce which modifiers attach to which modifyees.

Finally, some ambiguity about modifier attachment is normal in natural languages, and in many cases it is unclear even to the speaker which word a modifier should attach to. In the sentence _the suspect murdered the man in his bedroom_, does the prepositional phrase _in his bedroom_ attach to _the man_, or is it an adverbial modifier on _murdered_? Practically, it doesn't matter. The meaning is nearly the same in either case.

### Post-Complement Adverbial Phrases

The production rule for MPs described above requires us to put all modifiers of a predicate before its complements. This is awkward, because those modifiers might be lengthy prepositional phrases.

- **\*am edhen vath vowr na goweth pelt** "the bird devouring rice in the tree" (lit. "the bird eating up in the tree rice")

We can relax the rule, at the cost of some ambiguity, to:

```
MP = P_n MP* NP{n} MP*
```

That is, we can allow post-complement modifiers of the predicate.

The ambiguity results from the fact that the NP complements of the predicate might have modifiers of their own, and it might not be possible to tell where the sequence of modifiers for the last NP ends, and the modifiers for the MP begin.

However, if we assume that the post-complement modifiers are prepositional phrases, this is "good ambiguity". As discussed above, the attachment of prepositional phrases is usually not crucially important, so allowing the attachment to be ambiguous reduces the burden on the speaker without increasing the burden on the listener.
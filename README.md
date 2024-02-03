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

- **edhen am** "the bird"
- **velen edhen** "yellow bird"
- **velen edhen am** "the yellow bird"
- **pig velen edhen am** "the little yellow bird"

Variants of the basic production rule are possible. Since the determiner is essentially a clitic on the noun, we can move it to the other side without ambiguity:

```
NP = N DET? MP*
```

We can also choose to allow "simple" MPs (e.g. adjectives with no complements or modifiers of their own, denoted below as `SMP`) between the noun and its determiner:

```
NP = (DET SMP*)? N MP*
```

Note that we cannot allow SMPs before the noun in the absence of a determiner without creating syntactic ambiguity, since in an actual sentence the NP might be preceded by another NP with modifiers of its own.
There are ways to mitigate this ambiguity, e.g. agreement.

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



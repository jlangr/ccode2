# Continuous Design

Product owners (and others responsible for helping define a software product) determine the set of end goals that a system must accomplish for its users. Developers in turn build support for each of these goals in the system, in the form of code and configuration. As Jack W. Reeves taught us over three decades ago, this code (and configuration, but let's make it simpler by talk only about code here on out) is the ultimate and definitive representation of "the design." [REFERENCE here]

The word *design* has two primary meanings:

* The plan for building something.
* The form and functionality exuded by the current state of that something.

Software is made of code, but maybe it's more useful to say that software is made of thousands of small *behaviors*, each implemented in the form of code. These behaviors interact in a way that supports meeting the end goals of a system's users.

## Continuous Change

We can usually decompose end goals into a number of smaller behaviors, and sometimes in numerous ways. Given the goal "present a random list of flashcards for learning Czech nouns," we might implement the following sequence of behaviors:

* retrieve a random subset of all nouns
* for each noun:
  * present a question: a fill-in-the-blank sentence along with a multiple choice listing of the four possible noun forms
  * capture the user's choice
  * show the user the correct answer if their choice is incorrect
* repeat the prior three steps for each question that the user chose incorrectly

We might derive a number of alternate approaches. For example, the user might be able to continue selecting an answer for a single question until they get it correct; we might also present hints with each failed answer. We might also move from multiple choice to requiring a user to type in the actual noun form, for questions that the users gets correct.

If there are many design choices for an application itself, we have orders of magnitude more choices for implementing the software. No, that's not right. We have infinite choices. Most of those choices will be suboptimal.

The job of our product folks is to keep shaping the product to meet the perceived needs of its marketplace of users. Most software product continues to demand constant change in the form of new needs and variants on or enhancements to existing needs.

In turn, we are constantly reacting to such changes by shaping existing software. Code is reasonably unique: It must not only continuously present a design that demonstrates all current needs to date, but it must also accept that this design will need to be different tomorrow.

We might be able to build a useful flashcard system in a short period of time, perhaps a few months. Shortly after release (a beta hopefully), users will demand more features and a simpler user experience. We are in business, great! But we are also returning to the current system's design&mdash;its codebase&mdash;adding new features that maybe no one ever considered before.

## Continuous Design

When it comes to making choices in software, we have only a few kinds of primary building materials: modules (classes), functions (methods), data references (variables), and statements/expressions. The first three of these materials can be named, thus providing us humans quick means of understanding where and what things are in the codebase.

How we choose to manage and organize these building materials to support crafting behavioral concepts is software design. Put another way, our system's design is thus the collection of choices we have made to this point in time.

It is easily possible to make many bad choices when assembling these building materials, and in a very short matter of time to boot. Our flashcard app might still work, but at what cost to future change?

If our flashcard app's code is convoluted, poorly organized, with opaque names for its elements, accommodating new needs will be slow. We will expend too much time navigating the code to find where all the places it might need to be changed. Once we find those locations (plural), we will expend too much time understanding what the current code does. We will then expend too much time carefully inserting our changes, then ensuring that all the existing behaviors still work.

Each design choice we make impacts the cost for future choices. We succeed only if we consider the design impact of each choice. We refer to this critical concept as *continuous design*.

## Sailing on the Four C's of Clean Code

An Unclean Code© system makes it harder to:

* find code you're looking for
* understand where you need to make changes
* make changes without creating defects
* write tests to verify its behaviors

You might recognize these characteristics from the Class Classes chapter.

We have many design perspectives to help guide us toward a Clean Code system. As we change our system, we continue to reflect on one or more of these perspectives&mdash;SOLID, code smells, simple design, design patterns, and others, including various one-off principles such as the law of Demeter or Tell-Don't-Ask.

We'll try, as many before, to codify it all in a simple, hopefully memorable set of rules. Consider this YADP&mdash;Yet Another Design Perspective.

Our four design considerations are:

* clarity: The programmer's intents in the system are all clearly stated.
* conciseness (concision?): The programmer's intents in the system are all implemented in a minimal amount of code.
* cohesiveness: Each module in the system has a high level of cohesion&mdash;all its elements maximally relate to one another.
* confirmability: All behaviors in the system can be easily tested, in a way that also provides "living documentation" of these capabilities to its developers.

### Clarity

Within a function, we (generally) can't name our statements or expressions, though we can certainly group them in a manner that allows the function itself to describe its intent.

Software is unlike most other human-crafted products, in that its design can continue to change

## Speculative Design

These bigger goals (e.g., "show me a random list of flashcards for ) 

Our software itself&mdash;the code and other specifics of a system's implementation. 

programming is fundamentally a design activity and that the only final and true representation of “the design” is the source code itself

namely that programming is fundamentally a design activity and that the only final and true representation of “the design” is the source code itselfnamely that programming is fundamentally a design activity and that the only final and true representation of “the design” is the source code itself



We're building *czecher*, a flashcard application designed to help us ingrain challenging aspects of the Czech language. As a first step, we've created the module `words.js` to allow us to add new nouns to our collection of words to practice:

```
import { retrieveWord } from '../prompts/languageClient.js'

const definitions = {}

export const clearWords = () =>
  Object.keys(definitions).forEach(key => delete definitions[key])

export const loadDefinition = (word, definition) =>
  definitions[word] = { ...definition, word }

export const addWord = async word => {
  if (definitions[word]) return

  const definition = await retrieveWord(word)
  definitions[word] = { ...definition, word }
}

export const definition = word => definitions[word]

export const allDefinitions = () =>
  Object.values(definitions)
```

The `addWord` function takes on an English word as an argument (e.g. "dog"). If a definition has already been loaded for the word, the function does nothing. Otherwise, `addword` calls `retrieveWord` to look up information using a 3rd party resource (OpenAI in this case). The resulting definition is added to a local store. Calling the `definition` function with a word returns the detailed information.

Some tests might add a little color:

```
import { when } from 'jest-when'
import { addWord, clearWords, definition, allDefinitions, loadDefinition } from './words'
import { retrieveWord } from '../prompts/languageClient'

jest.mock('../prompts/languageClient')

describe('addWord', ()  => {
  const orangeDefinition = {
    word: 'orange',
    gender: 'MI',
    singular: { nominative: 'pomeranč', accusative: 'pomeranč' },
    plural: { nominative: 'pomeranče', accusative: 'pomeranče' }
  }
  const dogDefinition = {
    word: 'dog',
    gender: 'MA',
    singular: { nominative: 'pes', accusative: 'psa' },
    plural: { nominative: 'psi', accusative: 'psy' }
  }

  beforeEach(() => {
    clearWords()
    jest.resetAllMocks()
  })

  it('retrieves and saves definition on add', async () => {
    when(retrieveWord).calledWith('orange')
      .mockResolvedValueOnce(orangeDefinition)

    await addWord('orange')

    expect(definition('orange')).toEqual(orangeDefinition)
  })

  it('does nothing if already added', async () => {
    loadDefinition('orange', orangeDefinition)

    const word = definition('orange')

    expect(word).toEqual(orangeDefinition)
    expect(retrieveWord).not.toHaveBeenCalled()
  })

  const add = async (word, definition) => {
    when(retrieveWord).calledWith(word).mockResolvedValueOnce(definition)
    await addWord(word)
  }

  it('persists multiple words', async () => {
    await add('orange', orangeDefinition)
    await add('dog', dogDefinition)

    const definitions = allDefinitions()

    expect(definitions).toEqual([orangeDefinition, dogDefinition])
  })
})
```

The `words` module is our first incremental step toward an MVP. As a quick measure, we chose to persist the words in an in-memory JavaScript object (`{}`), where the keys are the words and the values provide the language detail we need. The constant `orangeDefinition` provides one example.

What's our continuous design report card look like? We get the following grades:

* Clarity: pass. All the operations appear in short, JavaScript-idiomatic methods, without any C-for-Cleverness (for which we would immediately get a failing grade).
* Confirmability: pass. All code was driven into existence by behavioral tests.
* Conciseness: pass. The code seems about as short as it could get.
* Cohesion: fail. What??

Our intent is to replace the JavaScript object with a proper persistence mechanism. Unfortunately, we've mired our little bit of definition-management logic with how we intend to store the definitions. When we do change, we'll have to open up the `words` module and poke through most of its methods to update implementations.

You will likely recognize that we're describing a single-responsibility principle (SRP) violation. The both say the same thing, minus some nuances. Cohesion is how well the elements of a module align to the same purpose.

Had we adhered to a cohesive solution, we might have found it a bit easier to build the solution in the first place. We'd also have been ready to immediately accommodate changes, rather than have to pre-factor our code to support the change.

We'll refactor the code now, to demonstrate how a cohesive solution increases the clarity of the code.

```
import { retrieveWord } from '../prompts/languageClient.js'
import * as Data from '../persistence/database.js'

export const clearWords = () =>
  Data.deleteAll()

export const loadDefinition = (word, definition) =>
  Data.add(word, definition)

export const addWord = async word => {
  if (Data.containsKey(word)) return

  const definition = await retrieveWord(word)
  Data.add(word, { word, ...definition})
}

export const definition = word => Data.get(word)

export const allDefinitions = () => Data.allValues()
```

The implementation specifics around JavaScript objects have been replaced with abstractions: `add`, `get`, `allValues`, `containsKey`, and `deleteAll`. The underlying implementation could change from an object, to a list, to interaction with a key-value store. The now-cohesive `words` module wouldn't be impacted by these changes.

The module `database.js` provides an abstract interface for persisting data, with nothing exported that betrays the underlying data structure. Future changes to its implementation don't impact other code in the system.

```
const data = {}

export const containsKey = key => data[key]

export const deleteAll = () =>
  Object.keys(data).forEach(key => delete data[key])

export const add = (key, value) =>
  data[key] = value

export const get = word => data[word]

export const allValues = () => Object.values(data)
```

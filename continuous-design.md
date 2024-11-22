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

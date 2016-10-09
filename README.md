# spacy-nlp
Expose Spacy nlp text parsing to Nodejs (and other languages) via socketIO

## Installation

```shell
# install spacy in python3
python3 -m pip install -U socketIO-client
python3 -m pip install -U spacy
python3 -m spacy.en.download

# install this npm package
npm i --save spacy-nlp
```

## Usage

```js
const spacyNLP = require('spacy-nlp')
# default port 6466
# start the server with the python client that exposes spacyIO (or use an existing socketIO server at IOPORT)
var serverPromise = spacyNLP.server({ port: process.env.IOPORT })
# Loading spacy may take up to 15s
```

*Note that `python3` is preferred. If you use `python2`, at each run set the env var `USE_PY2=true`.*

You'll see log like:

```shell
[Sun Oct 09 2016 16:53:33 GMT-0400 (EDT)] INFO Starting poly-socketio server on port: 6466, expecting 1 IO clients
[Sun Oct 09 2016 16:53:33 GMT-0400 (EDT)] INFO Starting socketIO client for python3 at 6466
[Sun Oct 09 2016 16:53:44 GMT-0400 (EDT)] DEBUG cgkb-py mXjDqupv852zUeMPAAAA joined, 0 remains
[Sun Oct 09 2016 16:53:44 GMT-0400 (EDT)] INFO All 1 IO clients have joined
```

Once it is ready, i.e. you can use the nodejs client `nlp` to parse texts:

```js
const spacyNLP = require('spacy-nlp')
const nlp = spacyNLP.nlp

// Note you can pass multiple sentences concat in one string.
nlp.parse('Bob Brought the pizza to Alice.')
  .then((output) => {
    console.log(output)
    console.log(JSON.stringify(output[0].parse_tree, null, 2))
  })
```

And the output is the syntax parse tree with POS tagging. For the `parse_tree`, `NE` means `Named Entity` for NER; `arc` of an object is incident on it. An arc points from `head` word to `modifier` word. See the explanation on [Tensorflow/syntaxnet](https://github.com/tensorflow/models/tree/master/syntaxnet#dependency-parsing-transition-based-parsing).

```JSON
[ { text: 'Bob Brought the pizza to Alice.',
    len: 7,
    tokens: [ 'Bob', 'Brought', 'the', 'pizza', 'to', 'Alice', '.' ],
    noun_phrases: [ 'Bob', 'the pizza', 'Alice' ],
    parse_tree: [ [Object] ],
    parse_list: 
     [ [Object],
       [Object],
       [Object],
       [Object],
       [Object],
       [Object],
       [Object] ] } ]
[
  {
    "word": "Brought",
    "lemma": "bring",
    "NE": "",
    "POS_fine": "VBD",
    "POS_coarse": "VERB",
    "arc": "ROOT",
    "modifiers": [
      {
        "word": "Bob",
        "lemma": "Bob",
        "NE": "PERSON",
        "POS_fine": "NNP",
        "POS_coarse": "PROPN",
        "arc": "nsubj",
        "modifiers": []
      },
      {
        "word": "pizza",
        "lemma": "pizza",
        "NE": "",
        "POS_fine": "NN",
        "POS_coarse": "NOUN",
        "arc": "dobj",
        "modifiers": [
          {
            "word": "the",
            "lemma": "the",
            "NE": "",
            "POS_fine": "DT",
            "POS_coarse": "DET",
            "arc": "det",
            "modifiers": []
          }
        ]
      },
      {
        "word": "to",
        "lemma": "to",
        "NE": "",
        "POS_fine": "IN",
        "POS_coarse": "ADP",
        "arc": "prep",
        "modifiers": [
          {
            "word": "Alice",
            "lemma": "Alice",
            "NE": "PERSON",
            "POS_fine": "NNP",
            "POS_coarse": "PROPN",
            "arc": "pobj",
            "modifiers": []
          }
        ]
      },
      {
        "word": ".",
        "lemma": ".",
        "NE": "",
        "POS_fine": ".",
        "POS_coarse": "PUNCT",
        "arc": "punct",
        "modifiers": []
      }
    ]
  }
]
```
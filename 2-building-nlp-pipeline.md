# Building an NLP Pipeline

## 1. NLP and Pipelines
- NLP is growing fast
- how to design end-to-end pipeline?
	1. start with raw text in any form
	2. process text
	3. extract relevant features
	4. build models to accomplish NLP tasks
- learn how stages interact with and depend on each other
- learn libraries and tools for each step

## 2. How NLP Pipelines Work
- common NLP pipeline in three stages:
	1. text processing
		- clean it
		- normalize it
		- convert it into something ready for feature extraction
	2. feature extraction
		- extract and produce feature representations
		- appropriate for chosen model
		- appropriate for chosen task
	3. modeling
- each stage produces result for next
- not always linear: may need to recode/rethink earlier stages as develop later ones

## 3. Text Processing
- why process text?
	- consider where we get text (often website)
	- take some raw HTML input from a Wikipedia page
	- usually strip out all or most tags
	- set aside URLs
- formats
	- may consume PDFs, documents, other files
	- may take input from a book scan
	- may take input from audio speech
- goal
	- extract plain text
	- keep text free of source-specific markers irrelevant to task
- further processing
	- if capitalization will not matter, samecase words
	- if punctuation will not matter, remove it
	- if small words will not matter, remove them

## 4. Counting Words
- counting frequencies commonly done in NLP
- implement `count_words()` to obtain and print the 10 most and least common unique words
- Starter code:
```Python
def count_words(text):
	"""Count how many times each unique word occurs in text."""
	counts = dict()  # dictionary of { <word>: <count> } pairs to return
	
	# TODO: Convert to lowercase
	
	# TODO: Split text into tokens (words), leaving out punctuation
	# (Hint: Use regex to split on non-alphanumeric characters)

	# TODO: Aggregate word counts using a dictionary  
	return counts
```
- My answer (popup prints PASS but message also says Try Again):
```
import re

def count_words(text):
	"""Count how many times each unique word occurs in text."""
	counts = dict()  # dictionary of { <word>: <count> } pairs to return
	
	# TODO: Convert to lowercase
	text = text.lower()
	
	# TODO: Split text into tokens (words), leaving out punctuation
	# (Hint: Use regex to split on non-alphanumeric characters)
	words = re.compile(r"[^a-z0-9]+").split(text)
	print(words)
	
	# TODO: Aggregate word counts using a dictionary
	for word in words:
		if not re.match(r"[a-z]+", word):
			continue
		if word in counts:
			counts[word] += 1
		else:
			counts[word] = 1
	
	print(counts)
	return counts
```

## 5. Feature Extraction
- can't quite feed this to a ML model
	- every character is encoded
	- computers transmit them in binary
	- characters have implicit ordering that could mislead our models (A 65 < B 66 < C 67)
	- besides, we're concerned with words, not characters
- consider how images are stored
	- each pixel has data about colors
	- each color is an r, g, b value stored directly
	- what's something similar we can do with text data?
		- depends on the model
		- depends on the task
	- WordNet visualizes using words as nodes and relations as connections between them
- statistical models will need some numerical representation
	- think about the end goal
	- document-level tasks
		- examples: spam detection, sentiment analysis
		- "bag of words" or "doc to vec" gives document-level representation
	- individual word-level tasks
		- examples: text generation, machine translation
		- "word to vec" or "glove"
	- practice to learn what you need for every kind of problem

## 6. Modeling
1. designing a model
	- statistical model
	- ML model
2. fitting parameters to training data
	- optimization procedure
3. making predictions about unseen data
- numerical features allow using any ML model
	- support vector machines
	- decision trees
	- neural nets
	- combined models
	- custom models
- ways of using the model
	- deploy to web
	- mobile app
	- integrate with products/services

## 7. Quiz: Split Sentences
- read text file
- split text into sentences
- split sentences into words
- use `re` to split
	- remember `r""` for raw input strings (so that Python just reads the raw text)
	- remove any leading/trailing spaces
- drop empty strings from list
- don't use `nltk` here but you can it in program projects
- Starter code:
```Python
"""Splitting text data into tokens."""

import re

def sent_tokenize(text):
	"""Split text into sentences."""
	
	# TODO: Split text by sentence delimiters (remove delimiters)
	
	# TODO: Remove leading and trailing spaces from each sentence
	
	pass  # TODO: Return a list of sentences (remove blank strings)


def word_tokenize(sent):
	"""Split a sentence into words."""
	
	# TODO: Split sent by word delimiters (remove delimiters)
	
	# TODO: Remove leading and trailing spaces from each word
	
	pass  # TODO: Return a list of words (remove blank strings)
```
- My answer:
```Python
"""Splitting text data into tokens."""

import re

def sent_tokenize(text):
	"""Split text into sentences."""
	
	# TODO: Split text by sentence delimiters (remove delimiters)
	sentences = re.compile(r"[\.\?\!]").split(text)
	
	# TODO: Remove leading and trailing spaces from each sentence
	sentences_formatted = []
	for sent in sentences:
		if sent == "":
			continue
		sentences_formatted.append(sent.strip())

	return sentences_formatted # TODO: Return a list of sentences (remove blank strings)


def word_tokenize(sent):
	"""Split a sentence into words."""
	
	# TODO: Split sent by word delimiters (remove delimiters)
	words = re.compile(r"[^A-Za-z0-9]+").split(sent)
	
	# TODO: Remove leading and trailing spaces from each word
	words_formatted = []
	for word in words:
		if word == "":
			continue
		words_formatted.append(word.strip())
	
	return words_formatted  # TODO: Return a list of words (remove blank strings)
```

## 8. Part-of-Speech Tagging
- how are words being used in a sentence?
	- understanding meaning
	- relationships between words
	- cross references
- NLTK has a `pos_tag` function
	- returns a tag for each word
	- understands how to classify tokens, like `lie` can be `NN` or `VBP` depending on context
- NLTK sentence parsing allows defining a custom grammar
	- pass `nltk.CFG.fromstring()` rewrite rules
	- initialize `nltk.ChartParser` with the defined grammr
	- parse an ambiguous sentence to get the multiple interpretations!
```Python
grammar = nltk.CFG.fromstring("""
S -> NP VP
PP -> P NP
NP -> Det N | Det N PP | 'I'
VP -> V NP | VP PP
Det -> 'an' | 'my'
N -> 'elephant' | 'pajamas'
V -> 'spotted'
P -> 'in'
""")
parser = nltk.ChartParser(grammar)
sentence = word_tokenize("I spotted an elephant in my pajamas")
for tree in parser.parse(sentence):
	print(tree)
```
	- or even draw the trees
```Python
for tree in parser.parse(sentence):
	tree.draw()
```

## 9. Named Entity Recognition
- NPs that refer to a particular person, object, place
- use `ne_chunk` to label named entities
	- first tokenize and tag
	- then run `ne_chunk` on the tokenized nad tagged sentence
	- see that it differentiates `PERSON`, `ORGANIZATION` and `GPE` (geopolitical entity)
```Python
from nltk import pos_tag, ne_chunk
from nltk.tokenize import word_tokenize

ne_chunk(pos_tag(word_tokenize("Juan joined Udacity Inc. in California")))
```
- performance in the wild varies
	- training on large corpus helps
- example usage: "index and search for news articles on companies of interest"

## 10. Bag of Words
- each doc is unordered collection of words
- examples
	- checking essays for plagiarism
	- analyzing sentiments conveyed by tweets
- Bag of Words steps:
	1. handle raw text: cleaning, normalizing, splitting words, stemming, lemmatization
	2. handle tokens: unordered collection (set)
- inefficiencies
	- each document as a separate set is very inefficient
		- different sizes
		- different words
		- hard to compare
	- what if word occurs multiple times?
	- better representation?
- **corpus (D)** as a set of documents
	- calculate vectors to store words as numbers
	1. find **vocabulary (V)**: unique words in the corpus
	2. order the vocabulary somehow
	3. set up **Document-Term Matrix**: a table illustrating relationship between documents and words
		- have the vocabulary form table columns
		- have each document occupy a row
	4. count the number of ocurrences of each word in each document
		- "how frequently does that term occur in this document?"
		- enter the value in the cell
	5. use this representation somehow
		- e.g. compare two docs to find how many words in common
		- e.g. compare two docs to find how similar term frequencies are
- measures of similarity
	- *dot product*: calculate product sums and find how similar the two vectors are
		- me: simple per-column operation to multiply frequency of a term across each doc being compared
		- me: then sum those products across every column
		- the greater the dot product, the more similar the two vectors
	- *cosine similarity*: divide vector dot product by product of vector magnitudes (Euclidean norms)
		- imagine vectors as arrows in n-dimensional space
		- it's equal to the cosine of the angle between them
		- me: simple operation to sum over each vector and get its frequencies across all vocabulary
		- me: then you multiply their sqrts in the denom, and hold dot product in the num
		- me: this takes into account the info about each vector rather than just the overlap
	- dot product is flawed, since it only captures overlap portions
		- not impacted by values that are not in common
		- so docs that are very different can have same dot product as those that are identical
	- cosine is better, since values range nicely from 1 (most similar) to -1 (least similar)
		- identical vectors have `cos(θ)=1`
		- orthogonal vectors have `cos(θ)=0`
		- opposite vectors have `cos(θ)=-1`

## 11. TF-IDF
- limitation of the Bag of Words
	- every word is equally important
	- intuitively some words are just more common in some docs
- so go back to that table or Document-Term Matrix we set up last time
	- count **document frequency**: the number of documents in which each word occurs
	- divide each term's frequency (each cell) by the document frequency
- usefulness of this metric
	- this metric is proportional to frequency of a term's occurence in a doc
	- this metric is inversely proportional to the number of docs in which a term appears
	- so it _highlights_ each document's more unique words
	- so it _characterizes_ the document by its standout vocabulary
- TF-IDF: the product of two weights
	- `tfidf(t, d, D) = tf(t, d) · idf(t, D)`
	- `tf` is the **term frequency**
		- defined as raw count of a term in a document divided by total number of terms in that document
		- defined as `count(t,d) ÷ |d|`
	- `idf` is the **inverse document frequency**
		- logarithm of total number of documents divided by number of documents where a term is present
		- defined as `log(|D| ÷ |{d∈D:t∈d}|)`
	- varations of this attempt to tweak it
		- normalizing or smoothing values
		- prevent edge cases including division by 0 errors
	- innovative way to assign weights to words

## 12. One-Hot Encoding
- so far we've characterized docs as unit collection of words
- this means our inferences are at document level
	- topic mixture in a doc
	- document similarity
	- document sentiment
- deeper analysis requires numerical representation for each word
- **one-hot encoding** treats each word like a class with an assigned vector
	- back to the table representation
	- place 1 in columns where the same word appears in the row
	- me: an example https://medium.com/@athif.shaffy/one-hot-encoding-of-text-b69124bef0a7

## 13. Word Embeddings
- one-hot breaks with large vocab
	- size of word representation grows with number of words
- how to control size of word representation?
	- limit to fixed-size vector
	- find an embedding for each word in a vector space
- how to make sure properties stay desirable?
	- words similar in meaning closer to each other than distant ones
	- pairs of words with similar distance in meanings are equally separated in the embedded space
- usage
	- finding synonyms
	- finding analogies
	- identifying concepts words are clustered around
	- classifying positive/negative/neutral words
- combining word vectors gives another way of representing documents

## 14. t-SNE
- word embeddings have high dimensionality (for capturing how natural language variation)
	- therefore hard to visualize
- **t-Distributed Stochastic Neighbor Embedding**: dimensionality reduction technique
	- maps high-dimensional vectors to lower-dimensional space
	- like PCA but tries to maintain relative distance between objects
	- similar words stay closer together, and dissimilar ones further
	- great for visualizing word embedding
- visually preserves substructures and relationships learned by the embedding model
- look at larger space to discover related words
- groupings usually quite intuitive
- also works on other data
	- CalTech 101 data set clusters images roughly corresponding to labels for classes
	- useful tool for understanding how a network learns
	- helps identify bugs

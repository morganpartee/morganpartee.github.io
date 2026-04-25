---
layout: post
title: "Making OpenAI Embeddings Easier for Everyone"
date: 2023-01-22
description: "Embedme is a simple Python package that lets anyone with basic programming knowledge experiment with text embeddings using OpenAI's API."
author: "John"
original_slug: easy-python-embeddings
---

> **2026 note:** `text-embedding-ada-002` is deprecated — use `text-embedding-3-small`/`-large`, or open-weights models like `nomic-embed-text` if you want to stay off paid APIs. The Embedme package itself is unmaintained; treat this post as a sketch of the pattern, not a current recommendation.

## Motivation - Thank You!

First, I got a DM from a no kidding stranger this morning, asking a question I've meant to answer for about a month now:

![Motivation](https://i.imgur.com/LtAmDrT.png)

I can't tell you how much this meant to me - This is someone I've truly never met asking me for help, which means I have to be doing something right here... right? The void said something back!

## Python Tools for Easy Embedding Use

(Or: Why I just built something really quick)

First time setup is still a pain with python. OpenAI requires a bit of boilerplate, numpy is intimidating, and the interaction between everything isn't super clear the first time you use it. So I set out to make it a little easier. I *love* Pinecone, but for most folks they just need some simple structures laid out so they can play - which I did with dictionaries for the raw data and numpy for the math bits.

You do still have to know some python - but if you do, you can do some *really cool stuff* with just this library.

If you've read enough, you can [Try it Here](https://pypi.org/project/embedme/), [Check Out the Code](https://github.com/morganpartee/embedme), or [Look at the Moby Dick Example in a Notebook](https://github.com/morganpartee/embedme/blob/main/example.ipynb)

## Using Embedme for Text Embeddings

Embedme is a python package that allows you to easily create and search text embeddings using OpenAI's API. This can be useful for experimenting with different text processing methods and different text chunks before moving to a more complex and costly solution like Pinecone.

Here's an example of how to use the Embedme class to create embeddings for chunks of text from the "Moby Dick" novel using the NLTK library and OpenAI's text-embedding-ada-002 model:

```python
import openai
import nltk
from more_itertools import chunked
from embedme import Embedme
from tqdm import tqdm

# Downloading the NLTK text
nltk.download('gutenberg')

# Creating an instance of the Embedme class
embedme = Embedme(data_folder='.embedme', model="text-embedding-ada-002")

# Getting the text for moby dick
text = nltk.corpus.gutenberg.raw('melville-moby_dick.txt')

# Splitting the text into sentences
sentences = nltk.sent_tokenize(text)

input("Hey this call will cost you money and take a minute. Like, a few cents probably, but wanted to warn you.")

# Send chunks of sentences at once. This is naive AF - but works okayish sometimes.
# If you're feeding this text to gpt-3 to summarize- who cares honestly
# If you want actually good results, drop down to a sentence or two.
for i, chunk in enumerate(tqdm(chunked(sentences, 20))):
    data = {'name': f'moby_dick_chunk_{i}', 'text': ' '.join(chunk)}
    embedme.add(data, save=False)

embedme.save()
```

In this example, we downloaded some text with NLTK, an instance of the Embedme class is created, the raw text of "Moby Dick" is obtained from the NLTK corpus, the text is split into sentences, and then the tqdm library is used to add the embeddings for each chunk of 20 sentences to the Embedme instance. The embeddings are not saved until the end to avoid a ton of writes.

Note that entries *must* have a `name` and `text` key - we store it by name, and embed the text.

You can include literally any other key and we'll return it to you so you can use this like a lazy nosql database, with vector search superpowers. Filenames, page numbers, line numbers, etc may be helpful if you're working with documents, but the possibilities are truly endless, and I want to hear what you learn.

Once you have your embeddings saved you can use the search method to find similar chunks of text to the one you searched for.

```python
# ...later
from embedme import Embedme

# Creating an instance of the Embedme class
embedme = Embedme()

embedme.load()

embedme.search("whale")
```

Gets us (among other things, but #2 was shortest):

```python
 {'moby_dick_chunk_11':
    {'meta': {'name': 'moby_dick_chunk_11',
        'text': 'Do you see that whale now?" "Ay ay, sir! A shoal of Sperm Whales! There she blows! There she\r\nbreaches!" "Sing out! sing out every time!" "Ay Ay, sir! There she blows! there--there--THAR she\r\nblows--bowes--bo-o-os!" "How far off?" "Two miles and a half." "Thunder and lightning! so near! Call all hands." --J. ROSS BROWNE\'S\r\nETCHINGS OF A WHALING CRUIZE. 1846. "The Whale-ship Globe, on board of which vessel occurred the horrid\r\ntransactions we are about to relate, belonged to the island of\r\nNantucket." --"NARRATIVE OF THE GLOBE," BY LAY AND HUSSEY SURVIVORS. A.D. 1828.'},
        ...
    }}
```

Pretty cool, right? To get even narrower results we could try this at the 1-3 sentence level, or add overlap, but this is where I am still trying very naive approaches - OpenAI embeddings got so good so fast, I was not prepared to use them well.

Pinecone is so good for so many things, but overcomplication kills projects. This lets us try it mostly locally - with the exception of OpenAI (incredibly cheaply - worth it).

## Raw Access

Once you've embedded all of your data, you can directly access the underlying embeddings (in a numpy array) after calling `.prepare_search()` (which happens pre-search normally, after things are added to the index).

```python
embedme.prepare_search()
vecs = embedme.vectors # np array!
```

## Security, Etc

This is for rapid prototyping - I wouldn't stick secrets in this. Just authenticate to openAI like they tell you to and this should work fine until you run out of ram - which is going to take shockingly long. The new embeddings are pretty compressed, you have tons of records before you have to worry.

Security is handed off to OpenAI - I just skip a few boilerplate steps that nobody wants to type again (myself included).

## Get Hacking!

I desperately need information on how folks are using embeddings - there is so much power there, but I just have no idea how best to represent the data going into an embedding.

Writing this article cost me about six cents worth of embeddings because I had to make it read Moby Dick about four times (as I broke and fixed my code...) - something you couldn't have paid me a million to do. The value is obvious - it just takes work to organize and capture it.

Like I said earlier, a more complete example and the (shockingly little) code are here:

[Try it Here](https://pypi.org/project/embedme/)

[Check Out the Code](https://github.com/morganpartee/embedme)

[Look at the Moby Dick Example in a Notebook](https://github.com/morganpartee/embedme/blob/main/example.ipynb)

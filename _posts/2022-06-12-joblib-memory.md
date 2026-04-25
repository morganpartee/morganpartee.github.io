---
layout: post
title: "Speeding Up Data Science Iteration with joblib.Memory"
date: 2022-06-12
description: "Iterating on models quickly is one of the most important things we can do as data scientists. Here's how I use joblib to avoid frequent calls to my database, while working with production-ready code."
author: "John"
original_slug: joblib-memory
---

Way too often, I see data scientists either downloading data to csv's and doing their data loading work twice, or querying databases every time they tweak a hyperparameter. Joblib's Memory object allows us to cache function results with just a decorator, so we can write the dataloading code once, and use a local cache to not spam our database!

A 'decorator' is a function that takes a function as an argument. In python, we have a short syntax for calling decorators. Below we'll use `@memory.cache` to apply joblib's caching! It takes our defined function as an argument, and returns a function that does the same thing, but caches the results of a function based on the inputs.

```py
from joblib import Memory

cache_location = './cache'
memory = Memory(cache_location, verbose=0)

@memory.cache
def download_data(query):
    ...
```

Or to use this on a function directly (like from another library):

```py
from joblib import Memory
from helpers import download_data

cache_location = './cache'
memory = Memory(cache_location, verbose=0)

cached_fetch = memory.cache(download_data)

#Then use cached_fetch just like you would download_data!
data = cached_fetch("query")
```

And that's it! Set the verbosity higher if you want to see what it is doing, and check out the cache folder too. There's handy data in json format that details what has been cached.

One word of caution - Be aware of your arguments to your function and how they will change. If you provide the same query daily and expect different results, this will not help you!

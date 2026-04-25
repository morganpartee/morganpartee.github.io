---
layout: post
title: "The Best Python Libraries for Data Science"
date: 2022-06-12
description: "There are a few libraries that I constantly use at work. Just an understanding of when they're useful makes you a better Data Scientist."
author: "John"
original_slug: python-libraries-for-data-science
---

If you're getting started in the data science world, here's a few libraries that you need to check out. The important thing with 'learning' a library is to find a reason to use it. You need a real project to make it stick!

------

## Machine Learning

Starting with the sexy stuff up front, this is what I use to ship real production machine learning. This covers about 80% of what I do, the rest is largely logistical (and cloud engineering!)

[scikit-learn](https://scikit-learn.org/stable/) - Duh. It's used everywhere because it's really good. Check out the tutorials, find some data, and fit some lines!

[River](https://riverml.xyz/0.11.1/introduction/installation/) - This library is badass if you need updatable (streaming!) models. I use thousands of their regressions models to project the output of thousands of devices, every day at work. The bottleneck is the database! Plays nicely with [pickle](https://docs.python.org/3/library/pickle.html) too.

-------
## Incredible Multitool

Scipy stands alone because it is incredibly useful for feature engineering, but has kickass data structures too. Have sparse data? `scipy.sparse` is your new best friend. I used it to run ML on a multi-million column matrix with no issues. Lots of help with linear algebra, preprocessing, and signal processing (which will be a whole article some day). Also some statistical programming help that scikit-learn lacks. `find_peaks` is my latest obsession, it's just *really* good.

[scipy](https://docs.scipy.org/doc/scipy/reference/)

------

## Data Structures and Manipulation

[numpy](https://numpy.org/) - Kind of a pain to work with, but *really* good at what it does. If it needs to be fast, it needs to be numpy. Multidimensional arrays, matrix math, and data cleaning *can* be done with numpy. It is **hard** to learn on its own, but [101 Numpy Exercises](https://www.machinelearningplus.com/python/101-numpy-exercises-python/) will get you there.

[pandas](https://pandas.pydata.org/docs/index.html) - Slower than numpy for most things, but not slower by dev time. If you're dealing with time series data, go this route. Handles datetimes exceptionally well. Very useful for reading/writing csv's, as the Python builtin is clunky. Can pull directly from sql/csv's/parquet too. Rolling statistics are a particular joy, rolling median is remarkably hard to implement in numpy. All? I believe? of scikit-learn supports pandas' DataFrames as inputs, so go this route if you're starting out.

[smart-open](https://pypi.org/project/smart-open/) - Replaces open. Provides s3 (and WAY more) support. Stupidly useful. Reading in a csv from s3? Use this with pandas.

For regular Python data (in lists, dictionaries, etc):

[collections](https://docs.python.org/3/library/collections.html) - Counter is super useful, and FAST.

[itertools](https://docs.python.org/3/library/itertools.html) - Different ways to iterate or combine data. Product, groupby, and chain are very useful.

## Visualization

[matplotlib](https://matplotlib.org/) - It isn't always pretty, and it is always easy, but if it's a quick graph for myself, it's in matplotlib. Plays very nicely in notebooks, less useful in production.

[plotly](https://plotly.com/) - Interactive charts, handy as hell, can be iteratively generated too. Can be saved off as standalone html files, which I send to my experts for feedback when we're building a model!

## Get to work!

That is most of what you need to do the daily work of a data scientist. Sometimes you'll find a specific problem (especially in signal processing!) that might have a better specialty package. Search Medium and Google for those! Kalman filters are a great example, good luck writing one yourself without `pykalman`!

Before you start installing packages though, make sure you're set up with [VSCode and some plugins]({% post_url 2021-03-10-vscode-defaults %}).

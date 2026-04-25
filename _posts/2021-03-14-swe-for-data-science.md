---
layout: post
title: "Just Enough Software Engineering (For Data Science)"
date: 2021-03-14
description: "After I got my masters I still couldn't DO anything with ML. Here's some of the tools I like now."
author: "John"
original_slug: swe-for-data-science
---

Well, here's the slides from a presentation I gave to a group of SMU students a few months ago. There's a lot of technical (over)simplification here because the audience doesn't really care about the finer points of software architecture, they just want to make a model more useful to the other developers on their team. Tony might post a (much better informed) rebuttal below, so I'll tell you to take all of this with a grain of salt. I'm no expert, I'm just okay at getting us started with an ML driven project.

Probably the most important part of this presentation is slide 4 - Know where your model fits.

- What are your inputs?
- What are your outputs?
- What are you actually predicting?

After that, knowing how to hand it to other developers is super helpful!

Here's some sensible defaults for shipping ML projects.

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vQ4WAzqWRZG72LrE8rqIP4KAaXqD16woMaXtVIKTxZaonIarrbRiJ8FVJjTcuJRAJ993wjYo4Tgba4K/embed?start=false&loop=false" frameborder="0" width="1440" height="839" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

So, go answer those three questions up top and try to make something. Learn a little about REST APIs, FastAPI, and Docker and you'll be a way stronger contributor on your team.

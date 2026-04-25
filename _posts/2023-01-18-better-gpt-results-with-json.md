---
layout: post
title: "Better GPT Results with JSON (Or whatever!)"
date: 2023-01-18
description: "GPT-3 (and ChatGPT) talk English primarily — what if you need to feed it a bunch of information? What if you need a bunch back? JSON works pretty well."
author: "John"
original_slug: gpt-json
---

## Making GPT Repeatable is hard

If you haven't heard of JSON that is fine - it's just a way we send data on the internet a lot of the time. It's computer and human-readable which is nice, but if we rely on GPT to do it we can get mixed results. They were mixed for me (badly, badly mixed) when I found this tweet a few weeks ago:

> I tweet GPT-3 examples with simple colon prompting for legibility, but for real applications this is a bad idea. Inputs and outputs should almost always be JSON, with quoting/escaping of strings, as shown here. Otherwise inputs can mimic prompt syntax. [pic.twitter.com/svUGEmcOcm](https://t.co/svUGEmcOcm)
>
> — Riley Goodside (@goodside) [August 10, 2022](https://twitter.com/goodside/status/1557220192375160832?ref_src=twsrc%5Etfw)

`Inputs can mimic prompt syntax` was the shock for me. That means that if we aren't careful with how we provide context (especially when we're getting started with ChatGPT or scaling up with GPT-3!), it can get confused really easily.

Riley's example of translations is an interesting one - If you're using an awesome model like `text-davinci-003` it could be expensive to send that request for each language. Why not group them so that we provide the file once, and get a few outputs back?

## A Bigger (Better?) Example

I've heard several folks now say something like "We have all of these meeting notes, but they're so hard to follow!". Of course, there's a GPT powered solution to this.

Here's one approach. I had ChatGPT write this with me, so it is a litte verbose, but it mostly works.

json

```
Summarize the following meeting notes, by extracting the people, places,
and ideas discussed in the text and outputting a JSON object with an array
of these entities, using the following format:

Input:
{
    "meeting_notes": "At the meeting, John Smith presented his proposal for a new project in Anytown USA. Jane Doe provided feedback on the project and raised concerns about the budget. Bob Johnson suggested using a different approach to reduce costs. The team discussed the feasibility of the project and decided to move forward with it. Anytown USA was mentioned as the location for the project."
}

Output:
{
    "people": ["John Smith", "Jane Doe", "Bob Johnson"],
    "places": ["Anytown USA"],
    "ideas": ["new project","budget concerns","different approach","feasibility"]
}

Input:
${input_text}

Output:
```

Now bear with me - That looks scary, but it's just another way of telling GPT that data is *different*. You could use bullet points, dashes, or whatever you like to represent that data - you just need to make it *obvious*.

There are two other little tricks that make this prompt even better - examples and reinforcement. We show GPT the format in the data that we hand it, and we reinforce the overall idea by providing a JSON example to it showing an input it could expect, and an output it should've returned.

It's hard to grasp at first - the difference is gpt learns by example really well. My first JSON attempt was about thirty words specifying the output format and that python must be able to read it in... it worked about half the time!

Examples, especially sneaky examples of format in how we hand gpt our data, can give you way better results than trying to explain it.

## What works better?

This is the method in my little project CodeGPT - where JSON was a pain.

> Here’s a more esoteric method to get quote safety without escaping. Here we use an ad hoc, indentation-based method of quoting strings so we can use simple colon labels without fear of newlines in inputs: [pic.twitter.com/G1EnCZIFm5](https://t.co/G1EnCZIFm5)
>
> — Riley Goodside (@goodside) [August 10, 2022](https://twitter.com/goodside/status/1557227829145882624?ref_src=twsrc%5Etfw)

The catch here is we use `>` to indicate that this is *different*. The 'key' for each value is on the line above the `>` block, and we count anything with a `>` in front of it as a part of the response until we get the next key without the carrot.

The big problem this solves is JSON is pretty notoriusly finicky - and if we're trying to bulk process results those failures count! That's still money we've spent that we now can't recover - because code has quotes in it, and GPT doesn't know how to escape them.

My example looks something like this (The code we're working on is in a block similar above this section):

ref

```
You may only send me complete files.

For each file, return one block in this format, using '> ' to indent values:

filename:
> <the filename to be output>
explanation:
> <The changes that you made>
code:
> <code line 1>
> <code line n...>

You must include the filename, an explanation of what you did, and the code for the file to be output, regardless of the format or file.
```

[Longer Example on GitHub](https://github.com/morganpartee/codegpt/blob/main/codegpt/gpt_interface.py#L50)

It's not the most efficient prompt or route I'm sure - but it largely got rid of parsing errors for me for CodeGPT which is nice. Being able to just send files back and forth is handy in some situations, code especially.

## Caveat - You probably don't need this

The other thing I've found is that a lot of the time (especially easy stuff - Answering simple questions, summarizing) you'll have just as good results from splitting the problem into smaller parts and having a cheaper model try to answer for you. Cheaper models require a little more finagling to get working right - like the prompt up top! But you can send 10 requests for the price of one at each level you step down - use a bunch of them!

The problem of confusing GPT with data is still a big issue - providing that data in JSON (or whatever!) format makes a TON of sense, especially for live systems where a human isn't reading the output - a computer is. But if we can get to a point where we only expect ONE answer from GPT per request, we can build some cool stuff fast - without having to build a parser.

And hey, if JSON is too scary - just try block quotes. Three quote marks, input your data, three quote marks. GPT oughta get it.

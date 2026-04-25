---
layout: post
title: "Why are my React functions running twice?"
date: 2022-06-13
description: "While I was building Tacticle, my React Wordle clone for chess, I ran into this problem and found the solution!"
author: "John"
original_slug: why-react-functions-run-twice
---

I'm a fairly new React developer, I've only been working on it for about the last two years, *very* off and on. I've noticed some effects being called more than once, but never really thought much of it. With [Tacticle](https://tacticle.co) I had to use a mutable object (a chess game, thanks chess.js!) in my state, which caused real problems when I was modifying it.

## Sources and Problem

Thanks to [this stackoverflow thread](https://stackoverflow.com/questions/50819162/why-is-my-function-being-called-twice-in-react) and [this github issue](https://github.com/facebook/react/issues/12856), I figured out why. React is designed to help prevent side effects in code, and functions are run twice on purpose **only in development** to try to emulate what could happen if a page was disconnected and reconnected, or some other bug that caused a function to *actually* run twice. In the perfect functional world, nothing would happen. But with mutable state, this is a problem!

## Solution

[create-react-app](https://github.com/facebook/create-react-app) - The boilerplate that a lot of people use, enables React strict mode by default. In our `index.jsx` (or `.tsx`!), we'll see something like this:

```jsx
ReactDOM.render(
    <React.StrictMode>
    <App />
    </React.StrictMode>,
  document.getElementById('root')
)
```
<br/>

Just remove the `<React.StrictMode>` component!

```jsx
ReactDOM.render(
    <App />,
  document.getElementById('root')
)
```

## Be Warned!

`React.StrictMode` is there to help prevent *real* problems in your code *while developing*. It shouldn't cause functions to double run in production. In this case, I couldn't find a way around mutating my state directly, so I had to disable strictmode, which comes with bug risks.

[React Documentation](https://reactjs.org/docs/strict-mode.html) covers what StrictMode does - Check it out if you haven't read up on it before.

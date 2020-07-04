---
title: "React Prevent Re-renders"
date: 2018-11-07T09:26:33Z
description: "How to improve React rendering performance by preventing unnecessary renders"
categories:
  - development
tags:
  - react
---

Today I learned how to prevent React from unnecessarily re-rendering a component:

```javascript
shouldComponentUpdate(nextProps) {
  // Compare the props with one another
  if (this.props.actions !== nextProps.actions) {
    return true;
  }

  return false;
}
```

I was trying to use a loop to sort some items prior to rendering them in a component. During the debug step I kept seeing the loop run twice, when I was only expecting it to run once.
<!--more-->

After spending a little too long thinking my loop was the problem, it suddenly clicked that React was re-rendering my component in reaction to a prop change. Although the props have changed only once, for some (slightly unknown **†**) reason the component was being rendered twice. By implementing a check with `shouldComponentUpdate` I can prevent the unnecessary render.

**†** I used another React lifecyle method initially to confirm the component was being re-rendered:

```javascript
componentDidUpdate(prevProps) {
  if (this.props !== prevProps) {
    console.log("Re-rendering...");
  }
}
```

Although I visually inspected `this.props` against `prevProps` and to me they looked identical, they obviously aren't.

A final note from React's documentation to remember when debugging:

**`componentDidUpdate()` will not be invoked if `shouldComponentUpdate()` returns false.**
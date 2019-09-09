---
title: "Declarative vs. Imperative programming"
date: 2019-09-09T18:11:45+01:00
categories:
  - development
tags:
  - meta
---

Today I learned more about the distinction between *declarative* and *imperative* programming. I have seen the terms used in various places, but I could not give a good explanation about what they mean. So with a bit of curiosity and I thought that I would get a better handle on the definition.

> Imperative programming is like **how** you do something, and declarative programming is more like **what** you do.
<!--more-->

To me the above definition outlines the difference between the two *paradigms*, but it needs a good example to really understand what that means.

Modern JavaScript has a lot of nice features that can illustrate the difference. Let's take a simple idea - add up an array of numbers and output the answer. That could be written two ways:

## Declaratively

```javascript
function add(array) {
  let result = 0;

  for (let i = 0; i < array.length; i++) {
    result = result + array[i];
  }

  return result
}

console.log(add([1,2,3]));
// Output: 6
```

The code above is **declarative**. It tells you *what* it is going to execute. It takes an array, sets a result variable, loops over the array and adds the values, and eventually it outputs the result. There is nothing wrong with this code in my opinion. But you can imagine a more complex function might end up with a lot of LOC that could affect maintainability and readability.

## Imperatively

```javascript
function add(array) {
  return array.reduce((prev, current) => prev + current, 0);
}

console.log(add([1,2,3]));
// Output: 6
```

The **imperative** example is interesting. It requires a bit more knowledge about native JavaScript array features, namely the use of `reduce` (which I only began to use fairly recently). `reduce` is an abstraction, as in it does some work for you under the hood. It allows the add function to be written in one LOC. It tells you *how* it is going to execute (that `reduce` is going to be used on the array). But it does take that slight advanced knowledge and to me, is only more readable once you understand what `reduce` does.

## Summary

I know have a better idea about what the two terms mean. I am going to use this knowledge and reflect upon the code I write and how it can be improved by making it more *imperative*.
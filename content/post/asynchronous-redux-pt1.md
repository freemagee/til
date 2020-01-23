---
title: "Asynchronous redux - Part 1"
date: 2019-10-28T09:29:17+00:00
categories:
  - development
tags:
  - react
  - redux
---

This is not a traditional **"Today I learned"** post. It is more like a **"Recently I learned"**. I have been using [Redux](https://redux.js.org/) as a system to control state across an app that has a lot of state. Once you get used to the actionType, action, reducer and selector pattern it really provides a deep level of control. 

## State complexity

It was not long before the need for complex redux actions became clear when the app required data from APIs. That data needed to be requested, processed and added to the redux store before other components could consume it. 

This lead me to [Redux Thunk](https://github.com/reduxjs/redux-thunk) as a way to define actions that were more complex than simply returning a plain object. Often these actions would need to execute promises. So a need to control the sequence of these actions was becoming a priority for the app.
<!--more-->

## Control the execution of promises

You can execute an array of promises using [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all). But the nature of async code is that promises are independent of one another and can complete at different times and out of sequence. The documentation explicitly discusses this:

> There is no implied ordering in the execution of the array of Promises given

[Source](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

### Example of promises executing out of sequence

```javascript
const promise1 = randomPromise(1);
const promise2 = randomPromise(2);
const promise3 = randomPromise(3);

Promise.all([promise1, promise2, promise3]).then(function(values) {
  console.log(values);
});

function randomPromise(id) {
  // Each promise can take up to 5 seconds to execute
  const delay = Math.random() * (5000 - 100) + 100;
  return new Promise(resolve =>
    setTimeout(() => {
      console.log(id);
      resolve([id, delay]);
    }, delay)
  );
}

// console
// 1
// 3
// 2
// [[1, 2597.6343345022324], [2, 4417.044144540687], [3, 4045.777037995134]]

```
[Link to example](https://jsbin.com/jabihuwesu/edit?js,console)

After reading up on executing promises *in sequence* I found a nice [Stack Overflow response](https://stackoverflow.com/a/41115086) that covered what I wanted.

```javascript
const serial = funcs =>
  funcs.reduce(
    (promise, func) =>
      promise.then(result => func().then(Array.prototype.concat.bind(result))),
    Promise.resolve([])
  );
```

This function takes an array of **functions** that return **promises**. That distinction is important. It does not take an array of promises like `Promise.all`. It takes the array of functions and executes them serially. Finally it returns a promise, so importantly `serial` is .thenable and therefore you can know when it the functions provided to it have all successfully resolved their promises.

### Example of promises executed serially

```javascript
const func1 = () => {
  return new Promise(resolve => {
    const random = Math.random() * (5000 - 100) + 100;

    setTimeout(() => {
      console.log("Func1 resolved")
      resolve();
    }, random);
  });
};

const func2 = () => {
  return new Promise(resolve => {
    const random = Math.random() * (5000 - 100) + 100;

    setTimeout(() => {
      console.log("Func2 resolved")
      resolve();
    }, random);
  });
};

// Ref: https://stackoverflow.com/a/41115086
const serial = funcs =>
  funcs.reduce(
    (promise, func) =>
      promise.then(result => func().then(Array.prototype.concat.bind(result))),
    Promise.resolve([])
  );

const promiseArray = [func1, func2];

serial(promiseArray).then(() => {
  console.log("All promises resolved");
});

// console
// "Func1 resolved"
// "Func2 resolved"
// "All promises resolved"
```

[Live example](https://jsbin.com/sikacovila/edit?js,console)

So now I had a way to control the sequence of execution of async actions in my app. I will show the pattern of how this is used in my app in the upcoming [Part 2](https://til.neilmagee.com/post/aynchronous-redux-pt2/).
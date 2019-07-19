---
title: "React Redux is pretty great!"
date: 2019-06-25T10:17:37+01:00
categories:
  - development
tags:
  - react
  - redux
---

Today I learned to really appreciate [React Redux](https://react-redux.js.org/). I have used [Flux](https://redux.js.org/introduction/prior-art#flux) like patterns only a handful of times. Mainly in Vue using [Vuex](https://vuex.vuejs.org/). But it's been a while since I last used one.

I am in the middle of building a prototype App in React and the *"main"* component was becoming unwieldy. It contained all the main state, all the functions that were passed to child components as props and the way things were going, it was about to become responsible for asyncronous data as well. That is too much for one component.
<!--more-->

So I began refactoring this prototype App to use React Redux. After getting to grips with the structure of Redux and where everything went ([this codesandbox](https://codesandbox.io/s/9on71rvnyo) was the most useful for me to understand the structure), slowly the feeling of friction departed and with every bit of code refactored, I could see the advantages of Redux immediately.

The *"main"* component has now been reduced from **216** lines to **51** and I feel the seperation of concerns has sped up refactoring of the child components. I can see that the previous non-redux version of this app was a fragile house of cards, where every child component depended on passing data up the chain until it eventually got to the top and had an effect. Now the *"main"* component does not care nor know about how the child components work. Which is pretty great!
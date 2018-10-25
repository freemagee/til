---
title: "React With Debounce"
date: 2018-10-24T20:10:38+01:00
draft: true
categories:
  - development
tags:
  - react
  - pwa
---

Today I learned how to debounce inputs in a [React](https://reactjs.org/) component. This is something I have done in many other places, in plain JavaScript, in jQuery, in AngularJs and in VueJs. Whilst the concept is the same:- have a user input some data (text, date, email, whatever), wait a short amount of time so that the user has finished typing and then do something. The idea being the "do something" does not happen whilst the user is still adding their data. In todays example I had two inputs, both took a numerical value, as long as the inputs passed a validating step a calculated value was created and shown on the screen when they finished. To get that to happen, took me a little longer than expected. Here is a basic breakdown:

```javascript
// Some code
```

The only way I could get this to work was to have one method that handled the input and controlled another debounced method. I spent a while reading and trying examples from this Stack Overflow question, but they way the top answer suggested to do it, I just could not do it exactly.
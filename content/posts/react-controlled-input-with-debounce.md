---
title: "React controlled input with debounce"
date: 2020-03-02T11:31:54+00:00
description: "How to write a controlled input in React with debounce effect"
categories:
  - development
tags:
  - react
---

I previously wrote a post about using [Lodash debounce with a React component](https://til.neilmagee.com/post/react-with-debounce/). I recently had to use a controlled input, that needed a debounce effect on it. But the difference between this new component and the one in my previous post was this new component was written in a functional way, using React hooks.

So the scope for this component would be to take an initial value from it's parent, handle changes in state, and then when the user has stopped typing, after a short delay, to lift the state up to the parent so that the input value can trigger something in the parent.

## Immediate problems

After writing the new component to use Lodash debounce, and taking advantage of `useEffect` to trigger side effects based on the input value changing I immediately saw a problem. I have increased the debounce "delay" to two seconds to exaggerate the effect.

{{< figure src="/images/react-controlled-input-with-debounce/debounce-fail.gif" title="Debounce gone wrong!" alt="Animated gif showing a debounce not working correctly" >}}
<!--more-->
What is happening here, is as the user types, the debounce is being triggered after every value change. So each letter is passed to the parent two seconds after is was typed. So from the **gif** you can see the parent receiving the new value one letter at a time. This is clearly not right.

## Trying to fix the issue

I tried a few different methods, experimented with `event.persist()`, over-complicated my `useEffect`, but I could not get Lodash debounce to work the way I wanted in this instance.

So I took a step back and thought about it from the ground up. Debounce's main action is like a `setTimeout` ([there *is* more to it](https://lodash.com/docs/4.17.15#debounce)). So with that in mind I looked to re-write the component without Lodash. This eventually led me to make this component.

```javascript
import React, { useState, useEffect } from "react";
import PropTypes from "prop-types";

DelayedInput.propTypes = {
  type: PropTypes.string,
  initialValue: PropTypes.string,
  inputDelay: PropTypes.number,
  placeHolder: PropTypes.string,
  setInput: PropTypes.func.isRequired
};

function DelayedInput({
  type = "text",
  initialValue = "",
  inputDelay = 300,
  placeHolder = "",
  setInput
}) {
  const [value, setValue] = useState(initialValue);

  useEffect(() => {
    const timer = setTimeout(() => {
      setInput(value);
    }, inputDelay);
    return () => clearTimeout(timer);
  }, [value, inputDelay, setInput]);

  function handleChange(event) {
    setValue(event.target.value);
  }

  const theValue = value;

  return (
    <input
      type={type}
      value={theValue}
      placeholder={placeHolder}
      onChange={handleChange}
    />
  );
}

export default DelayedInput;
```

When the `useEffect` recognises a change to `value`, a new timer is created. If no more `value` changes are detected, that timer completes and the state is lifted up to the parent. If a `value` change is detected, the timer is reset and it once again waits for more changes and completes if there are none. 

**Below** shows the parent receives its text in one go. Again I have increased the debounce "delay" to two seconds to exaggerate the effect. In production the delay is 300ms.

{{< figure src="/images/react-controlled-input-with-debounce/debounce-success.gif" title="Debounce working correctly" alt="Animated gif showing a debounce working as expected" >}}

I put this together as a demo on Github as [React Debounced Input](https://github.com/freemagee/react-debounced-input). You can see it in action [here](https://freemagee.github.io/react-debounced-input/).
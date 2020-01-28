---
title: "Controlled inputs and performance"
date: 2019-06-04T09:48:31+01:00
description: "The performance costs of using React controlled inputs"
categories:
  - development
tags:
  - react
---

Today I learned about the performance impact of using [controlled inputs](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/) in React.

I noticed that once I had multiples of a component I'd made on screen *(that included text inputs)* the typing performance would begin to lag. It also got exponentially worse for each additional component.

## onChange vs onBlur

The main difference I tried was changing my event from `onChange` to `onBlur` (this was based on some [Stack Overflow](https://stackoverflow.com/a/38914965) advice).

### Before

{{< figure src="/images/controlled-inputs-performance/bad.gif" title="Laggy input performance" alt="Animated gif showing poor input performance" >}}
<!--more-->
```javascript
// below uses onChange and value properties
<TextField
  name="question"
  value={question}
  id="question"
  label="Question"
  onChange={handleQuestionChange}
  required
  fullWidth
  autoFocus
/>
```

### After

{{< figure src="/images/controlled-inputs-performance/good.gif" title="Normal input performance" alt="Animated gif showing improved input performance" >}}

```javascript
// below uses onBlur and defaultValue properties
<TextField
  name="question"
  defaultValue={question}
  id="question"
  label="Question"
  onBlur={handleQuestionChange}
  required
  fullWidth
  autoFocus
/>
```

Using `onBlur` still updates the value of the `TextField` component by passing the value up to the parent component via props. That parent component is responsible for state and keeps track of this inputs value.

Often whilst putting together interfaces it is not until the testing stage that performance problems become apparent. With this knowledge, hopefully as I build more components I will try to avoid these issues.
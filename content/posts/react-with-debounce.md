---
title: "React With Debounce"
date: 2018-10-24T20:10:38+01:00
categories:
  - development
tags:
  - react
---

Today I learned how to debounce inputs in a [React](https://reactjs.org/) component. This is something I have done in many other places, in plain JavaScript, in jQuery, in AngularJs and in VueJs. Whilst the concept is the same - have a user input some data (text, date, email, whatever), wait a short amount of time so that the user has finished typing and then do something. The idea being the "do something" does not happen whilst the user is still adding their data. 

Here is a simplified example of what I did:

```javascript
import React from "react";
import debounce from "lodash/debounce";

export default class MyForm extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      name: "",
      output: ""
    };
  }
  handleChange = e => {
    const value = this.parseInput(e.target.value);
    this.setState({ name: value });
    this.delayedChange();
  }
  delayedChange = debounce(() => {
    // Do something cool!
    this.setState({ output: `Hi! My name is ${this.state.name}` });
  }, 1000)
  parseInput(val) {
    // do some parsing!
    return val;
  }
  render() {
    return (
      <div className="myForm">
        <div className="myForm__form-group">
          <label htmlFor="name" className="myForm__form-label">
            Name
          </label>
          <input
            type="text"
            className="myForm__form-control"
            id="name"
            value={this.state.name}
            onChange={this.handleChange}
          />
        </div>
        <p>{this.state.output}</p>
      </div>
    );
  }
}
```

The only way I could get this to work was to have one method that handled the input and then triggered another debounced method. I am using [lodash](https://lodash.com/docs/4.17.10#debounce) for the debounce functionality. There is a really good explanation by Sebastian Lorber on [Stack Overflow](https://stackoverflow.com/a/28046731) on how to do this properly.
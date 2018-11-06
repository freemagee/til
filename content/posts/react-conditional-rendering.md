---
title: "React Conditional Rendering"
date: 2018-11-05T17:13:02Z
categories:
  - development
tags:
  - react
---

Today I learned how to conditionally render a React component. I was using CSS to show and hide different components in reaction to the App's state. Setting a component to be `display: none;` or control it's opacity. The problem was that React was throwing up some warnings that were coming from "invisible" components, that were receiving props before they were really meant to. This is probably representative of a deeper problem in my App structure.

Having finally read the [conditional rendering](https://reactjs.org/docs/conditional-rendering.html) page, I have chosen the "Inline If with Logical && Operator" pattern for my App. Below is a stripped down example:

## Parent App

```javascript
import React, {Component} from "react";
import MyComponent from "./components/MyComponent.jsx";

class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      componentIsActive: false
    };
  }
  setComponentActive() {
    // Something else could trigger this
    this.setState({
        componentIsActive: true
    });
  }
  render() {
    return (
      // The component is passed a prop from the state
      <MyComponent
        active={this.state.componentIsActive}
      />
    );
  }
}

export default App;
```
<!--more-->

## Child Component

```javascript
import React from "react";

export default class MyComponent extends React.Component {
  render() {
    // Whilst the prop is false, the component will render "empty"
    const active = this.props.active;

    return (
      <div className="myComponent">
        {active && (
            <p>Yipee, I am active</p>
        )}
      </div>
    );
  }
}
```

### A catch

One catch to watch out for is the conditional element must comprise of only one DOM node. In my App, I have two form fields, so I need two conditionals:

```javascript
<div className="EditNote" data-edit-mode={this.props.mode}>
  {title !== "" && (
    <div className="EditNote__control">
      <input
        className="EditNote__title"
        type="text"
        value={title}
        name="title"
        onChange={this.handleChange}
      />
    </div>
  )}
  {content !== "" && (
    <div className="EditNote__control">
      <textarea
        className="EditNote__content"
        value={content}
        name="content"
        onChange={this.handleChange}
      />
    </div>
  )}
</div>
```

I was initially confused by this because of my experience using VueJS. In that framework, it appears that a component is not rendered until it is needed. If you inspect the DOM, there is nothing there. But if I inspect my ReactJs App DOM, I can see the elements are there, although they may be invisible.
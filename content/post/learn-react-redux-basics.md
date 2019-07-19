---
title: "The basics of React Redux"
date: 2019-05-20T16:39:16+01:00
categories:
  - development
tags:
  - react
---

Today I learned the basics of [React Redux](https://react-redux.js.org/) because the `state` requirements of the React app I have been building are now in need of a better solution.

I am a fairly experienced frontend developer, and I know how to use lots of technologies. But I was struggling with grasping the fundamentals of using React Redux. I have used [Vuex](https://vuex.vuejs.org/) previously, which I picked up pretty quickly and I thought it would be similar. They are similar in concept, but not in execution. I think Vue deserves a lot of credit for their documentation as it is really good.

What I was looking for was a basic tutorial, the *most* basic. But every "basic" tutorial was how to make a *todo* app. But I honestly needed a *Hello world*, just to see how the code was wired together. This is from the redux documentation:

> Don't be fooled by all the fancy talk about reducers, middleware, store enhancers—Redux is incredibly simple. If you've ever built a Flux application, you will feel right at home. If you're new to Flux, it's easy too!

[Source](https://redux-docs.netlify.com/basics/basic-tutorial)

I would agree in hindsight that it is relatively easy, but the path to that knowledge is shrouded in fog. It was time to find alternative education!
<!--more-->

{{< youtube OSSpVLpuVWA >}}

So if you are in the same position as me, I took to YouTube and found [this video](https://www.youtube.com/watch?v=OSSpVLpuVWA) by **Learn Coding Tutorials**, which had the one thing I needed - to see the redux code working in a single file. I have included that code below for reference. This code is working on top of an app created via `create-react-app`.

### index.js
```javascript
import React from "react";
import { render } from "react-dom";
import App from "./AppSimple";

import { combineReducers, createStore } from "redux";
import { Provider } from "react-redux";

function userReducer(state = "", { type, payload }) {
  switch (type) {
    case "updateUser":
      return payload.user;
    default:
      return state;
  }
}

const allReducers = combineReducers({
  user: userReducer
});

const store = createStore(
  allReducers,
  {
    user: "Michael"
  },
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);

console.log(store.getState());

const updateUserAction = {
  type: "updateUser",
  payload: {
    user: "John"
  }
};

store.dispatch(updateUserAction);

console.log(store.getState());

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

### App Simple
```javascript
import React, { Component } from "react";

import { connect } from "react-redux";

class App extends Component {
  render() {
    return (
      <div>
        <p>Hello {this.props.user}</p>
      </div>
    );
  }
}

const mapStateToProps = state => ({
  user: state.user
});

export default connect(mapStateToProps)(App);

```

<!--more-->
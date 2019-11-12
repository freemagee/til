---
title: "Asynchronous redux - Part 2"
date: 
categories:
  - development
tags:
  - react
  - redux
---

Following on from my [previous post](https://til.neilmagee.com/post/aynchronous-redux/), I am going to show a real example of asynchronous redux. The pattern below is used in many places in the app I am working on. It provides control for what is going to happen when the app needs to request/interact with data from the API.
<!--more-->

## First, creating some thunks

A helper function was created that takes [`dispatch`](https://redux.js.org/api/store#dispatchaction), an [`action creator`](https://redux.js.org/basics/actions#action-creators) and its arguments and returns a [**Thunk**](https://github.com/reduxjs/redux-thunk).

```javascript
function generateThunk(dispatch, action, ...args) {
  return () => Promise.resolve(dispatch(action(...args)));
}
```

`generateThunk` **returns** a function that **returns** a promise that is resolved immediately that **dispatches** an *action*. It is a bit convoluted. But it has reduced a lot of repetition in our app. Arguments are provided to the *action* with the [spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax), which allows zero or more arguments to be passed along. Pretty useful.

These **Thunks** were created to be executed serially. See [Asynchronous redux - part 1](https://til.neilmagee.com/post/aynchronous-redux/#example-of-promises-executed-serially) for more explanation on that.

## Example of a thunk async action

This **thunk async action** serves a few purposes. It contacts an API endpoint and upon success it will update the app state. It also controls various flags that show the request status and finally returns a resolved promise with a user message in it. I have tried to comment the example below, as the nesting can become hard to understand.

```javascript
import * as actionTypes from "../../actionTypes";
import { generateThunk } from "../actionHelpers";
import { putMoveQuestions } from "../../../services/sort.service";
import { updateQuestionOrderInSection } from "../../actions/section.actions";
import { serial } from "../../../utils/Promise";

// Action creators that dispatch actions and async actions from other action creators
// Ref: https://github.com/reduxjs/redux-thunk
function requestQuestionOrderChange(formId, newQuestionOrder) {
  // First level: Returns a "thunk"
  // When this "thunk" function is passed to `dispatch`, the thunk middleware will intercept it, and call it with `dispatch` /and `getState` as arguments.
  return dispatch => {
    // Second level: [promiseA] -> a new promise is returned. Allows requestQuestionOrderChange to be "thenable" from where it is executed.
    return new Promise(resolve => {
      // Get a dispatch in before the main action begins.
      dispatch({
        type: actionTypes.QUESTION_ORDER_CHANGE_REQUEST
      });
      // Third level: putMoveQuestions is a thin wrapper around a fetch. So it is another promise. The ".then" is where the main action of requestQuestionOrderChange happens.
      putMoveQuestions(formId, newQuestionOrder)
        .then(response => {
          // If putMoveQuestions does not return a response goto error state.
          if (response) {
            // Generally this is where other "thunks" wrapped in promises are triggered sequentially.
            // Create a promise array of all expected actions
            const promiseArray = Object.entries(newQuestionOrder).map(section =>
              generateThunk(
                dispatch,
                updateQuestionOrderInSection,
                section[0],
                section[1].questionIds
              )
            );
            // Now execute all promisified actions in sequence and it returns as a promise!
            return serial(promiseArray);
          } else {
            throw new Error("Failed to move question");
          }
        })
        .then(() => {
          // Get a dispatch in to show the request was successful.
          dispatch({
            type: actionTypes.QUESTION_ORDER_CHANGE_SUCCESS
          });
          // Finally resolve [promiseA] which will trigger ".then" in the executing component and the cleanup steps can then happen in the UI.
          resolve({ message: "Question moved successfully" });
        })
        .catch(error => {
          dispatch({
            type: actionTypes.QUESTION_ORDER_CHANGE_FAILURE,
            payload: error,
            error: true
          });
        });
    });
  };
}

export default requestQuestionOrderChange;

```

## Example of usage within component

This is an example of using `requestQuestionOrderChange` within a component. It is passed in using [`mapDispatchToProps`](mapDispatchToProps) from React Redux. In the app I am working on, it displays a success message, closes a dialog etc.

```javascript
//---- Within a component
function handleSubmit(event) {
    requestQuestionOrderChange(formId, newQuestionOrder).then(
      ({ message }) => {
        // After order has changed successfully
        //  Update UI etc.
      }
    );
}
//----
```

## Possible improvement: await/async

There is a planned refactor to change some of this syntax to the new await/async expressions. I believe it will reduce the complexity and make the process more understandable to a new developer working on the project. I will write that up when it happens.

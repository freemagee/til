---
title: "Update state from props in React"
date: 2019-01-08T11:11:05+00:00
categories:
  - development
tags:
  - react
---

Today I learned how to use a new(ish) React lifecycle hook called `getDerivedStateFromProps` ([link](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#updating-state-based-on-props)). I have struggled a little bit with form components that allow you to edit data. In my example below it is "user" data, but in reality this could affect all forms with any data in React.

## Overview

The reason for the struggle is a slight conflict for the one true source of the data for the form. When the "user" is edited, and the **user form component** is used, data is fed into it via props from it's *parent component*. So the Employer and Job title current values can appear in their relevant inputs.

That would be ok if that data was static, but the data is not and it needs to be editable. So the props are copied into the **user form component** state. And this state is used as the value of the relevant inputs. That allows the inputs to receive new data, which triggers off a chain of events. 

The new data is processed via a `handleChange` event in the **user form component**. Two things happen, it is added to the current state and also passed to a *parent component* for other actions to happen. When that *parent component* is done, it sends that data back to this **user form component** as props and the cycle can continue.
<!--more-->

## The Problem

I have two buttons that provide control for this form in another component (that is probably bad architecture, but it made sense at the time), Update User and Cancel. Update will eventually save the data back to the database. But Cancel will hide **user form component** and reset the user data back to it's original form in the *parent component*. This is where the problem can happen. Because **user form component** has it's own state, if a user edits the data, then cancels, then re-edits the data the **previous** edit would be visible.

I tried a combination of `shouldComponentUpdate` with `componentDidUpdate` to force **user form component** to change it's state based on newly changed parent props. But each time I had a bug. Either the component stopped rendering or I got infinite loops.

This probably means I don't fully understand what is happening and/or my app architecture has problems. I accept that. More to learn.

## The Solution

So for me, `getDerivedStateFromProps` is saying "if the new props do not match the state then output a new state". It does seem like the [React documentation](https://reactjs.org/docs/react-component.html#static-getderivedstatefromprops) is kind of recommending against this:

> If you want to “reset” some state when a prop changes, consider either making a component fully controlled or fully uncontrolled with a key instead.

But it works. For now! I will explore the React docs suggestions for a better, more maintainable solution.

## User Form Component - Complete

```javascript
import React from "react";

export default class User extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      employer: this.props.user.employer,
      jobTitle: this.props.user.jobTitle
    };

    this.handleChange = this.handleChange.bind(this);
  }
  static getDerivedStateFromProps(nextProps, nextState) {
    if (
      nextProps.user.employer !== nextState.employer ||
      nextProps.user.jobTitle !== nextState.jobTitle
    ) {
      // The props have changed. So update state accordingly.
      return {
        employer: nextProps.user.employer,
        jobTitle: nextProps.user.jobTitle
      };
    }

    // Return null to indicate no change to state.
    return null;
  }
  handleChange(event) {
    const target = event.target;
    const value = target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });

    // The following update the data in a parent component, which will eventually come back to this component via props.
    if (name === "employer") {
      this.props.onEmployerUpdate(value);
    }

    if (name === "jobTitle") {
      this.props.onJobTitleUpdate(value);
    }
  }
  render() {
    const employerClass =
      this.props.user.employer !== ""
        ? "User__employer"
        : "User__employer is-invalid";
    const jobTitleClass =
      this.props.user.jobTitle !== ""
        ? "User__jobTitle"
        : "User__jobTitle is-invalid";

    if (this.props.appMode === "edit") {
      return (
        <div className="User">
          <div className="User__control">
            <input
              className={employerClass}
              type="text"
              value={this.state.employer}
              name="title"
              onChange={this.handleChange}
            />
          </div>
          <div className="User__control">
            <input
              className={jobTitleClass}
              type="text"
              value={this.state.jobTitle}
              name="content"
              onChange={this.handleChange}
            />
          </div>
        </div>
      );
    } else {
      return <div className="User is-hidden" />;
    }
  }
}

```

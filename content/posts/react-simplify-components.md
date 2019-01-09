---
employer: "Simplify React components"
date: 2019-01-09T17:04:41+00:00
categories:
  - development
tags:
  - react
---

Today I learned a bit more about why setting props against state is considered an anti-pattern. After my [previous article](https://til.neilmagee.com/posts/react-update-state-from-props/), I was not happy with the component. It still felt like `getDerivedStateFromProps` was a hack rather than a maintainable fix. Plus in using the component, I was still triggering bugs that were quite situational.

I read more on [the react blog](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recap) and the recap really nailed what I needed to do.

> For example, rather than a child accepting a “committed” props.value and tracking a “draft” state.value, have the parent manage both state.draftValue and state.committedValue and control the child’s value directly.

This accurately described a process, to make my component simpler (I think it qualifies as a controlled component now), that would remove side effects. So I set out to remove state from this component and make it's parent responsible for passing in the correct data as props.
<!--more-->

## User Form Component - Before

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
              name="employer"
              onChange={this.handleChange}
            />
          </div>
          <div className="User__control">
            <input
              className={jobTitleClass}
              type="text"
              value={this.state.jobTitle}
              name="jobTitle"
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

The before version used set `props` against `state`. It then handled input from the user and used `getDerivedStateFromProps` to watch for changes to the props. I ran into multiple warnings from the React debugger whilst using this component as I changed other components in my app.

## User Form Component - After Refactor

```javascript
import React from "react";

export default class User extends React.Component {
  constructor(props) {
    super(props);
    // Look, no state!
    this.handleChange = this.handleChange.bind(this);
  }
  handleChange(event) {
    const target = event.target;
    const value = target.value;
    const name = target.name;
    // Record the current props data
    const stateOfValues = {
      employer: this.props.user.employer,
      jobTitle: this.props.user.jobTitle
    };
    // Record the newly changed value
    const newValue = {
      [name]: value
    };
    // Use spread operator for object merge.
    // In the case of a key collision, the right-most (last) object's value wins out.
    const merged = { ...stateOfValues, ...newValue };

    this.props.onDraftChange(merged);
  }
  render() {
    // Initially the incoming user props can be undefined, so handle that.
    const employer =
      typeof this.props.user.employer !== "undefined" ? this.props.user.employer : "";
    const jobTitle =
      typeof this.props.user.jobTitle !== "undefined"
        ? this.props.user.jobTitle
        : "";
    // TODO: The invalid classes are applied on a new user straight away, they should wait until a 'user' has started to input data/ or when they click an action
    const employerClass =
      employer !== ""
        ? "User__employer"
        : "User__employer isInvalid";
    const jobTitleClass =
      jobTitle !== ""
        ? "User__jobTitle"
        : "User__jobTitle isInvalid";

    return (
      <div className="User">
        <div className="User__control">
          <input
            className={employerClass}
            type="text"
            value={employer}
            name="employer"
            placeholder="Please enter a employer"
            onChange={this.handleChange}
          />
        </div>
        <div className="User__control">
          <input
            className={jobTitleClass}
            type="text"
            name="jobTitle"
            value={jobTitle}
            placeholder="Please enter a job title"
            onChange={this.handleChange}
          />
        </div>
      </div>
    );
  }
}
```

So one of the main differences is the method `handleChange`. As a user changes data, it triggers `handleChange`. Before that was where I updated the internal `state`, and then pushed the new values out to the parent component. But now I take a record of the current `props`, take the newly edited value and merge them into a new object. That object is pushed to the parent component. Various other steps are taken in the parent, but eventually the newly changed data comes back into the `user form component` as `props` and the value in the input is changed.

I hope I have got this pattern right this time...if not the knowledge is serving me well.
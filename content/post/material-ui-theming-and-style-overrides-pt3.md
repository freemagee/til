---
title: "Material UI theming and style overrides - Part 3"
date: 2019-05-31T14:54:14+01:00
description: "How to theme and style Material UI components in a React app - Part 3"
categories:
  - development
tags:
  - react
  - material-ui
---

Today I learned more about sharing styles across React components. Although CSS-in-JS is new to me, it is pretty easy to pick up (*Although I have not formed a final opinion on it yet...*). I think Material UI uses [React JSS](https://github.com/cssinjs/react-jss) under the hood, so a style can be written as an object:

```javascript
import React from 'react';
import { makeStyles } from '@material-ui/styles';

const useStyles = makeStyles({
  root: {
    backgroundColor: 'red',
  },
});

export default function MyComponent() {
  const classes = useStyles();
  return <div className={classes.root} />;
}
```

## Refactor to be more DRY

I had three components that were pretty similar to each other, with enough unique parts to keep them seperate. But they all had a style object in them. I could see the **style object was about 90% duplicated** between each component. So after using a diff tool to see what the differences were, I extracted the shared styles out into a new file called `Styles.js`. That looks a little like this:
<!--more-->
```javascript
// Provide access to my Material UI theme
import CustomTheme from "../../Theme";

const Styles = {
  title: {
    "& .MuiFormLabel-root": {
      fontSize: CustomTheme.typography.pxToRem(24)
    },
    "& .MuiInputBase-input": {
      fontSize: CustomTheme.typography.pxToRem(24)
    },
    "& .MuiInputLabel-asterisk": {
      color: CustomTheme.palette.error.main
    }
  },
  paper: {
    marginTop: CustomTheme.spacing(4),
    paddingTop: CustomTheme.spacing(4),
    paddingLeft: CustomTheme.spacing(4),
    paddingRight: CustomTheme.spacing(4),
    borderLeft: `3px solid transparent`
  },
  paperActive: {
    boxShadow: CustomTheme.shadows[7],
    borderLeft: `3px solid ${CustomTheme.palette.primary.main}`
  },
  input: {
    marginTop: CustomTheme.spacing(1),
    marginBottom: CustomTheme.spacing(1),
    "& .MuiInputBase-input": {
      fontSize: CustomTheme.typography.pxToRem(14)
    }
  },
  button: {
    marginLeft: CustomTheme.spacing(1),
    marginRight: CustomTheme.spacing(1)
  },
  footer: {
    display: "flex",
    justifyContent: "flex-end",
    marginTop: CustomTheme.spacing(6),
    padding: CustomTheme.spacing(2),
    borderTop: `1px solid ${CustomTheme.palette.grey[300]}`
  },
  vertDivider: {
    width: 1,
    height: 48
  }
};

export default Styles;
```
### A little bit of spread

I can then import that file into my component and combine the shared style object (using [ES6 spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Spread_in_object_literals)) with some component specific styles like this:

```javascript
// React & Material UI
import React from "react";
import PropTypes from "prop-types";
import { makeStyles } from "@material-ui/core/styles";
// Material UI components
import Paper from "@material-ui/core/Paper";
import TextField from "@material-ui/core/TextField";
import Input from "@material-ui/core/Input";
// App custom
import CustomStyles from "./Shared/Styles";

// Combine both objects using spread
const componentStyleOverrides = {
  ...CustomStyles,
  input: {
    width: "40%"
  }
};
const useStyles = makeStyles(componentStyleOverrides);

function ShortAnswer(props) {
  // ...
  // Additonal functionaliy removed for brevity
  // ...

  return (
    <Paper
      className={
        hasFocus === true
          ? [classes.paper, classes.paperActive].join(" ")
          : classes.paper
      }
      onClick={handleFocus}
    >
      <form className={classes.form} noValidate>
        <TextField
          value={props.question}
          id="question"
          classes={{ root: classes.title }}
          label="Question"
          onChange={handleChange("question")}
          required
          fullWidth
          autoFocus
        />
        <TextField
          value={props.description}
          id="description"
          label="Description (optional)"
          onChange={handleChange("description")}
          margin="normal"
          fullWidth
        />
        <Input
          value="Short answer text"
          className={classes.input}
          disabled
        />
      </form>
    </Paper>
  );
}

export default ShortAnswer;
```

## Conclusion

My components now have a common style, which is good. But I still need to assess what the advantages to this method are to me and my team. I am knowledgeable enough with regular CSS to know how to [modularise it](https://css-tricks.com/css-modules-part-1-need/) and keep the [scope of styles targeted](http://getbem.com/introduction/). I like how CSS-in-JS can take advantage of functions and logic, but a lot of that could be achieved via SCSS or PostCSS plugins. I also need to assess the overall architecture of this app, as other components will have a seperate style object of their own.
---
title: "Material UI theming and style overrides - Part 5"
date: 2019-08-05T11:54:29+01:00
description: "How to theme and style Material UI components in a React app - Part 5"
categories:
  - development
tags:
  - react
  - material-ui
---

Today I learned that sometimes a **refactor** is not always better, just *different*. I followed through with my plan to split my styles out into individual modules:

```javascript
// Example styles module 'body.js'
import CustomTheme from "../../Theme";

export default {
  formBuilder: {
    paddingBottom: CustomTheme.spacing(8)
  },
  formBuilderContainer: {
    maxWidth: "770px",
    padding: 0
  }
};
```

That module sits in a `/Styles` folder, at the same level as my container component, and is exposed via an `index.js` file like so:

```javascript
export { default as body } from "./body";
```
<!--more-->
Then the components that require those styles can access them via:

```javascript
// Material UI
import { makeStyles } from "@material-ui/core/styles";
// App custom
import { body } from "./Styles/";

const useStyles = makeStyles({ ...body });

const FormBuilder = () => {
  const classes = useStyles();

  return (
    <Container className={classes.formBuilderContainer} maxWidth={false}>
      <p>Content...</p>
    </Container>
  )

};

export default FormBuilder;
```

That covers the basics of what I did. I do not feel like it was a **big** win, but maybe a *small* one. I now have seperate style modules. Which is good. But I have not fixed the namespace issues I mentioned in my [previous post](https://til.neilmagee.com/post/material-ui-theming-and-style-overrides-pt4/#namespace-issues). 

## Namespace issues&hellip;again

I believed that I would get to a point where my style modules would be similar to this:

```javascript
// form.js
const form = {
  header: {
    // ... styles
  }
};

export default form;
```
```javascript
// section.js
const section = {
  header: {
    // ... styles
  }
};

export default section;
```

And that a component that needs access to `form` and `section` styles could import both modules and would eventually access the styles via syntax such as `className={classes.form.header}`. It turns out my assumption was wrong. Material UI's `makeStyles` API takes an object and generates class names from it. So to feed an object to `makeStyles` you could do:

```javascript
const useStyles = makeStyles({ ...form, ...section });
// or
const useStyles = makeStyles(Object.assign({}, form, section));
```

That would mean the `header` property from `section` would overwrite the same property in `form` and the unique style would no longer be available.

So namespacing the property names is still important and for now, it seems like the style object would have to follow this pattern:

```javascript
// form.js
const form = {
  formHeader: {
    // ... styles
  }
};

export default form;
```
```javascript
// section.js
const section = {
  sectionHeader: {
    // ... styles
  }
};

export default section;
```

If I find out a different way, I am sure to make a note of it here!
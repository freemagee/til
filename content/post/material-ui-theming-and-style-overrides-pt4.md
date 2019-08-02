---
title: "Material UI theming and style overrides - Part 4"
date: 2019-08-02T14:49:47+01:00
categories:
  - development
tags:
  - react
  - material-ui
---

Today I learned I still don't have a good answer when it comes to managing **CSS-in-JS**. [Last time](https://til.neilmagee.com/post/material-ui-theming-and-style-overrides-pt3/) I discussed this topic I had decided to make a single `Styles.js` object that contained style overrides to Material UI. I consolidated all my components styles into that `Styles.js` file. Abbreviated example below: 

```javascript
import CustomTheme from "../Theme";

const Styles = {
  formPreviewContainer: {
    maxWidth: "770px",
    paddingTop: CustomTheme.spacing(8),
    paddingBottom: CustomTheme.spacing(8)
  },
  formHeader: {
    position: "relative",
    paddingTop: CustomTheme.spacing(4),
    paddingBottom: CustomTheme.spacing(4),
    paddingLeft: CustomTheme.spacing(4),
    paddingRight: CustomTheme.spacing(8),
    marginBottom: CustomTheme.spacing(4),
    backgroundColor: CustomTheme.palette.primary.main
  },
  // ... other styles omitted for brevity ...
  textInput: {
    "& .MuiInput-underline:before": {
      borderBottom: `1px solid ${CustomTheme.palette.grey[300]}`
    },
    [CustomTheme.breakpoints.down("sm")]: {
      width: "100%"
    },
    [CustomTheme.breakpoints.up("md")]: {
      width: "50%"
    }
  },
  textArea: {
    width: "100%",
    "& .MuiInputBase-input": {
      lineHeight: "24px"
    },
    "& .MuiInput-underline:before": {
      borderBottom: `1px solid ${CustomTheme.palette.grey[300]}`
    },
  }
};

export default Styles;

```
<!--more-->
It worked, but it still did not feel right. It means the structure of practically every component in my app always includes these lines of code:

```javascript
import { makeStyles } from '@material-ui/styles';
import Styles from "./Styles";

const useStyles = makeStyles(Styles);

export default function WidgetA() {
  const classes = useStyles();

}
```

That means that **WidgetA** will import all styles from `Styles.js`. Although this isn't an immediate problem as the *CSS-in-JS* engine inside Material-UI ignores the styles it does not need. But as more styles are added to `Styles.js` it is becoming similar to using a single `Styles.css` file to style a complex app. Which is **not** something I would do and I consider that to be a regression. That is the reason I love `SCSS` partials.

## Split it up!

Today I am beginning to break up the `Styles.js` file. First into smaller objects that all get exported like so:

```javascript
import CustomTheme from "../Theme";

const form = {
  formPreviewContainer: {
    maxWidth: "770px",
    paddingTop: CustomTheme.spacing(8),
    paddingBottom: CustomTheme.spacing(8)
  },
  formHeader: {
    position: "relative",
    paddingTop: CustomTheme.spacing(4),
    paddingBottom: CustomTheme.spacing(4),
    paddingLeft: CustomTheme.spacing(4),
    paddingRight: CustomTheme.spacing(8),
    marginBottom: CustomTheme.spacing(4),
    backgroundColor: CustomTheme.palette.primary.main
  }
};

const inputs = {
  textInput: {
    "& .MuiInput-underline:before": {
      borderBottom: `1px solid ${CustomTheme.palette.grey[300]}`
    },
    [CustomTheme.breakpoints.down("sm")]: {
      width: "100%"
    },
    [CustomTheme.breakpoints.up("md")]: {
      width: "50%"
    }
  },
  textArea: {
    width: "100%",
    "& .MuiInputBase-input": {
      lineHeight: "24px"
    },
    "& .MuiInput-underline:before": {
      borderBottom: `1px solid ${CustomTheme.palette.grey[300]}`
    },
  }
};

const Styles = {
  ...form,
  ...inputs
};

export default Styles;

```

I am using the spread operator to clone the seperate objects into a combined `Styles` object. That means there are no breaking changes to the current code base&hellip;*yet*. I plan to now split those objects into seperate files and expose them as modules. Components can then import the styles they need.

## Namespace issues

Namespace issues have begun to be a problem as well, and eventually splitting the styles out should solve that problem. But it will also mean that each component will need its `className` references adjusted.

### Old example

Everything has to be prefixed to make the object keys unique and understandable.

```javascript
const Styles = {
  formHeader: {
    // ... styles
  },
  sectionHeader: {
    // ... styles
  },
  questionHeader: {
    // ... styles
  },
  myOtherWidgetHeader: {
    // ... styles
  }
};
```

### New example
```javascript
const form = {
  header: {
    // ... styles
  }
};

const section = {
  header: {
    // ... styles
  }
};

const question = {
  header: {
    // ... styles
  }
};

const myOtherWidget = {
  myOtherWidgetHeader: {
    // ... styles
  }
};
```

### Future example
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
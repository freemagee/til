---
title: "Controlling Tailwind"
date: 2019-08-08T14:49:02+01:00
categories:
  - development
tags:
  - css
---

Today I learned how to control [Tailwind](https://tailwindcss.com). I am planning on using **Tailwind** on an upcoming project and in order to put something new like this onto a production website, I need to understand it deeply.

## Tailwind out of the box

```css
/* input.css */
@tailwind base;

@tailwind components;

@tailwind utilities;
```

```javascript
// tailwind.config.js
module.exports = {
  theme: {},
  variants: {},
  plugins: []
};
```

```bash
# command
tailwind build input.css -o output.css
```

Putting the above together and running the command will output a `css` file that is **54309** lines of code and **854.59KB** in file size! The developers  talk about this on the [official site](https://tailwindcss.com/docs/controlling-file-size) and it is not the recommended way to use Tailwind. What I needed to know was what config settings could create a very minimal output that I could add to as I needed it.
<!--more-->

### Extreme - output no styles

```javascript
// tailwind.config.js
module.exports = {
  corePlugins: [],
  theme: {},
  variants: {},
  plugins: []
};
```

The above config will output an empty `css` file.

```bash
tailwindcss 1.1.0

Building... input.css

Finished in 114 ms
Size: 0B
Saved to output.css
```

Good to know!

## A more usable config

```javascript
// tailwind.config.js
const colorOverrides = {
  red: "#de3618"
};
const customColors = {
  "dodger-blue": "#22a7f0"
};

module.exports = {
  corePlugins: ["container", "margin", "padding", "textColor"],
  theme: {
    colors: colorOverrides,
    screens: {
      sm: "420px",
      md: "600px"
    },
    fontFamily: {},
    borderWidth: {},
    spacing: {},
    opacity: {},
    extend: {
      colors: customColors,
      spacing: {
        gap: "20px"
      }
    }
  },
  variants: {
    container: ["responsive"],
    margin: [],
    padding: [],
    textColor: []
  },
  plugins: []
};
```

This config is contrived, but it is beginning to show the control the config has over the output `css`. It has default colour overrides, custom colours, white listing of Tailwind corePlugins, a custom spacing value, breakpoint overrides and responsive classes only for the `container` corePlugin.

More importantly, I have full control of the output `css`. Running the build command results in the output below. Knowing all this gives me the confidence to use this on a production site.

## Example output

```
tailwindcss 1.1.0

Building... input.css

Finished in 157 ms
Size: 1.33KB
Saved to output.css
```

```css
/* output.css */
.container {
  width: 100%
}

@media (min-width: 420px) {
  .container {
    max-width: 420px
  }
}

@media (min-width: 600px) {
  .container {
    max-width: 600px
  }
}

.m-auto {
  margin: auto
}

.m-gap {
  margin: 20px
}

.-m-gap {
  margin: -20px
}

.my-auto {
  margin-top: auto;
  margin-bottom: auto
}

.mx-auto {
  margin-left: auto;
  margin-right: auto
}

.my-gap {
  margin-top: 20px;
  margin-bottom: 20px
}

.mx-gap {
  margin-left: 20px;
  margin-right: 20px
}

.-my-gap {
  margin-top: -20px;
  margin-bottom: -20px
}

.-mx-gap {
  margin-left: -20px;
  margin-right: -20px
}

.mt-auto {
  margin-top: auto
}

.mr-auto {
  margin-right: auto
}

.mb-auto {
  margin-bottom: auto
}

.ml-auto {
  margin-left: auto
}

.mt-gap {
  margin-top: 20px
}

.mr-gap {
  margin-right: 20px
}

.mb-gap {
  margin-bottom: 20px
}

.ml-gap {
  margin-left: 20px
}

.-mt-gap {
  margin-top: -20px
}

.-mr-gap {
  margin-right: -20px
}

.-mb-gap {
  margin-bottom: -20px
}

.-ml-gap {
  margin-left: -20px
}

.p-gap {
  padding: 20px
}

.py-gap {
  padding-top: 20px;
  padding-bottom: 20px
}

.px-gap {
  padding-left: 20px;
  padding-right: 20px
}

.pt-gap {
  padding-top: 20px
}

.pr-gap {
  padding-right: 20px
}

.pb-gap {
  padding-bottom: 20px
}

.pl-gap {
  padding-left: 20px
}

.text-red {
  color: #de3618
}

.text-dodger-blue {
  color: #22a7f0
}
```


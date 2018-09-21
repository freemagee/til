---
title: "Code Examples"
date: 2018-07-30T09:42:00+01:00
categories:
  - code
  - misc
tags:
  - php
  - javascript
---

Here are some code samples

```php
<?php phpinfo() ?>
```

And some more

```javascript
console.log("I am JavaScript");
```

Very pretty, now use some proper syntax highlighting

{{< highlight go "linenos=table,hl_lines=8 15-17,linenostart=1" >}}
module.exports = {
  "parserOptions": {
    "ecmaVersion": 6
  },
  "env": {
    "browser": true,
    "node": true
  },
  "extends": [
    "airbnb-base",
    "prettier"
  ],
  "plugins": [
    "import",
    "prettier"
  ],
  "rules": {
    "prettier/prettier": "error",
    "linebreak-style": 0,
    "no-use-before-define": ["error", { "functions": false, "classes": false }],
    "no-plusplus": ["error", { "allowForLoopAfterthoughts": true }],
    "no-restricted-imports": ["error", "fs"]
  },
  "globals": {}
};
{{< / highlight >}}
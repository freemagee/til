---
title: "Resolve eslint and prettier conflicts"
date: 2019-02-12T10:07:40+00:00
description: "How to resolve eslint and prettier config conflicts"
categories:
  - development
tags:
  - eslint
  - vue
---

Today I learned about a handy utility called `eslint-config-prettier-check` which I saw referenced in this GitHub [issue](https://github.com/prettier/prettier/issues/2609).

I was editing a `.vue` component in Visual Studio Code. I had a code section with red underlines, generally indicating that eslint was not happy with something. I could see it was a formatting issue, so I used `eslint --fix` to solve the issue and re-format the code. But then that triggered a new lint problem where *Prettier* was now unhappy with how *eslint* had formatted the code. The difference between the two is small, and involves a couple of indented spaces. See the images below for both examples.

{{< figure src="/images/eslint-and-prettier-conflicts/eslint-formatted.png" title="eslint Formatted" alt="Example of code formatted with eslint" >}}

{{< figure src="/images/eslint-and-prettier-conflicts/prettier-formatted.png" title="Prettier Formatted" alt="Example of code formatted with Prettier" >}}
<!--more-->

As a person bothered by inconsistencies like this, I started with a search (*eslint and prettier conflicting over indentation of ternary operator at [DuckDuckGo](https://duckduckgo.com/?q=eslint+and+prettier+conflicting+over+indentation+of+ternary+operator)*) which led me to the GitHub issue. In that issue, they mention `eslint-config-prettier-check`. Seems like this is a CLI helper tool installed with [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier/blob/0b04c0d16860e5dba62e30898046078b492aca3a/README.md#cli-helper-tool). After following the advice from the issue, I ran this command on Windows, in the root of the project:

```
node_modules\.bin\eslint.cmd --print-config .eslintrc.js | node_modules\.bin\eslint-config-prettier-check.cmd
```

Which provided the information:

```
The following rules are unnecessary or might conflict with Prettier:

- indent
```

Looking at my `.eslintrc.js` for this project, I removed the rule `indent: ["error", 2, { SwitchCase: 1 }]`. After running `eslint --fix` once more, the code was re-indented, the warnings went away and all is right once again. The final code, below, is formatted in the *Prettier* way. I do need to go check some `switch` code once more to make sure that is also formatting as expected.

{{< figure src="/images/eslint-and-prettier-conflicts/final-formatting.png" title="No lint errors, properly formatted!" alt="Final example of code formatted with eslint, with no lint errors" >}}
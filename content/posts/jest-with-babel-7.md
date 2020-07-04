---
title: "Get Jest working with Babel 7"
date: 2019-01-22T09:32:32+00:00
description: "How to get Jest to work with Babel 7 so you can use ES6 JavaScript features"
categories:
  - development
tags:
  - jest
---

Today I learned how to get [Jest](https://jestjs.io) to work with Babel 7. Often development involves tool configuration. I have lost many hours to this over the years. Something changes a version number and suddenly you are hunting through Stack Overflow and GitHub with a good idea of the problem, but lacking the `magic inscriptions` to solve it. Or there are version conflicts. Today's example is the latter.

I was writing a small tool for a maths problem, [Number difference table](https://github.com/freemagee/number-difference-table), and I thought it would benefit from having some unit tests written for it. I wanted to test some of the calculations and using the UI over and over, or console logging had become tedious.

So I chose to use [Jest](https://jestjs.io) and installed it.

```bash
yarn add -D jest
```
<!--more-->

I set up a test and imported my code and ran the test. I was met with

```bash
$ jest
 FAIL  tests/solveSequence.test.js
  ● Test suite failed to run

    Jest encountered an unexpected token

    --- LOG SHORTENED ---

    D:\dev\github\number-difference-table\tests\solveSequence.test.js:1
    ({"Object.<anonymous>":function(module,exports,require,__dirname,__filename,global,jest){import solveSequence from "solveSequence";

                   ^^^^^^

    SyntaxError: Unexpected token import

      at ScriptTransformer._transformAndBuildScript (node_modules/jest-runtime/build/script_transformer.js:403:17)

Test Suites: 1 failed, 1 total
Tests:       0 total
Snapshots:   0 total
Time:        5.823s
Ran all test suites.
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

I immediately could see this was an ES6 module import issue. My `solveSequence` module code was fine and looking at [Jest's docs](https://jestjs.io/docs/en/getting-started) they use a require statement in their examples.

So I Googled getting Jest to work with ES6 imports. This led me on a process of installing Babel, adding a Jest config to my package.json, adding a Babel config file (`.babelrc`) and installing a few other packages. This sadly led to more error messages!

This message:

```bash
$ jest
 FAIL  tests/solveSequence.test.js
  ● Test suite failed to run

    Requires Babel "^7.0.0-0", but was loaded with "6.26.3". If you are sure you have a compatible version of @babel/core, it is likely that something in your build process is loading the wrong version. Inspect the stack trace of this error to look for the first entry that doesn't mention "@babel/core" or "babel-core" to see what is calling Babel. (While processing preset: "D:\\dev\\github\\number-difference-table\\node_modules\\@babel\\preset-env\\lib\\index.js")
```

That gave me more of a clue what was happening. I ended up on a [GitHub issue](https://github.com/facebook/jest/issues/6913), and after reading it I got to a point where I removed my `node_modules` folder (I use `rimraf` for this). Removed `Jest` and any other packages I had installed and got back to my initial `package.json` config. I used `yarn install` to get my initial config installed once more. I then added `@babel/core`. After that I added `babel-core` using `7.0.0-bridge.0`. Finally `Jest` and `babel-jest` (not 100% sure `babel-jest` was necessary as some docs said it is installed as part of Jest). After `yarn` had done it's thing I ran my test. Bingo. The test was run successfully. No more error messages and I was getting results from my test.

My final `package.json` was:

```json
{
  "name": "number-difference-table",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "parcel index.html",
    "build": "rimraf dist/ && parcel build index.html --public-url https://freemagee.github.io/number-difference-table/ --no-source-maps",
    "deploy": "gh-pages -d dist",
    "test": "jest"
  },
  "license": "MIT",
  "dependencies": {
    "tachyons": "^4.11.1"
  },
  "devDependencies": {
    "@babel/cli": "^7.2.3",
    "@babel/core": "^7.2.2",
    "@babel/polyfill": "^7.2.5",
    "@babel/preset-env": "^7.2.3",
    "babel-core": "^7.0.0-bridge.0",
    "babel-jest": "^23.6.0",
    "eslint": "^5.12.0",
    "eslint-config-airbnb-base": "^13.1.0",
    "eslint-config-prettier": "^3.4.0",
    "eslint-plugin-import": "^2.14.0",
    "eslint-plugin-prettier": "^3.0.1",
    "gh-pages": "^2.0.1",
    "jest": "^23.6.0",
    "parcel-bundler": "^1.11.0",
    "prettier": "^1.15.3"
  },
  "browserslist": [
    ">0.2%",
    "not dead",
    "not ie <= 11",
    "not op_mini all"
  ],
  "jest": {
    "verbose": true,
    "moduleDirectories": [
      "node_modules",
      "src"
    ]
  },
  "babel": {
    "presets": [
      "@babel/preset-env"
    ]
  }
}
```

## Wrap up

So this led me to begin looking at [using babel](https://jestjs.io/docs/en/getting-started#using-babel) in the Jest docs. They state:

> Note: If you are using Babel version 7 then you need to install babel-jest, babel-core@^7.0.0-bridge.0 and @babel/core with the following command:

```bash
yarn add --dev babel-jest babel-core@^7.0.0-bridge.0 @babel/core regenerator-runtime
```

This may have been the solution all along. But I know that having added Babel to my project *after* I initially added Jest caused the problems I faced. Jest then want to use Babel 6 instead of 7 and thus the conflicts caused an error.
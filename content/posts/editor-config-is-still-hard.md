---
title: "Editor config is still hard"
date: 2018-11-14T11:16:00+00:00
description: "Vue, ESLint and Prettier config can still be hard to get right"
categories:
  - development
tags:
  - eslint
  - vue
---

Today I learned that configuring your code editor to lint and format code in a predictable way is still hard.

I use two editors frequently - Sublime Text and VS Code. Both are great. Both have plugins and their own settings. Today I was trying to update my environment for a Vue project in VS Code. I wanted the `.vue` files to be linted with ESLint, and for Prettier to handle the formatting. I was concentrating on how multiple attributes per line were being formatted on the `<template>` part.
<!--more-->

After following some advice on [Stack Overflow](https://stackoverflow.com/questions/52102705/cant-get-correct-autoformat-on-save-in-visual-studio-code-with-eslint-and-prett), which led me to an article on [Medium](https://medium.com/@doppelmutzi/eslint-prettier-vue-workflow-46a3cf54332f), an issue on [GitHub](https://github.com/vuejs/eslint-plugin-vue/issues/557) and a video by Wes Bos on [YouTube](https://youtu.be/YIvjKId9m2c). I got somewhere...but not where I want to be. I can not get this code:

```vue
<template>
  <div v-cloak id="app" class="w-100 mw8 center">
    <food-search
      :search-query="search.searchQuery"
      :search-branded="search.searchBranded"
      :loading="loading"
      @set-search-data="setSearchData"
    ></food-search>
    <search-results
      :food-list="foodList"
      :selected-id="selectedRawData.id"
      @retrieve="getDataForSelected"
    ></search-results>
    <nutrition-information
      :name="information.name"
      :nutrition="information.nutrition"
    ></nutrition-information>
  </div>
</template>
```

to format like this on save:

```vue
<template>
  <div
    v-cloak
    id="app"
    class="w-100 mw8 center"
  >
    <food-search
      :search-query="search.searchQuery"
      :search-branded="search.searchBranded"
      :loading="loading"
      @set-search-data="setSearchData"
    ></food-search>
    <search-results
      :food-list="foodList"
      :selected-id="selectedRawData.id"
      @retrieve="getDataForSelected"
    ></search-results>
    <nutrition-information
      :name="information.name"
      :nutrition="information.nutrition"
    ></nutrition-information>
  </div>
</template>
```

I will figure it out eventually, but this is often an element of development that can be overlooked - configuration and tooling.
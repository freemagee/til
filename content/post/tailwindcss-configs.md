---
title: "Tailwindcss configs"
date: 2020-01-23T10:02:46+00:00
description: "How to control Tailwindcss output file size using config"
categories:
  - development
tags:
  - css
---

Today I learned more about controlling [Tailwindcss](https://tailwindcss.com) output CSS. Based on my [previous post](https://til.neilmagee.com/post/controlling-tailwindcss/) I am now in a position to use Tailwindcss on a client project. That means I have been digging into the documentation and that has revealed to me a few more intricacies of the `tailwind.config.js`.

I started off by adding only the corePlugins I wanted to use to the config. But as that list grew it became clear that selectively disabling corePlugins was going to be simpler than adding the ones I wanted to use.

So I got a list of all the [corePlugins](https://tailwindcss.com/docs/configuration/#core-plugins), processed it in my text editor and used that to generate some boilerplate configs. Those configs are meant to be edited to taste and not used as they are.

I put this together in a new [GitHub repo](https://github.com/freemagee/tailwindcss-configs) with some explanations of the configs and examples of their output CSS.
<!--more-->
## Tailwindcss suggestions

The reason I am creating these configs is that I really want to control the filesize of the CSS and not include anything the client project is not going to use. There are already some areas of Tailwindcss that I feel could improve this and I may approach them with some suggestions.

One suggestion would be to seperate `positive` and `negative` into variants for `spacing`. When Tailwindcss generates margins it creates both positive and negative values such as:

```css
.m-1 {
  margin: 0.25rem;
}

.-m-1 {
  margin: -0.25rem;
}
```

I know the client project I am working on will not use negative margins, so it would be nice to disable them. This could be achieved by using the `variants` setting in the config:

```js
variants: {
  margin: ["positive"]
}
```

This could then only generate the positive margin values. This may be a good opportunity to contribute to some open source code!

**Update: 2020-01-28**

I did the easy thing and opened an [issue on negative margins on the Tailwindcss repo](https://github.com/tailwindcss/tailwindcss/issues/1332). Maybe it will end up in a useful change!
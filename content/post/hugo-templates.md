---
title: "Hugo template logic"
date: 2018-10-08T14:54:52+01:00
description: "Hugo theme tweaks and tricks"
categories:
  - development
tags:
  - hugo
---

Today I made some improvements to the [Hugo theme](https://github.com/freemagee/comfortable-basic) I created for this site. Adding some conditional statements to the templates to cater for various scenarios that don't fit how I intend to use the theme.

Although the theme is limited in scope, I want to eventually release it on [Hugo Themes](https://themes.gohugo.io/) and allow it to be flexible enough for others to use.

## Deal with no posts

On the `layouts/partials/pagination.html` template, it has been wrapped in

```golang
{{ if ne .TotalPages 0}}
  // Content
{{end}}
```
<!--more-->

So for the brief time that a new site contains no posts, there will not be weird pagination at the bottom listing "You are on page 0/1". I may move this logic to a parent template.

## Deal with intro content after first page

I have chosen to provide some intro content on my homepage, but once you use the pagination to move to page two or beyond, the intro is just taking up vertical space. It also could confuse a user as nothing visual changes between pages.

```golang
{{ if eq .Paginator.PageNumber 1 }}
  {{ if .Content }}
    // Content
  {{ end }}
{{ end }}
```

Pretty simple, if the page number does not equal 1, then do not show the intro. As I am new to `golang`, I was looking for a way to merge the two conditionals into one, but have not found that yet. The following:

```golang
{{ if eq .Paginator.PageNumber 1 && .Content }}
{{ end }}
```

Does not work. In other languages such as JavaScript I could express something similar using a logical operator:

```javascript
if (Paginator.PageNumber === 1 && Content !== "") {
  // Do something
}
```

I am sure I will find out why my `golang` syntax was not correct soon.

## Show/Hide about page on nav

If an about page does not exist, then it should not appear on the nav. Simple!

```golang
{{ with .Site.GetPage "/about" }}
  // nav link to about
{{end}}
```

I have used `with` here. It works. Maybe using an `if` would be better.
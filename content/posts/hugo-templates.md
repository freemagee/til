---
title: "Hugo Templates"
date: 2018-10-08T12:10:43+01:00
draft: true
categories:
  - code
  - hugo
tags:
  - golang
---

Today I made some improvements to the [Hugo theme](https://github.com/freemagee/comfortable-basic) I created for this site. Adding some conditional statements to the templates to cater for various scenarios.

Although the theme is limited in scope, I want to eventually release the theme on [Hugo Themes](https://themes.gohugo.io/) and have it be flexible enough for another user to use.

## Deal with no posts

On the `layouts/partials/pagination.html` template, it has been wrapped in

```golang
{{ if ne .TotalPages 0}}
  // Content
{{end}}
```
<!--more-->

So for the brief time that a new site contains no posts, there will not be weird pagination at the bottom listing "You are on page 0/1".

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

Does not work. In other languages such as JavaScript I could express something similar as:

```javascript
if (Paginator.PageNumber === 1 && Content !== "") {
  // Do something
}
```

I am sure I will look up the Go way of doing this soon.
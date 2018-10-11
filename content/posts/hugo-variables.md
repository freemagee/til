---
title: "Hugo Variables"
date: 2018-10-11T12:39:12+01:00
categories:
  - development
tags:
  - hugo
---

Today I learned that I do not know how to concatenate strings using `golang`.

My [Hugo theme](https://github.com/freemagee/comfortable-basic) has an 'about' page, which is supposed to have a conditional css class applied to the navigation when you are actually on that page. The default class is `site-header__nav-item` and that is meant to become `site-header__nav-item active` when visiting the 'about' page. This was not happening, so I have refactored the code a little and fixed the issue.

### The old code

```golang
<header class="site-header">
  <section class="site-header__ident">
    <a href="{{ .Site.BaseURL }}" class="site-header__link">{{- .Site.Title -}}</a>
  </section>
  <nav class="site-header__nav">
    {{ with .Site.GetPage "about" }}
    <a href="{{ .Site.BaseURL }}about/" class="site-header__nav-item {{ if eq .URL "/about/"}}active{{ end }}">About</a>
    {{ end }}
  </nav>
</header>
```
<!--more-->

### The new code

```golang
{{ $navStyle := "site-header__nav-item" }}
{{ if eq .RelPermalink "/about/" }}
  {{ $navStyle = "site-header__nav-item active" }}
{{ end }}
<header class="site-header">
  <section class="site-header__ident">
    <a href="{{ .Site.BaseURL }}" class="site-header__link">{{- .Site.Title -}}</a>
  </section>
  <nav class="site-header__nav">
  {{ with .Site.GetPage "about" }}
    <a href="{{ .Site.BaseURL }}about/" class="{{ $navStyle }}">About</a>
  {{ end }}
  </nav>
</header>
```

What I could not work out (whilst briefly reading the docs) was how to concatenate the string ` active` to the variable `$navStyle`. My current solution was to conditionally reassign the `$navStyle` variable. What I do not like about this method is the repetition it requires. I am only doing this once here, but if there were a few more pages, then this would not adhere to DRY principles. What I would have expected to do would be similar to this:

```golang
{{ $navStyle := "site-header__nav-item" }}
{{ if eq .RelPermalink "/about/" }}
  {{ $navStyle += " active" }}
{{ end }}
```

I am sure there is a way and I can write an update post in the future. Learning the grammar of a new language can be tough at first.
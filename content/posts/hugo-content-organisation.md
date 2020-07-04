---
title: "Hugo content organisation"
date: 2020-07-04T12:42:26.486Z
description: "How to organise content in your Hugo project"
categories:
  - development
tags:
  - hugo
---

Today I learned a lesson that Hugo definitely wants your content in the **content/posts** directory. I learnt this by needing to make an addition to my [Hugo theme](https://github.com/freemagee/comfortable-basic) that themes this site.

As I am now working from home, and have recently set my personal Mac up for local development, I used Homebrew to install Hugo. Previously I often used Hugo on my work PC. I then navigated to the local version of my TIL repo, ran `hugo server` and navigated to `localhost:1313`. What I saw was that my site was really broken.

{{< figure src="/images/hugo-content-organisation/localhost.png" title="Where are my posts?" alt="An image of my broken Hugo site" >}}
<!--more-->

Previously I kept all my markdown posts in **content/post**. I picked this up from either a Hugo theme tutorial, or maybe the Hugo docs. It worked. When I created a new post, I added it and ran `hugo --minify` locally to build the site. My homepage showed a nice paginated list of posts.

I guess the version of Hugo I was using before on my Work PC was a bit out of date. So the new version I installed on my Mac (0.73) wants the posts to be in **posts**! After I understood this I needed to make some tweaks to keep my site working how I wanted.

That's enough waffling. What did I do to fix this?

## First move the content

I simply renamed `content/post` to `content/posts`. Easy!

## Fix permalinks

I like the URL structure `{domain name}/post/{post title}`. I like the singular use of `post`. The URL structure was trying to be `{domain name}/posts/{post title}`. To achieve this, I added some config to my site `config.toml`. This prevented my posts from going to a 404.

```toml
[permalinks]
  posts = "/post/:title/"
```

## Fix pagination in my theme

This fix was not so obvious. I just looked at the pagination examples on [Hugos documentation](https://gohugo.io/templates/pagination/) for clues to try. I could see the reference to the type "post" was incorrect, but changing it to "posts" did not solve the problem.

**Before**

```html
{{ define "main" }}
{{ $paginator := .Paginate (where .Pages "Type" "post") }}
  {{ if eq $paginator.PageNumber 1 }}
    {{ if .Content }}
    <div class="site-intro">
      <header class="site-intro__header">
        <h1 class="site-intro__title">{{- .Title -}}</h1>
      </header>
      <div class="site-intro__content">{{- .Content -}}</div>
    </div>
    {{ end }}
  {{ end }}

  <div class="posts posts--home">
    {{ range $paginator.Pages }}
      {{ .Render "summary" }}
    {{ end }}
  </div>

  {{ partial "pagination.html" $paginator }}
{{ end }}
```

It seemed the line `{{ $paginator := .Paginate (where .Pages "Type" "post") }}` was causing the problem. Once I removed it and replaced references to `$paginator` with `Paginator` my pagination began working again, and all my posts began to show on the homepage as expected.

**After**

```html
{{ define "main" }}
  {{ if eq .Paginator.PageNumber 1 }}
    {{ if .Content }}
    <div class="site-intro">
      <header class="site-intro__header">
        <h1 class="site-intro__title">{{- .Title -}}</h1>
      </header>
      <div class="site-intro__content">{{- .Content -}}</div>
    </div>
    {{ end }}
  {{ end }}

  <div class="posts posts--home">
    {{ range .Paginator.Pages }}
      {{ .Render "summary" }}
    {{ end }}
  </div>

  {{ partial "pagination.html" .Paginator }}
{{ end }}
```

I am pleased I worked this out, as it can be hard when you jump back into a project that worked the last time you used it, only to find it is now broken.
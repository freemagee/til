---
title: "Hugo deploy"
date: 2018-10-10T16:31:28+01:00
description: "How to deploy your site with Go Hugo"
categories:
  - development
tags:
  - hugo
---

Today I had a lesson in [RTFM](https://en.wikipedia.org/wiki/RTFM). I noticed my Hugo `public/` folder had a bunch of orphaned content from a previous deploy that used dummy content. There is a clear reference to this in the [Hugo documentation](https://gohugo.io/getting-started/usage/#deploy-your-website).
<!--more-->

> Running *hugo* does not remove generated files before building. This means that you should delete your *public/* directory (or the publish directory you specified via flag or configuration file) before running the *hugo* command. If you do not remove these files, you run the risk of the wrong files (e.g., drafts or future posts) being left in the generated site.

A large part of being a developer is reading documentation. I obviously need to read more wisely.

After deleting `public/` and regenerating my content, all is good once more. I will need to build a delete step into my build process to avoid this in the future.
---
title: "Time for a change"
date: 2021-05-16T14:13:30+01:00
description: "Personal projects and optimising development time"
categories:
  - development
tags:
  - meta
---

I put this blog together at a time when I was learning modern React based web development and trying to better myself. This was largely driven by my personal interest, but I also saw React as a good career opportunity. Writing a personal blog helped in that process.

Fast-forward almost two years and I am working as a Software Developer, develping with React daily and learning TypeScript as well. This blog has sadly been put on the back-burner for quite a while and I am not happy about that.

I have limited personal development opportunity at the moment, being a father and working in a new-ish role in lockdown has meant personal projects have had to be put aside or even sunsetted.
<!--more-->

## Get to the point!

So in order to still work on personal projects I took a decision to try and simplify my development flow. Previously I ran my own Ubuntu LAMP instance on [Digital Ocean](https://www.digitalocean.com). I was quite happy with that for a while, and it was technology I was familiar with. But I no longer want to manage a server with the time I have available. So I shut it down. This had consequences! I hosted my personal website on there, plus a number of personal projects and I even hosted photos/files on there as well. I had to make some hard decisions about each.

[My personal website](https://www.neilmagee.com) - *LIVE* - This was a relatively simple static site (that needs some attention, but that is a seperate matter!) that I decided to put onto Netlify (for now). I was already hosting the source code on GitHub anyway, so it made sense. This is temporary, as I have plans to move this site (and this blog) to GitHub pages.

Hacker News Clone - *SUNSET* - I ran my own Hacker News (lite) clone. This was my oldest personal project and fulfilled a need I had at the time. It added a responsive re-skin to Hacker News and removed all functionality other than the links and the comments. I learnt a lot building it, and even re-built it over time. The last significant changes I made to it were replacing the front-end styles with [TailwindCSS](https://tailwindcss.com/) and replacing the PHP based cron job with PM2 and NodeJS. Sometimes I miss this project, especially if I have 5 mins to kill somewhere.

Streak - *SUNSET* - I built a React PWA habit tracker over the summer of 2020. It is an app that has a purpose for me, but I also used the development of it to become more familiar with React `useContext` and `useReducer` I enjoyed working on it, but ultimately I stopped developing it around September of 2020, when I began my new role. This is probably the main project I want to re-develop, and I would need to re-develop it as the backend ran on PHP with JSON files and was not great! I would rather run this on a complete JS stack and am still thinking about what tech I would use and where I could host it. I would need an API with a database, and my current hosting stack does not offer that.

Photos/File hosting - *REPLACED* - For now, I have fallen back to Google to fulfill the needs of sharing photos and/or files. I would prefer to control this myself, but I have no time to setup my own cloud service, so that is a limitation I accept.

### The future

I plan to move my static sites to GitHub pages and close my Netlify account. This simplifies my hosting and means I have one less account to manage. So that covers this blog and my personal website. I think leveraging free hosting with technology I am familiar with is win-win.

I need to find a back-end solution for APIs/Database/NodeJS that does not cost much per month (around £5 would be good). I do not have a good answer at the moment. I have been looking at Render/AWS and some other services. I need it to be super simple to setup, as again, time to spend on this is limited.

I am excited as I look forward to finding solutions and working in a more optimised manner. I am also looking forward to dumping PHP and working with NodeJS as a back-end technology. I know that once I find a stack that works, I can then begin to resurrect some sunsetted projects that I would be happy to work on again.
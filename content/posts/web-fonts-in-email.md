---
title: "Using web fonts in email campaigns"
date: 2019-06-27T16:01:37+01:00
description: "Use web fonts in HTML email campaigns for greater styling control"
categories:
  - development
tags:
  - email
---

Today I learned to keep following my *intuition*.

**TLDR** Use `woff`, not `woff2` for web fonts in email

I had been asked a question by our art director today about using web fonts in an email campaign that was being designed. The company we are designing for has a corporate identity that specifies certain font families. I had already begun to test this back in March so I had an idea of the state of web font support in email clients.

Before I answered him more definitively, I wanted to perform a few tests using our email marketing software &mdash; Campaign Monitor &mdash; to confirm what could be achieved.
<!--more-->

I built a few tests, combining different `css` techniques such as `@font-face`, `@import` and in-lining `css` styles. Then I ran them through the Campaign Monitor test suite. I got the best results from `@font-face`, embedding the `css` into the `html` email, and then finally getting Campaign Monitor to in-line the `css` styles when the email was being imported. After about 8 separate tests, I could see that Apple Mail and iOS were showing the web fonts, but other email clients that were supposed to (Outlook for Mac, Outlook App, Thunderbird) were not.

I found [this article on the Litmus blog](https://litmus.com/blog/the-ultimate-guide-to-web-fonts) that outlined what I already knew. But the interesting part to me was a link to an email campaign that used web fonts ([view the email](https://litmus.com/scope/a59ua3rogjrd)). After a brief bit of `html` extraction, I got that email into Campaign Monitor so I could see it tested with my own eyes. It worked as expected. I looked at the `html` and it looked very similar in concept to my own tests &mdash; it was using Google Fonts, it embedded the `@font-face` rules in a `style` tag in `head`, it in-lined it's styles on the elements. It did wrap it's `@font-face` in a media query. I tried that with no effect. I scratched my head a little, unsatisfied with not being able to see what the ~~problem~~ difference was. I looked closer &mdash; it came down to the number **2**&hellip;

### Reference email @font-face rule
```css
@font-face {
  font-family:'Montserrat';
  font-style:normal;
  font-weight:400;
  src:local('Montserrat-Regular'), url(https://fonts.gstatic.com/s/montserrat/v7/zhcz-_WihjSQC0oHJ9TCYL3hpw3pgy2gAi-Ip7WPMi0.woff) format('woff');
}
```

### My @font-face rule
```css
@font-face {
  font-family: 'Fira Sans';
  font-style: normal;
  font-weight: 400;
  src: local('Fira Sans Regular'), local('FiraSans-Regular'), url(https://fonts.gstatic.com/s/firasans/v9/va9E4kDNxMZdWfMOD5Vvl4jL.woff2) format('woff2');
}
```

Although we are using different font families, that was not the problem. The font **format** was. I was asking for `woff2`, but the reference email was asking for `woff`. The reason I was using `woff2` is I had gotten this `css` snippet by pasting `https://fonts.googleapis.com/css?family=Fira+Sans` into Chrome. Chrome supports `woff2`, behind the scenes Google served me the `woff2`. Email clients do not. I ended up using Internet Explorer 11 to get a new `css` snippet with the correct link to the `woff` file.

### The new, correct @font-face rule
```css
  @font-face {
    font-family: 'Fira Sans';
    font-style: normal;
    font-weight: 400;
    src: local('Fira Sans Regular'), local('FiraSans-Regular'), url(https://fonts.gstatic.com/s/firasans/v9/va9E4kDNxMZdWfMOD5Vvl4jN.woff) format('woff');
  }
```

This `css` snippet added support for Outlook on Mac, Thunderbird and Outlook on iOS (well on the iPhone 7&hellip;). If I had not kept digging and following that feeling of dissatisfaction, knowing that I was missing something, I would not have learnt more and given a weaker decision to the art director.
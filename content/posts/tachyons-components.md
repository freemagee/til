---
title: "Tachyons Components"
date: 2018-11-19T14:54:11Z
categories:
  - development
tags:
  - css
---

Today I learned a bit more about using [Tachyons](http://tachyons.io/) to make components. I read about Tachyons a long time ago and had a pretty common reaction to it which was to dismiss it outright. I come from a background of having learnt CSS when semantics were being preached. Carefully named classes were chosen for their meaning, and keeping styles out of HTML was important.

But my views have matured over time as I started to use BEM for CSS naming and realised the limitations of creating classes and how rubbish humans are at naming things. Especially when maintaining larger systems. I read [articles](https://hackernoon.com/full-re-write-with-tachyons-and-functional-css-a-case-study-part-1-635ccb5fb00b) about [utility classes](https://adamwathan.me/css-utility-classes-and-separation-of-concerns/) and slowly I began to agree with their perspective.
<!--more-->

Often before I use new technologies in actual client work, I will make demos or side projects ([Sugar Cubed](https://freemagee.github.io/sugar-cubed/)) using the tech I want to explore. I am considering refactoring one of the admin areas of a client project using Tachyons. I wanted to make a navigation component with simple drop down menus. I coded a demo on [CodePen](https://codepen.io/freemagee/pen/EOweRE) to see what that might look like:

### Tachyons version

```html
<nav class="flex justify-between ph3 bg-light-gray">
  <ul class="flex flex-shrink-0 list ma0 pa0">
    <li><a href="#" class="dib pv3 ph2 no-underline sans-serif dark-gray hover-bg-near-black hover-light-gray">Admin Home</a></li>
    <li class="relative">
      <a href="#" class="dib pv3 ph2 no-underline sans-serif dark-gray hover-bg-near-black hover-light-gray js-has-dropdown" aria-expanded="false">Events<span class="dib ml1 v-mid bw2 bt bt--dashed bt-1 br br--transparent br-1 bl bl--transparent bl-1 bb-0"></span></a>
      <ul class="dn absolute list bg-mid-gray pv3 ph2">
        <li><a href="#" class="db no-underline ph2 pv1 sans-serif light-gray hover-bg-near-white hover-dark-gray nowrap">Manage Events</a></li>
        <li><a href="#" class="db no-underline ph2 pv1 sans-serif light-gray hover-bg-near-white hover-dark-gray nowrap">Create New</a></li>
        <li><a href="#" class="db no-underline ph2 pv1 sans-serif light-gray hover-bg-near-white hover-dark-gray nowrap">Edit Existing / Duplicate</a></li>
        <li><a href="#" class="db no-underline ph2 pv1 sans-serif light-gray hover-bg-near-white hover-dark-gray nowrap">View Past</a></li>
      </ul>
    </li>
    <li class="relative">
      <a href="#" class="dib pv3 ph2 no-underline sans-serif dark-gray hover-bg-near-black hover-light-gray js-has-dropdown" aria-expanded="false">Users<span class="dib ml1 v-mid bw2 bt bt--dashed bt-1 br br--transparent br-1 bl bl--transparent bl-1 bb-0"></span></a>
      <ul class="dn absolute list bg-mid-gray pv3 ph2">
        <li><a href="#" class="db no-underline ph2 pv1 sans-serif light-gray hover-bg-near-white hover-dark-gray nowrap">Create User</a></li>
        <li><a href="#" class="db no-underline ph2 pv1 sans-serif light-gray hover-bg-near-white hover-dark-gray nowrap">Edit User</a></li>
      </ul>
    </li>
    <li><a href="#" class="dib pv3 ph2 no-underline sans-serif dark-gray hover-bg-near-black hover-light-gray">Manage Statuses</a></li>
    <li><a href="#" class="dib pv3 ph2 no-underline sans-serif dark-gray hover-bg-near-black hover-light-gray">Information</a></li>
  </ul>
  <ul class="flex list ma0 pa0">
    <li class="br b--light-silver">
      <strong class="dib pt3 pr0 pb3 pl2 sans-serif dark-gray">Logged in as:</strong>
      <span class="dib pt3 pr2 pb3 pl0 sans-serif i dark-gray">Jim Bob</span>
    </li>
    <li class="br b--light-silver">
      <a href="#" class="dib pv3 ph2 no-underline sans-serif dark-gray hover-bg-near-black hover-light-gray">Home</a>
    </li>
    <li>
      <a href="#" class="dib pv3 ph2 no-underline sans-serif dark-gray hover-bg-near-black hover-light-gray">Log out</a>
    </li>
  </ul>
</nav>
```

That forms the basis of the styling and the layout. There is some additional javascript needed to make the dropdowns work. Once you begin to understand the Tachyons naming system, it becomes more fluid. Tools such as [Tachyons TLDR](https://tachyons-tldr.now.sh/#/classes) help as well. Here is similar code from the actual project for this component (CSS not included):

### Non Tachyons version

```html
<nav class="site-container">
  <ul class="login-information-container">
    <li class="login-label">
      <strong>Logged in as:</strong>
      <span>Jim Bob</span>
    </li>
    <li class="seperator">
      <a href="#">Home</a>
    </li>
    <li class="seperator">
      <a href="#">Log out</a>
    </li>
  </ul>
  <ul class="main-menu-container">
    <li><a href="#">Admin Home</a></li>
    <li class="dropdown">
      <a href="#" class="dropdown-toggle" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Events<span class="caret"></span></a>
      <ul class="dropdown-menu">
        <li><a href="#">Manage Events</a></li>
        <li><a href="#">Create New</a></li>
        <li><a href="#">Edit Existing / Duplicate</a></li>
        <li><a href="#">View Past</a></li>
      </ul>
    </li>
    <li class="dropdown">
      <a href="#" class="dropdown-toggle" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Users<span class="caret"></span></a>
      <ul class="dropdown-menu">
        <li><a href="#">Create User</a></li>
        <li><a href="#">Edit Users</a></li>
      </ul>
    </li>
    <li><a href="#">Manage Statuses</a></li>
    <li><a href="#">Information</a></li>
  </ul>
</nav>
```

At the moment it is hard to judge the code in isolation. The Tachyons code is more verbose and looks more abstract, but I actually find it more readable as I can predict what it is going to look like from the Tachyons classes. There is a greater chance I could re-use this code in another Tachyons project. Whereas the non Tachyons version would need classes from multiple areas to be copied into a new project. The non Tachyons version might be easier to modify it's overall style, as changes could be made directly in the CSS. But when writing the Tachyons version, copy/pasting similar classes was not such a big deal after style changes.

I will have a more complete opinion after I have written a larger project in Tachyons or Tailwind...
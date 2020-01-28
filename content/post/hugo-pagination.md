---
title: "Hugo theme improved pagination"
date: 2019-07-18T17:01:21+01:00
description: "How to do pagination in your Hugo theme"
categories:
  - development
tags:
  - hugo
---

Today I learned how to do numbered pagination in my [Hugo theme](https://github.com/freemagee/comfortable-basic) using `golang`. This was a bit harder than I expected as I am no expert on programming with `golang`. But once I remembered that `golang` still follows the same programming conventions that other languages do, even though the syntax is different.

After the posts in my theme, there is a simple navigation area at the bottom. At the moment it looks like this:

{{< figure src="/images/improved-pagination/current-pagination.png" title="My current pagination" alt="An image of my Hugo themes current pagination" >}}
<!--more-->

It works fine, and let's you navigate through the posts linearly. But as this site gets more posts, I saw the need to be able to jump to a particular page. I looked for an existing theme that used pagination similarly to how I wanted. I found [Hugo Bootstrap](https://themes.gohugo.io/theme/hugo-bootstrap/) and went to look at the [pagination source code](https://github.com/Xzya/hugo-bootstrap/blob/master/layouts/partials/paginator.html). It took me a while to decipher it as the syntax is so different to PHP or JavaScript that I am more familiar with.

### The Hugo Bootstrap pagination source

```golang
{{ $pag := $.Paginator }}
{{ if gt $pag.TotalPages 1 }}
<nav class="blog-pagination">
    <ul class="pagination justify-content-center">
        {{ with $pag.First }}
        <li class="page-item">
            <a class="page-link" href="{{ .URL }}" aria-label="First"><span aria-hidden="true">&laquo;&laquo;</span></a>
        </li>
        {{ end }}
        <li class="page-item {{ if not $pag.HasPrev }}disabled{{ end }}">
            <a class="page-link" href="{{ if $pag.HasPrev }}{{ $pag.Prev.URL }}{{ end }}" aria-label="Previous"><span aria-hidden="true">&laquo;</span></a>
        </li>
        {{ $.Scratch.Set "__paginator.ellipsed" false }}
        {{ range $pag.Pagers }}
            {{ $right := sub .TotalPages .PageNumber }}
            {{ $showNumber := or (le .PageNumber 3) (eq $right 0) }}
            {{ $showNumber := or $showNumber (and (gt .PageNumber (sub $pag.PageNumber 2)) (lt .PageNumber (add $pag.PageNumber 2)))  }}
            {{ if $showNumber }}
                {{ $.Scratch.Set "__paginator.ellipsed" false }}
                {{ $.Scratch.Set "__paginator.shouldEllipse" false }}
            {{ else }}
                {{ $.Scratch.Set "__paginator.shouldEllipse" (not ($.Scratch.Get "__paginator.ellipsed") ) }}
                {{ $.Scratch.Set "__paginator.ellipsed" true }}
            {{ end }}
            {{ if $showNumber }}
                <li class="page-item {{ if eq . $pag }}active{{ end }}">
                    <a class="page-link" href="{{ .URL }}">{{ .PageNumber }}</a>
                </li>
            {{ else if ($.Scratch.Get "__paginator.shouldEllipse") }}
                <li class="page-item disabled">
                    <span class="page-link" aria-hidden="true">&hellip;</span>
                </li>
            {{ end }}
        {{ end }}
        <li class="page-item {{ if not $pag.HasNext }}disabled{{ end }}">
            <a class="page-link" href="{{ if $pag.HasNext }}{{ $pag.Next.URL }}{{ end }}" aria-label="Next"><span aria-hidden="true">&raquo;</span></a>
        </li>
        {{ with $pag.Last }}
            <li class="page-item">
                <a class="page-link" href="{{ .URL }}" aria-label="Last"><span aria-hidden="true">&raquo;&raquo;</span></a>
            </li>
        {{ end }}
    </ul>
</nav>
{{ end }}
```

This can be interpreted as:

```
if there are more pages than 1
  show first page link
  show previous page link
  loop over pages
    show a numbered page link
  end loop
  show next page link
  show last page link
end if
```

There is more going on, but this was enough to get me started. First thing I did was pasted the Hugo Bootstrap code into my partial to see what would happen. This caused Hugo to fall over and display some errors. After looking at the Hugo Bootstrap implementation I could see I was using the pagination feature of Hugo differently. In the Hugo implementation, the partial is responsible for generating a list of pages for the pagination partial. That is what `{{ $pag := $.Paginator }}` does on the first line. In my Hugo theme, I pass in the list of pages into my partial from it's parent (index.html). That meant instead of doing `$pag.TotalPages` I could simply do `.TotalPages` with the full stop acting a bit like `this` in JavaScript.

After removing references to `$pag` that got the first, previous, next and last links working. The Hugo debugger was still complaining about `$.Scratch.Set`. After looking up *Scratch* in the Hugo docs I tried to set `Scratch` as a local variable `{{ $scratch := newScratch }}`. Then I changed all references to `$.Scratch` to use `$scratch` instead. **Bingo**. The pagination rendered. I swapped the Hugo Bootstrap elements for my own and made some new CSS classes to style the new elements. That left my pagination looking like this:

{{< figure src="/images/improved-pagination/pagination-working.png" title="The new pagination" alt="An image of my Hugo themes new pagination" >}}

The final part for me to get working was setting the current page number as active in the pagination. I could see how it worked in the Hugo Bootstrap theme, they compared the *current page* against the *current item* in the `$pag.Pager` loop - `{{ if eq . $pag }}`. But because my theme is a little different, I could not initially get the current page number. I thought this might work for a second `{{ if eq . .PageNumber }}`. It didn't. It took a little while to understand how to get to the parent page variable. That's where I had a little *eureka* moment and set `{{ $thisPageNumber := .PageNumber }}` at the top of my pagination partial and then referenced it below `{{ if eq .PageNumber $thisPageNumber }}`. *Voila*, the active links now worked:

{{< figure src="/images/improved-pagination/active-page-working.png" title="Active links working" alt="An image of active page highlighted in the pagination" >}}

Now all the elements I need are in place, I can refine the design more. The pagination is obviously too wide at the moment and I have not seen the *ellipsis* feature in action yet. So there may be a post to come about that.

### My WIP pagination code

```golang
{{ $thisPageNumber := .PageNumber }}
{{ if ne .TotalPages 0}}
<nav class="pagination">
  <div class="pagination__ctrl">
    {{ with .First }}
    <a href="{{ .URL }}" class="pagination__nav pagination__nav--newer"><span class="pagination__nav-icon">
      <svg version="1.1" id="Chevron_left" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px"
      y="0px" viewBox="0 0 20 20" enable-background="new 0 0 20 20" xml:space="preserve">
      <path d="M12.452,4.516c0.446,0.436,0.481,1.043,0,1.576L8.705,10l3.747,3.908c0.481,0.533,0.446,1.141,0,1.574
      c-0.445,0.436-1.197,0.408-1.615,0c-0.418-0.406-4.502-4.695-4.502-4.695C6.112,10.57,6,10.285,6,10s0.112-0.57,0.335-0.789
      c0,0,4.084-4.287,4.502-4.695C11.255,4.107,12.007,4.08,12.452,4.516z" class="pagination__nav-icon-newer" />
      </svg>
    </span>First page</a>
    {{ end }}

    <a href="{{ if .HasPrev }}{{ .Prev.URL }}{{ end }}" class="pagination__nav pagination__nav--newer {{ if not .HasPrev }}pagination__nav--disabled{{ end }}"><span class="pagination__nav-icon">
      <svg version="1.1" id="Chevron_left" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px"
      y="0px" viewBox="0 0 20 20" enable-background="new 0 0 20 20" xml:space="preserve">
      <path d="M12.452,4.516c0.446,0.436,0.481,1.043,0,1.576L8.705,10l3.747,3.908c0.481,0.533,0.446,1.141,0,1.574
      c-0.445,0.436-1.197,0.408-1.615,0c-0.418-0.406-4.502-4.695-4.502-4.695C6.112,10.57,6,10.285,6,10s0.112-0.57,0.335-0.789
      c0,0,4.084-4.287,4.502-4.695C11.255,4.107,12.007,4.08,12.452,4.516z" class="pagination__nav-icon-newer" />
      </svg>
    </span>Newer posts</a>

    {{ $scratch := newScratch }}
    {{ $scratch.Set ".ellipsed" false }}
    {{ range .Pagers }}

    {{ $right := sub .TotalPages .PageNumber }}
    {{ $showNumber := or (le .PageNumber 3) (eq $right 0) }}
    {{ $showNumber := or $showNumber (and (gt .PageNumber (sub .PageNumber 2)) (lt .PageNumber (add .PageNumber 2))) }}

    {{ if $showNumber }}
      {{ $scratch.Set "__paginator.ellipsed" false }}
      {{ $scratch.Set "__paginator.shouldEllipse" false }}
    {{ else }}
      {{ $scratch.Set "__paginator.shouldEllipse" (not ($scratch.Get "__paginator.ellipsed") ) }}
      {{ $scratch.Set "__paginator.ellipsed" true }}
    {{ end }}

    {{ if $showNumber }}
    <a class="pagination__nav {{ if eq .PageNumber $thisPageNumber }}pagination__nav--active{{ end }}" href="{{ .URL }}">{{ .PageNumber }}</a>
    {{ else if ($scratch.Get "__paginator.shouldEllipse") }}
    <span class="pagination__nav pagination__nav--disabled" aria-hidden="true">&hellip;</span>
    {{ end }}

    {{ end }}

    <a href="{{ if .HasNext }}{{ .Next.URL }}{{ end }}" class="pagination__nav pagination__nav--older {{ if not .HasNext }}pagination__nav--disabled{{ end }}">Older posts<span class="pagination__nav-icon">
      <svg version="1.1" id="Chevron_right" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px"
      y="0px" viewBox="0 0 20 20" enable-background="new 0 0 20 20" xml:space="preserve">
      <path d="M9.163,4.516c0.418,0.408,4.502,4.695,4.502,4.695C13.888,9.43,14,9.715,14,10s-0.112,0.57-0.335,0.787
      c0,0-4.084,4.289-4.502,4.695c-0.418,0.408-1.17,0.436-1.615,0c-0.446-0.434-0.481-1.041,0-1.574L11.295,10L7.548,6.092
      c-0.481-0.533-0.446-1.141,0-1.576C7.993,4.08,8.745,4.107,9.163,4.516z" class="pagination__nav-icon-previous" />
      </svg>
    </span></a>

    {{ with .Last }}
    <a href="{{ .URL }}" class="pagination__nav pagination__nav--older">Last page<span class="pagination__nav-icon">
      <svg version="1.1" id="Chevron_right" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px"
      y="0px" viewBox="0 0 20 20" enable-background="new 0 0 20 20" xml:space="preserve">
      <path d="M9.163,4.516c0.418,0.408,4.502,4.695,4.502,4.695C13.888,9.43,14,9.715,14,10s-0.112,0.57-0.335,0.787
      c0,0-4.084,4.289-4.502,4.695c-0.418,0.408-1.17,0.436-1.615,0c-0.446-0.434-0.481-1.041,0-1.574L11.295,10L7.548,6.092
      c-0.481-0.533-0.446-1.141,0-1.576C7.993,4.08,8.745,4.107,9.163,4.516z" class="pagination__nav-icon-previous" />
      </svg>
    </span></a>
    {{ end }}
  </div>

  <span class="pagination__count">Page {{ .PageNumber }} of {{ .TotalPages }}</span>
</nav>
{{ end }}
```

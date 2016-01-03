---
layout: post
title:  "Fitting Three 80-column Emacs windows on 1920x1080 monitor"
date:   2016-01-03
tags: [emacs]
---

I can't read any smaller than 10.5pt.
Fortunately, these tricks let me fit exactly three 80-column windows of a 10.5pt font on my 1920x1080 monitor.

Before
------

{% highlight elisp %}
(set-face-attribute 'default nil :family "Consolas" :height 105)
{% endhighlight %}

[Three windows 78 columns each screenshot](/images/emacs-three-windows-78-columns-each.png)

Without Fringes
---------------

{% highlight elisp %}
(set-face-attribute 'default nil :family "Consolas" :height 105)
(set-fringe-style 0)
{% endhighlight %}

[Three windows 79 columns each screenshot](/images/emacs-three-windows-79-columns-each.png)

Without Truncation and Wrap Characters
--------------------------------------

The truncation glyph indicates truncated lines and defaults to `$`; the wrap
glyph indicates wrapped lines and defaults to `\`.
We can squeeze in an 80th character by using this hack to 'get rid of them':

{% highlight elisp %}
(set-face-attribute 'default nil :family "Consolas" :height 105)
(set-fringe-style 0)
;; Seting these to newlines makes one extra character fit on each line.
;; I don't know why this works; it may break in the future.
(let ((newline-glyph (make-glyph-code ?\n)))
  (set-display-table-slot standard-display-table 'truncation newline-glyph)
  (set-display-table-slot standard-display-table 'wrap newline-glyph))
{% endhighlight %}

[Three windows 80 columns each screenshot](/images/emacs-three-windows-80-columns-each.png)

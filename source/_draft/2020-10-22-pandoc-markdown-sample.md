---
title: Pandoc Markdown sample
date: 2020-10-22 22:44:59
tags: "markdown"
categories: ["computer science", "other"]
mathjax: true
documentclass: ctexart
classoption: UTF8
geometry:
- margin=1in
- a4paper
---

用于测试 markdown 能否正确被渲染（hexo 或 pandoc）

* * *

heading:

# h1

## h2

### h3

#### h4

##### h5

###### h6

* * *

block quote:

> block quote 1
>
> meant adult brought carry entire needle figure copy muscle weight chief repeat slight note same support related team track poor hold pilot vapor charge
>
> sense smallest wagon ten to they rhythm shadow war parts write lay suddenly thank pain everywhere variety orange bottom health pole shop highway mail

> block quote 2
getting bit habit outside bark snake dangerous oldest won bank took settlers term plane sing attached eye load stairs phrase neighborhood thirty pick trip
continued planned suit offer telephone pain daily win visitor memory examine tone settlers go school military shallow warn cent cookies possibly if atmosphere stage

* * *

code block:

```haskell
qsort [] = []
```

```java
public static void main(String[] args){
    System.out.println("hello world");
}
```

* * *

compact list:

* compact 1
* compact 2
* compact 3

loose list:

* loose 1

* loose 2

* loose 3

block content:

* First paragraph.

  Continued.

* Second paragraph. With a code block, which must be indented eight spaces:

      { code }

ordered list:

1. one
2. two
3. three

* * *

table:

| Right | Left | Default | Center |
|------:|:-----|---------|:------:|
|   12  |  12  |    12   |    12  |
|  123  |  123 |   123   |   123  |
|    1  |    1 |     1   |     1  |

  : Demonstration of pipe table syntax.

* * *

emphasis: *sample*

strong emphasis: **sample**

strike out: ~~sample~~

H~2~O is a liquid.  2^10^ is 1024.

inline code: `sample`

This is an [inline link](/url)

Here is [my link][FOO]

[Foo]: /bar/baz

* * *

inline math: $\LaTeX$

display math:

$$
\LaTeX
$$

$$
\begin{aligned}
\LaTeX\\
\LaTeX
\end{aligned}
$$

* * *

Here is a footnote reference,[^1] and another.[^longnote]

[^1]: Here is the footnote.

[^longnote]: Here's one with multiple blocks.

    Subsequent paragraphs are indented to show that they
belong to the previous footnote.

        { some.code }

    The whole paragraph can be indented, or just the first
    line.  In this way, multi-paragraph footnotes work like
    multi-paragraph list items.

This paragraph won't be part of the note, because it
isn't indented.


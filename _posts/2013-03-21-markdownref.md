---
layout: post
title: Markdown Reference Guide
category: posts
---

So this blog is powered by [Jekyll](https://github.com/mojombo/jekyll), 
a static site generator. Basically Jekyll transforms markdown files into 
static html pages. No databases required or dynamic content generated, but it 
works great for simple sites and its *fast*.

Doing a quick search on markdown will probably lead you to the markdown 
specifications over at [daringfireball.net](https://github.com/mojombo/jekyll) 
Sadly, I couldn't find a simple reference with -visual- examples. So I've made 
a quick and dirty guide here. It's not comprehensive but it shows the most
common formatting syntax. Note that markdown is parsed into html, so 
stylesheets will dictate how it looks.

Headers
========
You can use any number of underlining with ='s or -'s.

Example:

    News Flash!
    -----------

    News Flash!
    ===========

Output:
News Flash!
---

News Flash!
===
<hr>
You can also specify header size with hashes. Number of opening hashes 
mark header size. Closing hashes are ignored but can be added for aesthetics.

Example:
    # h1
    ## h2
    ### h3
    #### h4
    ##### h5
    ###### h6 

Output:
# h1
## h2
### h3
#### h4
##### h5
###### h6 
<hr>

Links
=====
Specify link text in brackets and enclose the url in parenthesis.

Example:
    [reddit](http://reddit.com)

Output:

[reddit](http://reddit.com)

<hr>

Blockquotes
===========

Use > to denote quoted blocks

Example:
    > Two roads diverged in a yellow road..
    > And I took the one less traveled by

Output:
> Two roads diverged in a yellow road..
> And I took the one less traveled by

<hr>
You can also nest them, inception-style!

Example:
    > Row Row
    >
    > Row your boat
    >
    >> Aaaaaaggghggh i'm drowning
    >>
    >> Help
    >
    > Gently down the stream

Output:
> Row Row
>
> Row your boat
>
>> Aaaaaaggghggh i'm drowning
>>
>> Help
>
> Gently down the stream

<hr>

Lists
=====

Use *'s, +'s or -'s for unordered lists. Use numbers for ordered lists -- 
caveat: the actual numbers don't matter. Markdown will automatically 
interpret numbered lists into ordered list tags 

Example:

    * Apple
    * Banana
    * Orange

    + Grapes
    + Pineapple
    + Mango

    - Chocolate
    - Vanilla
    - Strawberry

    1. Morgan Freeman
    2. Jennifer Aniston
    3. Will Smith

    2. Tiger Woods
    14. Bill Clinton
    1. Kobe Bryant

Output:

* Apple
* Banana
* Orange
<hr>
+ Grapes
+ Pineapple
+ Mango
<hr>
- Chocolate
- Vanilla
- Strawberry
<hr>
1. Morgan Freeman
2. Jennifer Aniston
3. Will Smith
<hr>
2. Tiger Woods
14. Bill Clinton
1. Kobe Bryant
<hr>

Emphasis
========
Wrap text with asterisks or underscores for emphasis. Double up for strong

Example:
    *unbelievable*
    _unbelievable_

    that's what **she** said
    that's what __she__ said

Output:

*unbelievable*

_unbelievable_
<hr>
that's what **she** said

that's what __she__ said
<hr>
Code
====
Wraps code segments with backticks (`).

Example:

    `System.out.println('Hello world');`

    Use `npm install` to automatically packages defined in `package.json`

Output:

`System.out.println('Hello world');`

Use `npm install` to automatically packages defined in `package.json`
<hr>

Images
======
Images use syntax similar to links but with a preceding exclamation (!).
Instead of link text, alt text is defined inside brackets. Url inside 
parenthesis.

Example:
    ![Carlton](/images/carlton.jpg)

Output:
![Carlton](/images/carlton.jpg)
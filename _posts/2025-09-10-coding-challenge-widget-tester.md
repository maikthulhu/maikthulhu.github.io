---
layout:     post
title:      "Coding Challenge: Widget Tester Test Widget Tester"
date:       2025-09-10
tags:       [coding, challenge, widget]
---

## Introduction
Random ass blog post time! Just had a coding challenge ~~after literally spending hours on a such a remarkably bullshit similar problem fuuuuu~~ pop into my head and wasn't sure where to put it, so to the blog it goes!

I swear I've built my career mostly on being good at googling shit, but this one eluded me and I will not show you my solution. lol. no, sir. Maybe it's a sign I'm getting older, but that it's coinciding with el goog becoming markedly worse lately helps me worry less.

![people hi](/assets/img/people_hi.gif)

## Scenario
For lack of a more apparent example - Imagine, if you will:
> You work in the quality assurance department for a great big widget manufacturer. Widgets, as we all know, have 3 holes. Hole "a" can either be open (0) or closed (1). Likewise, hole "b" can either be open (0) or closed (1). Concordantly... ok.

>Your shiny new Widget Tester 1000 can operate on a large number of widgets at one time. To start, we'd like to generate the minimum number of test widgets necesary to test each combination of hole configurations, with no repeats.


I don't know if that holds up, @ me ✌️

## Details
_Tools_
>Using whatever means necessary (yes, itertools if you must):

_Task_
>write a routine that takes a list of items (strings, objects, whatever), and returns a list of tuples of combinations of each item, paired with either a 0 or 1, indicating whether the item is "off" or "on".
```
## Example
_input:_
```
[
    "a", "b", "c"
]
```

_output:_
```
[
    [("a", 0), ("b", 0), ("c", 1)],
    [("a", 0), ("b", 1), ("c", 0)],
    [("a", 0), ("b", 1), ("c", 1)],
    [("a", 1), ("b", 0), ("c", 0)],
    [("a", 1), ("b", 0), ("c", 1)],
    [("a", 1), ("b", 1), ("c", 0)],
    [("a", 1), ("b", 1), ("c", 1)],
]
```
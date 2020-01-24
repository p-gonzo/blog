---
title: Bit Twiddling in JavaScript
date: 2020-01-24
published: false
tags: ['JavaScript', 'C++', 'Bit Twiddling']
series: false
canonical_url: false
description: ""
---

Recently, I have been picking up C++ for a client project.  To get comfortable with the syntax, I am working through a number of [Code Wars](http://codewars.com) challenges.  While completing these, I came across a traditional CS challenge to count the number of **set bits** in an integer:

For those not familiar with base 10 to base 2 conversations, and what `set bits` are, check out the table below:


|Number |   128 |   64  |   32  |   16  |   8   |   4   |   2   |   0   |Calculation    | Set Bits  |
|:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:          |:---:      |
|**4**  |   0   |   0   |   0   |   0   |   0   |   1   |   0   |   0   |4^1 = 4        |   1       |
|**8**  |   0   |   0   |   0   |   0   |   1   |   0   |   0   |   0   |8^1 = 8        |   1       |
|**7**  |   0   |   0   |   0   |   0   |   0   |   1   |   1   |   1   |4^1+2^1+0^1=7  |   3       |
|**35** |   0   |   0   |   1   |   0   |   0   |   0   |   1   |   1   |32^1+2^1+0^1=35|   3       |
<br/>
Before learning C++, I hadn't learned
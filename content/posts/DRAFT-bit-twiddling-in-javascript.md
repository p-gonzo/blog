---
title: Bit Twiddling in JavaScript
date: 2020-01-24
published: false
tags: ['JavaScript', 'C++', 'Bit Twiddling']
series: false
canonical_url: false
description: ""
---

Recently, I have been picking up C++ for a client project.  To get comfortable with the syntax, I am working through a number of [Code Wars](http://codewars.com) challenges.  While completing these, I came across a traditional CS challenge to **count the number of set bits** in an integer:

For those not familiar with base 10 to base 2 conversations, and what `set bits` are, check out the table below:


|number |   128 |   64  |   32  |   16  |   8   |   4   |   2   |   0   |conversion    | set bits  |
|:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:         |:---:      |
|**4**  |   0   |   0   |   0   |   0   |   0   | **1** |   0   |   0   |4¹ = 4        |   1       |
|**8**  |   0   |   0   |   0   |   0   | **1** |   0   |   0   |   0   |8¹ = 8        |   1       |
|**7**  |   0   |   0   |   0   |   0   |   0   | **1** | **1** | **1** |4¹+2¹+0¹ = 7  |   3       |
|**35** |   0   |   0   | **1** |   0   |   0   |   0   | **1** | **1** |32¹+2¹+0¹ = 35|   3       |

<br/>

To begin our `countSetBits` journey, let's use this helper function to display the base 2 representation of a base 10 number:


```javascript
const displayBits = n => {
    const bits = [];
    while(n) {
        bits.push(n & 1);
        n = n >> 1;
    }
    return bits.reverse();
}
```

As you can see in the function above, we are storing our bits in an Array called `bits` and returning it's reversed value at the end of the function.

The interesting part of this function happens inside of the while loop.  Our function is passed an integer `n`:

So while n is not falsey, we:
-   Evaluate `n & 1`, and push the result onto our Array
-   Reassign `n` to `n >> 1`

The first part of our loop, we are using the bitwise operator `&` (as distinct from the boolean operator `&&`), to evaluate our number, `n` with `1`.  The bitwise `&` operator evaluates the bits of two numbers, and returns a new number where both bits are set in the same location.

For example, if we use the numbers 5 and 7:

| base 10 | base 2 |
|--|--|
|`5`|`101`|
|`7`|`111`|
|`5 & 7`|`101`|

<br/>

More interestingly, if we use the numbers 5 and 1:

| base 10 | base 2 |
|--|--|
|`5`|`101`|
|`1`|`001` (or just `1`)|
|`5 & 1`|`001` (or just `1`)|

<br/>

We can see that when two bits **in the same position** overlap, the `&` operator returns a new number with only the overlapping bits set.  In the case with the comparison with `1`, we, we only are interested in the number's **least significant bit**. 

Masking:
https://en.wikipedia.org/wiki/Mask_(computing)
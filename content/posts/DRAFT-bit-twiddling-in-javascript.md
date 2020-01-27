---
title: Bit Twiddling in JavaScript
date: 2020-01-24
published: true
tags: ['JavaScript', 'C++', 'Bit Twiddling']
series: false
cover_image: ./images/ones-and-zeros.jpg
canonical_url: false
description: ""
---

Recently I have been brushing up on my C++ for a client project.  In my spare time, I have been doing a number of toy problems to get comfortable with the syntax, and of these problems was a pretty cool challenge to **count the number of set bits** in an integer.  Spending a lot of time in the browser, I wanted to re-implement it in JavaScript.

The challenge itself involves expressing integers not in their traditional base 10 representation, but instead their using base 2, binary, representation.  For those not familiar with base 10 to base 2 conversations, and what `set bits` are, check out the table below:


|number|  128 |   64  |   32  |   16  |   8   |   4   |   2   |   0   |conversion    | set bits  |
|:---:|:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:         |:---:      |
|`4`  |   0   |   0   |   0   |   0   |   0   | `1`   |   0   |   0   |4¹ = 4        |   1       |
|`4`  |   0   |   0   |   0   |   0   |   0   | `1`   |   0   |   0   |4¹ = 4        |   1       |
|`8`  |   0   |   0   |   0   |   0   | `1`   |   0   |   0   |   0   |8¹ = 8        |   1       |
|`7`  |   0   |   0   |   0   |   0   |   0   | `1`   |   `1` |   `1` |4¹+2¹+0¹ = 7  |   3       |
|`35` |   0   |   0   | `1`   |   0   |   0   |   0   | `1`   | `1`   |32¹+2¹+0¹ = 35|   3       |

<br/>

### Displaying integer bits in JavaScript:

To begin our `countSetBits` journey, we can use the following helper function to display the base 2 representation of a base 10 integer:


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

*Note: there is a far simpler way of accomplishing base 10 to base 2 conversion this in JavaScript.  By running `(n).toString(2)`, we can return our integer as its stringified base 2 self.  We will instead however, refer to the formula above to illustrate several properties about bitwise operators.*

As you can see in the function above, we will be storing the ones and zeros in an Array called `bits` and returning it's reversed value at the end of the function.

The interesting part of the function happens inside of the while loop.  Our function is passed an integer `n`, and while `n` is not falsey, we:
-   Evaluate `n & 1`, and push the result onto our Array
-   Reassign `n` to `n >> 1`


#### Evaluate `n & 1`, and push the result onto our Array:

The first part of our loop uses the bitwise operator `&` (as distinct from the boolean operator `&&`), to evaluate `n` with `1`.

The bitwise `&` operator is similar to the boolean `&&` operator in that it will return `1` if and only if both bits are set to `1`, otherwise it will return `0`, it is different in that it compares each bit in both numbers separately.

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

We can see that when two bits **in the same position** overlap, the `&` operator returns a new number with only the overlapping bits set.

In the case with the comparison with `1`, we are only are interested in the number's [least significant bit](https://en.wikipedia.org/wiki/Bit_numbering#Least_significant_bit), the rightmost bit that represents either a base 10 1 or 0.

By using the `&` against the number 1, we can check whether the least significant bit is is set or not.  This is also called **bit masking**, or just **masking** for short.  We mask a given integer with 1, and it's return value tells us whether or not the least significant bit is set or not.

Given that our loop contains the expression `bits.push(n & 1)`, we will either push a 0 or 1 into our bits array based on whether or not n's least significant bit is set or not. 


#### Reassign `n` to `n >> 1`:

The first command in our loop tells us whether or not the least significant bit is `1` or `0` (set or not set) and pushes the value into the bits array.  But if we always mask with one, we will only ever check the least significant bit.

This is where the second command in our loop comes in.  By reassigning `n` to `n >> 1`, we shift all of `n`'s bits to the right by exactly one bit.

- The integer 5 in binary is: `101`
    - and `5 >> 1` is `10` (**2** as a base 10 integer)
    - and `5 >> 1 >> 1` is `1` (**1** as a base 10 integer)
    - finally `5 >> 1 >> 1 >> 1` is `0` (**0** as a base 10 integer)


Putting these two aspects of our loop together allow us to display base 10 integers in their binary format:

``` javascript
displayBits(5); // [1, 0, 1]
displayBits(9); // [1, 0, 0, 1]
displayBits(200); // [1, 1, 0, 0, 1, 0, 0, 0]
```

### A note on JavaScript Numbers:

Under the hood, JavaScript represents *all* numbers as a 64 bit floating point, so when you use a bitwise operator on a JavaScript number, the number is temporarily transformed into a 32 bit Integer.

We can demonstrate the conversion to a 32 bit integer by taking a JavaScript number (64 bits), and performing a "non mutating" bit shift by zero on it.)


```javascript
let x = 2147483647; // maximum value of a 32 bit integer.
x + 1 // 2147483648, number increments correctly.
(x + 1) >> 0 // -2147483648, bitwise right shift by zero. 32 bit integer overflows.
```

MDN, has a very good explanation of [some of the quirks](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_logical_operators) of this conversion, most notably:

> Numbers with more than 32 bits get their most significant bits discarded. For example, the following integer with more than 32 bits will be converted to a 32 bit integer

> ```
> Before: 11100110111110100000000000000110000000000001
> After:              10100000000000000110000000000001
> ```

Because I wrote this post mostly from a standpoint of academic curiosity, we'll ignore this quirk for the time being, however it's certainly notable.
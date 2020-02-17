---
title: Bit Twiddling in JavaScript
date: 2020-01-24
published: true
tags: ['JavaScript', 'C++', 'Bit Twiddling']
series: false
cover_image: ./images/ones-and-zeros.jpg
canonical_url: false
description: "Recently, I came across a fun C++ challenge to count the number of set bits in an integer.  Spending a lot of time in the browser, I wanted to re-implement it in JavaScript."
---

Recently, I came across a fun C++ challenge to **count the number of set bits** in an integer.  Spending a lot of time in the browser, I wanted to re-implement it in JavaScript.

The challenge itself involves expressing integers in their binary representation.  For those not familiar with base-10 to base-2 conversations, and what `set bits` are, check out the table below:


|number|  128 |   64  |   32  |   16  |   8   |   4   |   2   |   0   |conversion    | set bits  |
|:---:|:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:  |:---:         |:---:      |
|`4`  |   0   |   0   |   0   |   0   |   0   | `1`   |   0   |   0   |4¹ = 4        |   1       |
|`4`  |   0   |   0   |   0   |   0   |   0   | `1`   |   0   |   0   |4¹ = 4        |   1       |
|`8`  |   0   |   0   |   0   |   0   | `1`   |   0   |   0   |   0   |8¹ = 8        |   1       |
|`7`  |   0   |   0   |   0   |   0   |   0   | `1`   |   `1` |   `1` |4¹+2¹+0¹ = 7  |   3       |
|`35` |   0   |   0   | `1`   |   0   |   0   |   0   | `1`   | `1`   |32¹+2¹+0¹ = 35|   3       |

<br/>

### A note on JavaScript Numbers:

Under the hood, JavaScript represents *all* numbers as 64 bit floating point numbers.  When we use a bitwise operator on a JavaScript `number`, it is transformed into a 32 bit integer.

We can demonstrate the conversion to a 32 bit integer by taking a JavaScript `number`, and performing a non mutating right bit shift by zero on it.


```javascript
let x = 10;
y + 1 // = 6
(y + 1) >> 0 // = 6 >> 0 = 6.  Six bit shifted to the right by zero bits is still six

let y = 2147483647; // maximum value of a 32 bit integer.
y + 1 // = 2147483648, number increments correctly.
(y + 1) >> 0 // = -2147483648, bitwise right shift by zero. 32 bit integer overflows.
```

MDN, has a very good explanation of [some of the quirks](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_logical_operators) of this conversion:

> Numbers with more than 32 bits get their most significant bits discarded. For example, the following integer with more than 32 bits will be converted to a 32 bit integer

> ```
> Before: 11100110111110100000000000000110000000000001
> After:              10100000000000000110000000000001
> ```

Don't expect bitwise operations to work as expected with numbers that need more than 32 bits of of memory, or any number over `2147483647`.

```javascript

// Show two different numbers from binary to base-10:

parseInt("11100110111110100000000000000110000000000001", 2); // 15872588537857
parseInt(            "10100000000000000110000000000001", 2); // 2684379137

// Show those same numbers bit shifted to the right by zero:

15872588537857 >> 0; // -1610588159
2684379137 >> 0; // -1610588159

// ¯\_(ツ)_/¯
```

### Displaying integer bits in JavaScript:

To see how we can implement `countSetBits`, we will use the following helper function to display the binary representation of an integer:


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

**Note**: There is a far simpler way of displaying an integer to binary in JavaScript:

```javascript
(5).toString(2); // "101"
(7).toString(2) // "111"
```

We will instead use the `displayBits` function above to discuss bitwise operators.

The function stores the ones and zeros in an Array called `bits` and returns it's reversed value.

In the loop, the function evaluates an integer `n`, and while `n` is not falsey it:
-   Evaluates `n & 1`, and pushes the result onto `bits`
-   Reassigns `n` to `n >> 1`


#### Evaluate `n & 1`, and push the result onto `bits`:

The bitwise `&` operator compares each bit in two integers and returns `1` if both bits are set to `1`, otherwise it returns `0`.

For example, using 5 and 7:

| base 10 | binary |
|--|--:|
|`5`|`101`|
|`7`|`111`|
|`5 & 7`|`101`|

<br/>

And using 5 and 1 (our function uses `n & 1`):

| base 10 | binary |
|--|--:|
|`5`|`101`|
|`1`|`  1`|
|`5 & 1`|`1`|

<br/>

Using 7 and 1:

| base 10 | binary |
|--|--:|
|`7`|`111`|
|`1`|`  1`|
|`7 & 1`|`1`|

<br/>

Using 8 and 1:

| base 10 | binary |
|--|--:|
|`8`|`1000`|
|`1`|`   1`|
|`8 & 1`|`0`|

<br/>

We can see that when two ones **in the same position** overlap, the `&` operator returns a `1` in that position, otherwise it returns a `0`.

When we perform a bitwise `n & 1` we are only are interested in `n`'s [least significant bit](https://en.wikipedia.org/wiki/Bit_numbering#Least_significant_bit) (its rightmost bit), to see if it is a `0` or `1`.

Because our loop runs `bits.push(n & 1)`, we will either push a `0` or `1` into our bits array based on whether or not `n`'s least significant bit is set or not. 


#### Reassign `n` to `n >> 1`:

By reassigning `n` to `n >> 1`, we shift all of `n`'s bits to the right by exactly one bit.

- The integer 5 in binary is: `101`
    - `5 >> 1` is `10` (**2** as a base-10 integer)
    - `5 >> 1 >> 1` is `1` (**1** as a base-10 integer)
    - `5 >> 1 >> 1 >> 1` is `0` (**0** as a base-10 integer)


Putting these two aspects of our loop together allow us to display base 10 integers in their binary format:

``` javascript
displayBits(5); // [1, 0, 1]
displayBits(9); // [1, 0, 0, 1]
displayBits(200); // [1, 1, 0, 0, 1, 0, 0, 0]
```


### Counting Set Bits:

### A More Efficient Way:

### Practical Usage: A Bitmasked Enum:
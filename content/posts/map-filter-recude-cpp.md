---
title: Implementing Map, Filter, and Reduce in C++ with Iterators and Template Specialization
date: 2020-05-20
published: true
tags: ['C++', 'Functional Programing', 'Template Specialization', 'Lambda Expressions']
series: false
cover_image: ./images/cpp-map-filter-reduce.png
canonical_url: false
description: "For funsies, let's implement JavaScript's <code class=language-text>Map()</code>, <code class=language-text>Filter()</code>, and <code class=language-text>Reduce()</code> in C++."
---

For starters, let's acknowledge that C++ already has it's own superior versions of Map, Filter, and Reduce in the form of `std::transform`, `std::remove_if`, and `std::accumulate`.  Our goal isn't to compete with these preexisting functions, but to instead learn about several C++ features by implementing simple versions from scratch.  With that out of the way, let's do some functional programming in C++!

## Using a templated `std::vector`:

Imagining for a moment that we are okay with our [higher order functions](https://eloquentjavascript.net/05_higher_order.html#p_cao2fH68Tj) (map, filter, reduce) only being able to operate on a `std::vector`, we might start with something like this:

```cpp
template <typename T>
void ForEach(std::vector<T> &items, std::function<void(T &item)> forEachCb)
{
    for (T item: items)
        forEachCb(item);
}
```

Our `ForEach` function above accepts an array of type `T` and callback that accepts one item of type `T` as its parameter and returns nothing.  In the `ForEach` body, we iterate through each item in our vector and invoke the callback on each item.

We could invoke our `ForEach` function like so:

```cpp
std::vector<int> nums { 1, 2, 3, 4, 5 };
void printNum(int num&) {std::cout << num << std::endl; }
ForEach<int>(nums, printNum)
```

Or using Lambda syntax:

```cpp
std::vector<int> nums { 1, 2, 3, 4, 5 };
ForEach<int>(nums, [](int num&){ std::cout << num << std::endl; });
```

For an overview of Lambda Expression syntax, check out the docs from Microsoft [here](https://docs.microsoft.com/en-us/cpp/cpp/lambda-expressions-in-cpp).

Because ForEach accepts a callback function, we can use it to compose our three main methods.  Starting with Map, we might write something like this:

```cpp
template <typename T, typename T2>
std::vector<T2> Map(std::vector<T> &items, std::function<T2(T &item)> mapCb)
{
    std::vector<T2> mappedVec;
    ForEach<T>(items, [&mappedVec, &mapCb](T &item) { mappedVec.emplace_back(mapCb(item)); });
    return mappedVec;
}
```

We can see that `Map` is a templated function that accepts two types
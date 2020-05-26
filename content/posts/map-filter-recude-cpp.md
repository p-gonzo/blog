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

## Part I - Using a templated `std::vector`:

Imagining for a moment that we are okay with our [higher order functions](https://eloquentjavascript.net/05_higher_order.html#p_cao2fH68Tj) (map, filter, reduce) only being able to operate on a `std::vector`, we might start with something like this:

```cpp
template <typename T>
void ForEach(std::vector<T> &items, std::function<void(T &item)> forEachCb)
{
    for (T item: items)
        forEachCb(item);
}
```

Our ForEach function above accepts an array of type `T` and callback that accepts one item of type `T` as its parameter and returns nothing.  In the ForEach body, we iterate through each item in our vector and invoke the callback on each item.

We could invoke our ForEach function like so:

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

### `Map()`:

Because ForEach accepts a callback function, we can use it to compose our three main methods.  Starting with Map, we might implement something like this:

```cpp
template <typename T, typename T2>
std::vector<T2> Map(std::vector<T> &items, std::function<T2(T &item)> mapCb)
{
    std::vector<T2> mappedVec;
    ForEach<T>(items, [&mappedVec, &mapCb](T &item) { mappedVec.emplace_back(mapCb(item)); });
    return mappedVec;
}
```

We can see that Map is a templated function that accepts two types: `T` - the data type contained in the original array, and `T2` - the data type contained in returned array.  The function accepts the parameters `items` a `std::vector<T>&` and a callback function.  The callback accepts a `T&` and returns a `T2`.

Map creates a `std::vector<T2>` and returns it at the end of the function call.  In between, it invokes ForEach, passing it the original `std::vector<T>` and a callback function that pushes the item onto the `mappedVec` after calling the user-supplied `mapCb` on it.

It's useful to note that there are two callbacks occurring: **1)** the user-supplied callback which is nested inside **2)** the callback that gets consumed by ForEach.  

Finally we see the `[&mappedVec, &mapCb]` syntax at the start of our lambda expression.  This allows our `mappedVec` and `mapCb` to remain in scope as the expression ```mappedVec.emplace_back(mapCb(item))``` gets passed to ForEach.  This syntax allows us to create a [closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) around those two variables.

We can then invoke our Map function like so: 

```cpp
std::vector<int> nums { 1, 2, 3, 4, 5 };

struct IntContainer
{
    IntContainer(int v): val(v) { }
    int val {0};
};

auto mappedVed = Map<int, IntContainer>(nums, [](int &num) {return IntContainer(num * num); });
```

The example above maps between a `std::vector` containing the `int` type and `std::vector` containing a custom type, `IntContainer`. 

### `Filter()` and `Reduce()`:

We can implement Filter and Reduce following a similar pattern:

```cpp
template <typename T>
std::vector<T> Filter(std::vector<T> &items, std::function<bool(T &item)> filterCb)
{
    std::vector<T> filteredVec;
    ForEach<T>(items, [&filteredVec, &filterCb](T &item) { if (filterCb(item)) filteredVec.emplace_back(item); });
    return filteredVec;
}
```

Filter doesn't require a second return type `T2`, as we are assuming that the filtered vector will always be of the same type as the input vector.

```cpp
template<typename T, typename T2>
T2 Reduce(std::vector<T> &items, std::function<T2(T &item, T2 &memo)> reduceCb, const T2 &start)
{
    T2 memo = start;
    ForEach<T>(items, [&memo, &reduceCb](T &item) { memo = reduceCb(item, memo); });
    return memo;
}
```

Reduce uses a second type, `T2`, however unlike Map, it is not the type contained in a returned `std::vector`, but rather the return type itself.  

The T2 value is passed as a const ref, `start`, which is assigned to a non-const `memo` variable.  That variable is then mutated each time ForEach runs its callback:

```cpp
memo = reduceCb(item, memo);
```

## Part II - Using Iterators:

Though the above code works well, it will only work if our containing class is a `std::vector`.  What if we want to Filter a `std::string` or Reduce a `std::list`?
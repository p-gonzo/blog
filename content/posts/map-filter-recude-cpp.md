---
title: Building Map, Filter, and Reduce in C++ with Templates and Iterators
date: 2020-05-20
published: true
tags: ['C++', 'Functional Programing', 'Generics', 'Templates', 'Lambda Expressions']
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
    ForEach<T>(items, [&mappedVec, &mapCb](T &item) { mappedVec.push_back(mapCb(item)); });
    return mappedVec;
}
```

We can see that Map is a templated function that accepts two types: `T` - the data type contained in the original array, and `T2` - the data type contained in returned array.  The function accepts the parameters `items` a `std::vector<T>&` and a callback function.  The callback accepts a `T&` and returns a `T2`.

Map creates a `std::vector<T2>` and returns it at the end of the function call.  In between, it invokes ForEach, passing it the original `std::vector<T>` and a callback function that pushes the item onto the `mappedVec` after calling the user-supplied `mapCb` on it.

It's useful to note that there are two callbacks occurring: **1)** the user-supplied callback which is nested inside **2)** the callback supplied by Map that gets consumed by ForEach.  

Finally we see the `[&mappedVec, &mapCb]` syntax at the start of our lambda expression.  This allows our `mappedVec` and `mapCb` to remain in scope as the expression ```mappedVec.push_back(mapCb(item))``` gets passed to ForEach.  This syntax allows us to create a [closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) around those two variables.

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
    ForEach<T>(items, [&filteredVec, &filterCb](T &item) { if (filterCb(item)) filteredVec.push_back(item); });
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

## Part II - Using iterators:

Though the above code works well, it will only work if our containing class is a `std::vector`.  What if we want to Filter a `std::string` or Reduce a `std::list`?  To do this we'll refactor or original functions above to use the iterator interface.

### `ForEach()`:

To cut down on the amount of boilerplate, we're going to create a `using` clause that allows us to grab the type contained within an iterator:

```cpp
template <typename IteratorType>
using ItemType = typename std::iterator_traits<typename IteratorType::iterator>::value_type;
```

With that in place, we can implement our new `ForEach` like so:

```cpp
template <typename IteratorType>
void ForEach(IteratorType &items, std::function<void(ItemType<IteratorType> &item)> forEachCb)
{
    for (typename IteratorType::iterator ptr = items.begin(); ptr != items.end(); ++ptr)
        forEachCb(*ptr);
}
```

Similar to our original ForEach, the signature requires two parameters, however the first parameter, `items`, is no longer a `std::vector<T>`, but instead an `IteratorType`.  The second parameter is still a `std::function` though it's signature is different in that instead of each item being of type `T`, they're of type `<ItemType<IteratorType>>`.

Our iteration method has also changed.  Instead of using a C++ [range-based for loop](https://en.cppreference.com/w/cpp/language/range-for), we're using the [iterator interface](https://en.cppreference.com/w/cpp/iterator/iterator) and a normal for loop to iterate though each item and invoke a callback on the dereferenced pointer.


The syntax used in the function declaration is messier, however it allows us to do the following:

```cpp
std::list<char> chars {'a', 'b', 'c', 'd'};
std::vector<int> nums { 1, 2, 3, 4, 5 };
std::string word { "Hello" };

ForEach<std::list<char>>(chars, [](char &chr){ std::cout << chr << std::endl; });
ForEach<std::vector<int>>(nums, [](int &num) { std::cout << num << std::endl; });
ForEach<std::string>(word,[](char &letter) { std::cout << letter << std::endl; });
```

As you can see, our ForEach function is now agnostic as to whether we are passing a `std::string`, `std::vector`, `std::list`, or any other containing class so long as it implements the iterator interface.

### `Map()`, `Filter()`, and `Reduce()`:

Our new Map function's signature is almost identical to our new ForEach signature, with the only difference being that the callback now returns a `ItemType<IteratorType>` instead of `void`.

```cpp
template <typename IteratorType>
IteratorType Map(IteratorType &items, std::function<ItemType<IteratorType>(ItemType<IteratorType> &item)> mapCb)
{
    IteratorType mappedIterator;
    ForEach<IteratorType>(items, [&mappedIterator, &mapCb](auto &item) { mappedIterator.push_back(mapCb(item)); });
    return mappedIterator;
}
```

Filter and Reduce are also updated accordingly:

```cpp
template <typename IteratorType>
IteratorType Filter(IteratorType &items, std::function<bool(ItemType<IteratorType> &item)> filterCb)
{
    IteratorType filteredIterator;
    ForEach<IteratorType>(items, [&filteredIterator, &filterCb](auto &item) { if (filterCb(item)) filteredIterator.push_back(item); });
    return filteredIterator;
}
```

```cpp
template <typename IteratorType, typename MemoType>
MemoType Reduce(IteratorType &items, std::function<MemoType(ItemType<IteratorType> &item, MemoType &memo)> reduceCb, const MemoType &start)
{
    MemoType memo = start;
    ForEach<IteratorType>(items, [&memo, &reduceCb](auto &item) { memo = reduceCb(item, memo); });
    return memo;
}
```

With our three functions rewritten to accommodate any iterator, we can now begin using them.

Such as using Map to perform a Rot1-like sypher over the characters in a string:
```cpp
std::string word { "Hello" };
auto mappedWord = Map<std::string>(word, [](char &chr) { return static_cast<char>(chr + 1); });
```

Or reducing the a string into the sum of its character integer representations:

```cpp
std::string word { "Hello" };
int wordToNum = Reduce<std::string, int>(word, [](char &chr, int &memo){ return memo + (int)chr; }, 0);
// The above equals exactly 500 🤯
```

We can even operate on a `std::map`:

```cpp
std::map<int,char> items {std::pair<int, char>(5, 'a'), std::pair<int, char>(6, 'b'), std::pair<int, char>(7, 'c')};
ForEach<std::map<int,char>>(items, [](std::pair<int,char> pair){ std::cout << pair.first << ": " << pair.second << std::endl; } );
```

## Final Thoughts:

Hopefully this was a useful exercise in using iterators and templated functions to recreate the Filter, Map, and Reduce functions that exist on [Array.prototype](https://developer.mozilla.org/tr/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype) in JavaScript.  These recreations are presented for academic intrigue, and shouldn't be used in production.


Our Functions are a little more flexible than the native Javascript versions in that they can work on any containing class that implements the iterator interface.  However, they also lack some of the niceties provided in the native versions or in a library such as [Underscore.js](https://underscorejs.org/).  For example, Underscore's Reduce implementation accounts for the following:

> If no memo is passed to the initial invocation of reduce, the iteratee is not invoked on the first element of the list. The first element is instead passed as the memo in the invocation of the iteratee on the next element in the list.

Another area for improvement is ensuring [const correctness](https://isocpp.org/wiki/faq/const-correctness#overview-const) and memory optimization in our function signatures.  Many of our functions and lambda expressions can (and should) be passing by `const &` rather than by value.
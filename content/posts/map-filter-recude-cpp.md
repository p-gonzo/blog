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
---
layout: post
title: ["Hey f, show me you're const!"]
date: 2016-10-06
comments: true
---

## Introduction

This week, I was attending a C++ course. One of the discussed topics was the constexpr specifier. As Scott Meyers points out in EMC++ Item 15, when applied to objects, constexpr is essentially a beefed-up form of const. However, for a constexpr function
```c++
constexpr
int f(int x)
{
    return 2*x;
}
```
you can't assume that the result of `f` will be const, nor can you take for granted that its value is known during compilation.  The obvious question then arises as to how one can check that the value of a `constexpr` function is actually known at compile time.  One of the answers that popped up during the course was to take a look at the assembler output.  However, as I'm not really familiar with assembler, I was looking for other solutions.  In this post, I'm summarizing what I could come up with after a bit of searching on the net.

## Method 1: templates!

Inspired by EMC++ Item 4 'Know how to view deduced types', I was hoping some template-trickery could help me out.  Suppose we have two integers of which one is const:
```c++
const int a = 1;
int b = 2;
```
then we could define the following template:
```c++
template <int N>
struct ForceCompileTimeEvaluation {};
```
and use it like this to find out in what cases `f` can be evaluated at compile-time:
```c++
ForceCompileTimeEvaluation<f(1)> dummy_1;  // ok
ForceCompileTimeEvaluation<f(a)> dummy_a;  // ok
ForceCompileTimeEvaluation<f(b)> dummy_b;  // error
```
As it turns out, only the declaration for `dummy_b` fails to compile, so `f(b)` is not a compile-time constant.  A disadvantage of this method is that first of all one has to define the helper template `ForceCompileTimeEvaluation` and secondly, my educated guess is that some extra assembler code will be generated here (please correct me if I'm wrong).

## Method 2: `std::integral_constant`
One could also try to use `f` as a template parameter in `std::integral_constant` like this:
```c++
std::integral_constant<int, f(1)>::value;  // ok
std::integral_constant<int, f(a)>::value;  // ok
std::integral_constant<int, f(b)>::value;  // error
```
The disadvantage of this method that I can think of, is that it is only applicable for functions that return an `int`.

## Method 3: `constexpr` (again...)

A third method simply tries to assign the result of `f` to a `constexpr auto` variable.  If this fails to compile, then again it means `f` can't be evaluated at compile time.
```c++
constexpr auto result_1 = f(1);  // ok
constexpr auto result_a = f(a);  // ok
constexpr auto result_b = f(b);  // error
```

## Method 4: `static_assert`

`static_assert` requires a boolean constant expression as a parameter.  We can therefore pass the result of `f` to it and let that result be silently converted to `bool`.  This lets us write our checks as follows:
```c++
static_assert(f(1), "");  // ok
static_assert(f(a), "");  // ok
static_assert(f(b), "");  // error
```
Because `static_assert` performs compile-time assertion checking, it will not give us overhead during runtime and no unnecessary generated code.

## Conclusion

Given the considerations above, I guess using `static_assert` is the best method I have found to check if a `constexpr` function evaluates to some compile-time constant expression.  I would love to see alternative (and possibly more elegant) solutions.  I would also find it interesting to see how this can be checked by looking at the generated assembler output.  So feel free to send me your corrections, comments, suggestions that can even make this blog post better!

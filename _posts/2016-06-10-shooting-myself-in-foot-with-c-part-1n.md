---
layout: post
title: ["Shooting myself in the foot with C++ (part 1/n)"]
date: 2016-06-10
---

If you are a professional programmer, or even a not-so-advanced do-it-yourself hacker, you might know about Bjarne Stroustrup's quote

> C makes it easy to shoot yourself in the foot; C++ makes it harder, but when you do it blows your whole leg off.

This is my first post in an infinite series of posts about how I shot myself in the foot several times while using the C++ programming language. Let's just start with something that came up during a ``C++ Fundamentals'' course that I was participating in today.

Given an array
```c++
int a[10];
```
we all know that
```c++
sizeof(a)
```
will return the total number of bytes in the array and that this result is implementation-defined. On my platform, where an `int` is 4 bytes, it returns 40. So far so good.

Now, what size gets printed if we declare a function
```c++
void f(int x[])
{
    std::cout << sizeof(x) << std::endl;
}
```
taking an array of `int` as parameter, and pass our array to this function:
```c++
int main()
{
    int a[10];
    f(a);
}
```
If, for one second or longer, you thought that would also give 40 on my platform, then continue reading. The fact is that in this case, your intuition failed. Inside the function `f`, the result of `sizeof(x)` is 8 on my platform, being the size of an `int*`.

Investigating this a little further, led me to Stroustrup's *The C++ Programming Language, Special Edition*, section 7.2.1 *Array Arguments* that states:

> If an array is used as a function argument, a pointer to its initial element is passed. [...] That is, an argument of type `T[]` will be converted to a `T*` when passed. [...] In other words, arrays differ from other types in that an array is not (and cannot be) passed by value.

Next to that, *ISO/IEC 9899:1999 section 6.7.5.3/7* reads:

> A declaration of a parameter as ``array of type'' shall be adjusted to ``qualified pointer to type'', [...]

Note that this means that in the following three functions
```c++
void f1(int* x)
{
    std::cout << sizeof(x) << std::endl;
}
 
void f2(int x[])
{
    std::cout << sizeof(x) << std::endl;
}
 
void f3(int x[5])
{
    std::cout << sizeof(x) << std::endl;
}
```
the `sizeof` operator returns 8 (on my platform). So get used to it: the size you get when you pass an array as a parameter to a function is the size of a pointer to the type of the array. No more, no less!

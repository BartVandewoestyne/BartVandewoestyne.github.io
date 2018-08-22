---
layout: post
title: ["Struggling with legacy code (part 1 of n)"]
date: 2016-06-15
---

Today, I encountered a nice example of the kind of things our team has to deal with while working our way through legacy code. It boils down to the following: given is a function with the following declaration:
```c++
bool SomeClass::doSomethingThatCanFail();
```
Cppcheck pointed me to a piece of code that uses this function in an if-condition:
```c++
if (success = doSomethingThatCanFail() == false)
    return success;
```
Rather intriguing, isn't it?

For now, I decided to just deal with the fact that the equality operator has higher precedence than the assignment operator, and make that very explicit to the reader:
```c++
if (success = (doSomethingThatCanFail() == false))
    return success;
```
There, that will do for now. My brain is not ready for more refactoring. That is postponed till later. Bear with me!

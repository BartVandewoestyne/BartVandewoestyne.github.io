---
layout: post
title: [""]
date: 2016-06-10
---

# Introduction

During my dayjob, I often find myself wading through thousands of lines of legacy C++98 code.  One of the things I come across often, is a check for `NULL` before deleting a pointer:
```c++
if (somePointer != NULL)
{
    delete somePointer;
    somePointer = NULL;
}
```
In this post, I do not want to discuss whether it is good practice or not to set the pointer to `NULL` after the deletion. I do would like to have your opinions on the necessity of the check for `NULL`. Paragraph 2 in section 5.3.2 of the *ISO/IEC 14882 C++98 standard* mentions:

> [...] if the value of the operand of delete is the null pointer the operation has no effect.

which makes me conclude that the check for NULL is not necessary and so can be unconditionally removed.
However, I do not want to shoot myself in the foot. I prefer to be hyper-aware of the consequences of my changes when I decide to remove code in a legacy code base. That is the reason for this post. I am wondering if there are situations for which it is unsafe to remove the check for NULL.

Feel free to use the source code below to experiment and share me your thoughts in the comments!

```c++
/*
 * This sample tries to answer the question whether it is necessary to check
 * for NULL before deleting a pointer.
 *
 * Current answer:
 *
 *   The check is not necessary, because per 5.3.5/2 in ISO/IEC 14882,
 *   deleting a NULL pointer is a no-op.
 *
 * References:
 *
 *   [1] ISO/IEC 14882
 *   [2] http://stackoverflow.com/questions/615355/is-there-any-reason-to-check-for-a-null-pointer-before-deleting
 *   [3] http://stackoverflow.com/questions/13818803/check-for-null-before-delete-in-c-good-practice
 *   [4] http://stackoverflow.com/questions/8004495/deleting-a-null-pointer
 *   [5] http://stackoverflow.com/questions/4804258/c-checking-for-null-on-delete
 *
 */

#include <cstddef>

class Widget {};

int main()
{
    Widget* pw1 = new Widget;
    delete pw1;                 // typical case, works
    //delete pw1;               // error, cannot delete again!

    Widget* pw2 = NULL;
    delete pw2;                 // no-op (see 5.3.5/2 in [1])

    Widget* pw3 = new Widget;
    if (pw3 != NULL)            // a lot of legacy code often checks
    {                           // for NULL before deleting...
        delete pw3;             // QUESTION: is this check necessary???
    }
}
```

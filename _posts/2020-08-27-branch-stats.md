---
layout: post
published: true
title: Quick 'n' Dirty Branch Statistics with GCC
date: '2020-08-27'
---
In the appendix of Ulrich Drepper's 2007 paper on [memory and computer architecture](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) he demonstrates a way to verify how bad our branch prediction intuition truly is. To my amusement it still works, mostly untouched. Let's see what we can do with it.

# The Code
{% gist cdc01bf9a8f141ed37065ee5cdd4ef89 %}

Essentially the above will generate a section (a block of instructions and data in your final executable, kind of) in your assembly for each branch that you instrument. In each section we allocate two 8-byte counters (`.quad 0; .quad 0`). The [%=](https://gcc.gnu.org/onlinedocs/gcc/Extended-Asm.html) will generate a unique identifier and append it to the section name (so each branch will have its own counter). Similarly, we store the line number and filename.

The meat is in the last line of assembly. We increment one of the two counters by: `

{% gist 8092ffe8423ba4ef6257a7b04463634f %}

## Compiling
On some systems you will have to tell GCC not to produce a position independent executable (PIE) using: `-no-pie`

The C++ standard forbids the expression grouping we did in the `debugpred__` define. For us it's a non-issue since GCC supports it.
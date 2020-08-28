---
layout: post
published: true
title: Quick 'n' Dirty Branch Statistics with GCC
date: '2020-08-27'
---
In the appendix of Ulrich Drepper's 2007 paper on [memory and computer architecture](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) he shows a way to verify how bad our branch prediction intuition truly is. To my amusement it still works, mostly untouched. Let's see what we can do with it.

# The Code
{% gist cdc01bf9a8f141ed37065ee5cdd4ef89 %}


{% gist 8092ffe8423ba4ef6257a7b04463634f %}
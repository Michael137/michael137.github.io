---
layout: post
published: true
title: Quick 'n' Dirty Branch Statistics with GCC
date: '2020-08-27'
---
In the appendix of Ulrich Drepper's 2007 paper on [memory and computer architecture](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) he demonstrates a way to verify how bad our branch prediction intuition truly is. The code still works like a charm, mostly untouched. Let's see what we can do with it.

# TLDR
Using [this working example](https://godbolt.org/z/EqPonc) and a sneaky `#define if(expr) if(likely((expr)))` we can explore how often our branches are really being taken.

![BRANCHPRED](/img/branchpred_test.png)

# The Code
{% gist cdc01bf9a8f141ed37065ee5cdd4ef89 %}

Essentially the above will generate a section (a block of instructions and data in your final executable, kind of) in your assembly for each branch that you instrument. In each section we allocate two 8-byte counters (`.quad 0; .quad 0`). The [%=](https://gcc.gnu.org/onlinedocs/gcc/Extended-Asm.html) will generate a unique identifier and append it to the section name (so each branch will have its own counter). Similarly, we store the line number and filename of where the branch occurred.

The meat is in the last line of assembly. We increment one of the two counters by:
```
section_address = addressOf(predictctn) + 8 * (prediction == outcome)
*section_address += 1
```

I.e., if we predicted incorrectly, the first counter is chosen and incremented. Otherwise we skip 8-bytes to the second counter (thanks to having organized our data in sections).

**Note**: the original code had to be slightly modified to store the expression outcome in a separate variable (`_b`) before passing it to the inline assembly block. Otherwise, on one of the tested systems GCC 10.2 complained about an invalid index expression.

To access the counters we rely on some linker magic. First we want to make sure we create the sections we reference in the inline assembly above somewhere in our code. We mark the `predict_data` section as writable (`w`) so we can update the counters. The linker will have generated `__start` and `__stop` variables corresponding to our section names. Then we simply run through the counters we stored for each branch (we ensured contiguous counts inside the `predict_data` section using `.pushsection`).

{% gist 8092ffe8423ba4ef6257a7b04463634f %}

## Compiling
We assume a 64-bit architecture and that your project is assembled and linked using the GNU assembler and linker respectively. In the original snippet, U. Drepper accounted for 32-bit systems as well.

On some systems you will have to tell GCC not to produce a position independent executable (PIE) using: `-no-pie`.

The C++ standard forbids the expression grouping we did in the `debugpred__` define. For us it's a non-issue since GCC supports it.

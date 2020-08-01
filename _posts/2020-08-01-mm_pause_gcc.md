---
layout: post
published: false
title: Possible _mm_pause assembly regression on GCC
date: '2020-08-01'
---

While homebrewing some spinlocks I discovered an interesting possible regression in the compilation of the [_mm_pause](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#text=_mm_pause&expand=4141,4141) intrinsic on GCC. On supported architectures this intrinsic should translate to a [PAUSE](https://c9x.me/x86/html/file_module_x86_id_232.html) instruction, which can be used to stop CPU pipeline flushes in typical spin-locks.

The assembly generated on GCC trunk adds a mysterious `NOP` after each `PAUSE`: [assembly](https://godbolt.org/z/PrWxrT)
The last version of GCC on Godbolt which "correctly" does this translation is GCC 8.3: [assembly](https://godbolt.org/z/666sGo)

On GCC trunk the following snippet:

```c++
#include <emmintrin.h>

void test()
{
    while(true)
    {
        _mm_pause();
        _mm_pause();
        _mm_pause();
        _mm_pause();
        _mm_pause();
    }
}
```

translates to:

```
test():
 push   rbp
 mov    rbp,rsp
 pause
 nop
 pause
 nop
 pause  
 nop
 pause  
 nop
 pause  
 nop
 jmp    401106 <test()+0x4>
main:
 push   rbp
 mov    rbp,rsp
 mov    eax,0x0
 pop    rbp
 ret    
 nop    WORD PTR cs:[rax+rax*1+0x0]
 nop    DWORD PTR [rax+0x0]
```

Note how we have a `nop` in between the `pause` instructions which we can't map back to the source snpipet.

Why this is happening will need some digging inside GCC. Check back in the next couple of weeks and there might be answer why this change ever occurred and whether it truly is a regression or not.

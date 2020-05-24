---
layout: post
published: true
title: Linux adsprpc Driver Perf Infrastructure
date: '2020-05-23'
---
CPU statistics are readily available to the user. However, workloads on mobile phones run across dozens of other hardware components. To reason about the behaviour of IP blocks on mobile phones something along the lines of performance counters would go a long way. Below we the outline performance counter infrastructure built into the Linux MSM Kernel DSP driver. Since it is done on an adhoc basis between drivers, we outline the same for other hardware components in future blog posts.

The Linux [adsprpc driver](https://android.googlesource.com/kernel/msm/+/android-msm-flo-3.4-kitkat-mr1/Documentation/arm/msm/adsprpc-drv.txt) dictates communiction between applications and a DSP. Most of the driver source sits in [adsprpc.c](https://android.googlesource.com/kernel/msm/+/android-msm-marlin-3.18-nougat-dr1/drivers/char/adsprpc.c). The kernel implements the adsp subsystem as a character device driver. User-space applications can invoke the DSP using the IOCTL interface.

# Infrastructure
- Available counters: these are enumerated in `enum fastrpc_perfkeys`
  1.  `PERF_COUNT`   : bookkeeping
  2.  `PERF_FLUSH`   : cache flushes
  3.  `PERF_MAP`     : fastrpc mmap calls and ion mappings
  4.  `PERF_COPY`    : page copies
  5.  `PERF_LINK`    : fastrpc_invoke_send calls (i.e., smd_write and glink_tx calls)
  6.  `PERF_GETARGS` : get_args calls
  7.  `PERF_PUTARGS` : put_args calls
  8.  `PERF_INVARGS` : inv_args or inv_args_pre calls
  9.  `PERF_INVOKE`  : complete fastrpc_internal_invoke call
  10. `PERF_KEY_MAX` : bookkeeping (denotes last perf key entry)
- Each of the above enum entries is actually an offset used to look up and store performance counters in a `struct fastrpc_perf`
- The `struct fastrpc_file` file descriptor stores a list `struct fastrpc_perf` per process ID
- Each performance counter in `struct fastrpc_perf` is an `int64_t`
- The `#define PERF` macro encloses a block of code and times it using [getnstimeofday](https://www.kernel.org/doc/html/latest/core-api/timekeeping.html#c.getnstimeofday)
  - The result is stored into the appropriate key in `struct fastrpc_perf`
- The `#define GET_COUNTER(perf_ptr, offset)` macro uses pointer arithmetic to retrieve a perf key from a `struct fastrpc_perf` pointer
  - `perf_ptr` is a `struct fastrpc_perf*`
  - `offset` is one of the enum values above
    - Since the struct consists of consecutive `int64_t`s, an increment of a `perf_ptr` allows consecutive access of the individual counters
- `getperfcounter` is used to retrieve an element of the appropriate `struct fastrpc_perf` from the file descriptor
  - It iterates through the `struct fastrpc_perf` list to find the structure that was created for the current current process ID
  - It then returns the offset into the found structure using the enum key
- A user-space application can use the `FASTRPC_IOCTL_GETPERF` ioctl command to retrieve performance counters
  - It does essentially the same as `getpercounter` above but copies out some additional information about available performance counters to the user

# Invocation
An example program to retrieve information about DSP performance counters is as follows:
```c
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/ioctl.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

struct fastrpc_ioctl_perf
{
    /* kernel performance data */
    uintptr_t data;
    uint32_t numkeys;
    uintptr_t keys;
};

struct hlist_node {
    struct hlist_node *next, **pprev;
};

struct fastrpc_perf {
        int64_t count;
        int64_t flush;
        int64_t map;
        int64_t copy;
        int64_t link;
        int64_t getargs;
        int64_t putargs;
        int64_t invargs;
        int64_t invoke;
        int64_t tid;
        struct hlist_node hn;
};

#define FASTRPC_IOCTL_GETPERF _IOWR( 'R', 9, struct fastrpc_ioctl_perf )

int main( void )
{
    int fd;
    struct fastrpc_ioctl_perf data;

    fd = open( "/dev/adsprpc-smd", O_RDWR );

    if( fd == -1 )
    {
        perror( "open" );
        return -1;
    }

    memset( &data, 0, sizeof( struct fastrpc_ioctl_perf ) );

    struct fastrpc_perf frpc_d;
    memset( &frpc_d, 0, sizeof( struct fastrpc_perf ) );
    data.data = (uintptr_t)&frpc_d;

    char* keys = malloc( sizeof( char ) * 1024 );
    data.keys  = (uintptr_t)&keys;

    ioctl( fd, FASTRPC_IOCTL_GETPERF, &data );
    printf( "keys: %lu\n"
            "numkeys: %u\n"
            "data: %lu\n",
            data.keys, data.numkeys, data.data );

    close( fd );

    printf( "Data content: %ld %ld %ld\n", ((struct fastrpc_perf*)data.data)->count,
                                           ((struct fastrpc_perf*)data.data)->flush,
                                           ((struct fastrpc_perf*)data.data)->tid );

    printf("Keys: %s\n", (char*)data.keys);

    return 0;
}
```

# Allocators
This is where I am putting my allocators that I make. Currently has two kinds of allocators: greedy arena and lazy arena.

Greedy arena will do one transaction once and only once and that transacted memory will persist until the end of the allocator's lifetime. So I call arena_put, and give it a pointer to some data I wanna put it the allocator, and give it a size to allocate with, then that data will never be deallocated/deinitialized without the entire allocated being deinitialized/deallocated. It's a "write once, free all" kind of mechanism. This is sorta your typical arena allocator. This allocator is monotonic in nature.

```c
#include "arena.h"

int main(){
    arena_alloc* arena = arena_init(1024);
    int data = 5;
    int* pdata = (int*)arena_put(arena, &data, sizeof(int));
    //...
    arena_deinit(arena);
    return 0;
}
```

Lazy arena will allow you to put data freely anywhere within the arena. Regardless of size, as long as the size is within the arena's size, you can put data anywhere anytime. This is called lazy because it does not keep your data, you can write 0 to any of the offsets at any time. Using lazy_arena_put with a given lazy_arena pointer, you can put any data of any size at any offset from the start of the arena.

```c
#include "lazy_arena.h"

int main(){
    lazy_arena_alloc* lazy_arena = lazy_arena_init(1024);
    int data = 5;
    //Put `data` at offset 10
    int* pdata = (int*)lazy_arena_put(lazy_arena, 10, &data, sizeof(int));
    //...
    lazy_arena_deinit(lazy_arena);
    return 0;
}

```
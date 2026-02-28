# C Symbol Typing Drills

> These drills are for when you can already touch-type the alphabet.
> Do these during weeks 7–10 alongside the week plan drills.
> Go slow. One missed brace is more costly than any WPM number.

---

## Level 1: Individual Symbol Isolation

Type each line 3 times without looking. Stop and fix your finger position if you miss.

### Braces and Brackets
```
{ } { } { } { } { } { } { } { }
[ ] [ ] [ ] [ ] [ ] [ ] [ ] [ ]
( ) ( ) ( ) ( ) ( ) ( ) ( ) ( )
{ [ ( ) ] } { [ ( ) ] } { [ ( ) ] }
```

### Semicolons, Colons, and Asterisks
```
; ; ; ; ; ; ; ; ; ; ; ; ; ; ; ;
: : : : : : : : : : : : : : : :
* * * * * * * * * * * * * * * *
*ptr; *ptr; *ptr; *ptr; *ptr;
```

### Comparison Operators
```
== == == != != != <= <= <= >= >= >=
< > < > < > == != == != <= >=
x == 0; x != 0; x <= n; x >= 0;
```

### Pointer Operators
```
-> -> -> -> -> -> -> -> -> ->
& & & & & & & & & & & & & &
*p; &x; p->field; (*p).field;
int *ptr = &var;
```

---

## Level 2: C Expression Patterns

Type these exactly. Time yourself after a few sessions.

### Declarations
```c
int x;
int *ptr;
int **pptr;
const char *s;
char *argv[];
void (*callback)(int, void *);
int arr[256];
unsigned char buf[MAX_SIZE];
```

### Struct Patterns
```c
struct point {
    int x;
    int y;
};

typedef struct node {
    int value;
    struct node *next;
} Node;

struct foo *bar = malloc(sizeof(*bar));
if (bar == NULL) { return -ENOMEM; }
bar->value = 0;
bar->next = NULL;
free(bar);
```

### Control Flow
```c
if (x == NULL) {
    return -1;
}

if (ret < 0) {
    fprintf(stderr, "error: %d\n", ret);
    goto cleanup;
}

for (int i = 0; i < n; i++) {
    arr[i] = 0;
}

while (p != NULL) {
    p = p->next;
}

switch (state) {
case STATE_INIT:
    init();
    break;
case STATE_RUN:
    run();
    break;
default:
    return -EINVAL;
}
```

### Preprocessor
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdint.h>
#include <stdbool.h>
#include "myheader.h"

#define MAX_SIZE    256
#define ARRAY_SIZE(a) (sizeof(a) / sizeof((a)[0]))
#define MIN(a, b)   ((a) < (b) ? (a) : (b))
#define MAX(a, b)   ((a) > (b) ? (a) : (b))

#ifndef MYHEADER_H
#define MYHEADER_H

/* ... */

#endif /* MYHEADER_H */

#ifdef DEBUG
#define DBG(fmt, ...) fprintf(stderr, fmt "\n", ##__VA_ARGS__)
#else
#define DBG(fmt, ...) do {} while (0)
#endif
```

---

## Level 3: Full Functions

Type these complete functions from memory after enough practice, or just transcribe them touch-style.

### Memory management
```c
static char *xstrdup(const char *s)
{
    char *copy = malloc(strlen(s) + 1);
    if (copy == NULL) {
        fprintf(stderr, "out of memory\n");
        exit(EXIT_FAILURE);
    }
    return strcpy(copy, s);
}

void *xcalloc(size_t n, size_t size)
{
    void *p = calloc(n, size);
    if (p == NULL) {
        perror("calloc");
        exit(EXIT_FAILURE);
    }
    return p;
}
```

### Bit manipulation
```c
static inline uint32_t rotate_left(uint32_t v, int n)
{
    return (v << n) | (v >> (32 - n));
}

static inline bool is_power_of_two(unsigned long n)
{
    return n != 0 && (n & (n - 1)) == 0;
}

static inline int count_bits(uint32_t v)
{
    int count = 0;
    while (v) {
        count += v & 1;
        v >>= 1;
    }
    return count;
}
```

### String handling
```c
int safe_snprintf(char *buf, size_t size, const char *fmt, ...)
{
    va_list ap;
    int ret;

    va_start(ap, fmt);
    ret = vsnprintf(buf, size, fmt, ap);
    va_end(ap);

    if (ret < 0 || (size_t)ret >= size) {
        buf[size - 1] = '\0';
        return -1;
    }
    return ret;
}
```

---

## Level 4: Symbols That Trip People Up

These specific patterns cause the most hesitation. Drill them.

### Double operators
```
++ -- += -= *= /= %= &= |= ^= <<= >>=
i++; i--; --i; ++i;
x += 5; y -= 3; z *= 2;
flags |= NEW_FLAG;
mask &= ~CLEAR_BIT;
value >>= 4;
```

### Ternary operator
```c
int max = (a > b) ? a : b;
const char *s = (ptr != NULL) ? ptr : "(null)";
return (x >= 0) ? x : -x;
size_t len = (n > MAX) ? MAX : n;
```

### Function pointers
```c
int (*fn)(int, int);
fn = &add;
int result = fn(3, 4);

typedef int (*compare_fn)(const void *, const void *);
void sort(void *base, size_t n, size_t size, compare_fn cmp);

struct ops {
    int (*init)(struct device *dev);
    void (*cleanup)(struct device *dev);
    ssize_t (*read)(struct device *dev, char *buf, size_t len);
};
```

### Cast patterns
```c
uint8_t *bytes = (uint8_t *)ptr;
size_t n = (size_t)count;
struct foo *f = (struct foo *)malloc(sizeof(*f));
int val = (int)(float_val + 0.5f);
void *p = (void *)((uintptr_t)base + offset);
```

---

## Timing Benchmarks

Use these to measure progress. Time how long to type each block (start to finish, no looking, acceptable accuracy):

**Beginner benchmark** (aim for <60 seconds in week 7):
```c
int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "usage: %s <file>\n", argv[0]);
        return 1;
    }
    return 0;
}
```

**Intermediate benchmark** (aim for <90 seconds in week 9):
```c
typedef struct node {
    int value;
    struct node *next;
} Node;

Node *list_create(int value) {
    Node *n = malloc(sizeof(*n));
    if (n == NULL) return NULL;
    n->value = value;
    n->next = NULL;
    return n;
}

void list_free(Node *head) {
    while (head != NULL) {
        Node *next = head->next;
        free(head);
        head = next;
    }
}
```

**Advanced benchmark** (aim for <120 seconds in week 12):
```c
#include <stdlib.h>
#include <string.h>
#include <stdint.h>

#define HASH_SIZE   256
#define HASH(key)   ((uint8_t)(key[0] ^ key[strlen(key)-1]))

typedef struct entry {
    char *key;
    void *value;
    struct entry *next;
} Entry;

typedef struct {
    Entry *buckets[HASH_SIZE];
    size_t count;
} HashMap;

HashMap *hashmap_create(void) {
    HashMap *map = calloc(1, sizeof(*map));
    return map;
}

int hashmap_set(HashMap *map, const char *key, void *value) {
    uint8_t h = HASH(key);
    Entry *e = map->buckets[h];
    while (e != NULL) {
        if (strcmp(e->key, key) == 0) {
            e->value = value;
            return 0;
        }
        e = e->next;
    }
    e = malloc(sizeof(*e));
    if (e == NULL) return -1;
    e->key = strdup(key);
    e->value = value;
    e->next = map->buckets[h];
    map->buckets[h] = e;
    map->count++;
    return 0;
}
```

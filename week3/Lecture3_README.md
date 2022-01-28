# Software Engineering For Embedded Applications

## Lecture 3 (January 20, 2022) - C Programming Language II - Dynamic Arrays. Abstract Data Type.

---

Last week, we (re)introduced the C language. Today, we'll continue exploring C programming language, to make a simple application. We will then talk about dynamic allocation and dynamic arrays in C. Finally, we will talk about Abstract Data Types (ADTs).

# The C Programming Language

## Review: Types in C

### Integer Types in C
In C, every variable, function argument, and function return value needs to have a declared type. 

One of the basic types in C is `int`, which refers to a positive, zero, or negative integer. The following bit of code uses the `int` type to define a function that takes an `int` argument, has two local `int` variables, and returns an `int` value.

```c
int sum(int n) {
  int i,
      s = 0;
  for (i=0; i<=n; i++) {
    s = s + i;
  }
  return s;
}
```

The following code demonstrates most of the basic integer types in C along with some initializations showing what sorts of values they take. Note that character values such as `'a'`, `'b'` and `'c'` are shorthand for ASCII integer values, in this case `97`, `98`, and `99`.

```c
char a = 97;             /* one byte */
unsigned char b = 'b';
signed char c = -99;
short d;                 /* two bytes */
unsigned short e;
int f;                   /* two or four bytes */
unsigned int g;
long h;                  /* four bytes */
unsigned long i;
```

To see how many bytes a type has, you can use the `sizeof` function, as in

```c
printf("The size of an int on this machine is %d\n", sizeof(int));
```

### Float Types in C

In addition , the following floating point types are available:

```c
float x;                 /* four bytes, 6 decimal places     */
double y;                /* eight bytes, 15 decmial places   */
long double z;           /* sixteen bytes, 19 decimal places */
```

You trade storage space and speed as you increase the size of these types.

### The void Type

In C, an object with type `void` has no value. Usually we use `void` to refer to functions that do not return anything, such as

```c
void say_hello() {
  printf("hello\n");
  return;
}
```

Note that you cannot declare a variable of type `void`.

## Review: Modifiers

### Modifiers: `register`

This keyword is used to strongly suggest to the compiler that the variable should be stored in a register instead of in RAM. You would do something like this:

```c
void f(int x) {
  register int i;
  for(i=0; i<10; i++) {
    /* do something */
  }
}
```

However, most compilers know how to figure out when to use a register for a counter without the `register` keyword, so you will almost never need to use this modifer.

### Modifiers: `volatile`

This keyword is used to refer to locations in memory that might change do to something happening outside of the code. It prevents the compiler from assuming that a volatile variable that is assigned only once in the code will never change and subsequently optimizing it out of existence. For example, on an embedded device, if you know that location 0x5555 was a globally available register being written to by, for example, a sensor or interrupt handler, then you could do

```c
voif f() {
  volatile int * x = 0x5555; /* x is a pointer to the
                              * location 0x5555 (see pointers
                              * below) */
  while ( *x == 0 ) {
    /* wait */
  }
  /* do something because *x changed,
    * presumably due to some outside event */
}
```

### Modifiers: `static`

A static variable is one that preserves its value even after it has gone out of scope. For example, compare the following two functions

```c
int f() {
  int x = 0;
  x++;
  return x;
}

int g() {
  static int x = 0; /* note: must be a literal constant,
                      *  not a computed value */
  x++;
  return x;
}
```

If you call `f` twice in a row, you will get the value `1` each time. If you call `g` twice in a row, you will get `1` the first time and `2` the second time. Thus, the function `g` is using `x` as local storage. It initializes the variable the first time it is called, but does not reinitialize it upon subsequent calls to the function.

### Modifiers: `static` within a File

The `static` keyword is also used in a _totally different way_ to declare variables and functions as local to the file in which they are defined. If a function is defined without static, as in

```c
int f(int x) {
  return x+1;
}
```

then it is globally available to your code (assuming your code includes its declaration). If a function is defined as static, then it is only available in that file:

```c
static int f(int x) {
  return x+1;
}
```

If you put this in a file called `my_source.c`, then only codw within `my_source.c` can use the function `f`. This is a way to make private functions that you do not want users of an API to access.

### Modifiers: `const`

This keyword is used in variable declarations to make symbols that refer to constants. For example

```c
int f() {
  const double PI = "3.14159";
  /* etc */
}
```

The compiler will complain if you attempt to reassign `PI` later in the code for `f`.

The use of `const` gets complicated when combined with pointers (see below). In short, you can define a constant pointer with

```c
int * const ptr;
```

and a pointer to a constant value with

```c
const int * ptr;
```

### Modifiers: `const` Arguments

If you have a function that takes a pointer to a value and want to enforce that the function does not modify the value pointed to, then you would define the argument to the function as follows:

```c
void f(const int * p) {
  /* do things like print *p but don't
    * modify it */
}
```

The following example will produce a compile error because the function attempts to change the value pointed to by `x`.

```c
int f ( const int * x ) {
    *x = *x + 1;
    return *x;
}
```

Compiling this code gives the error

```bash
  error: assignment of read-only location '* x'
  *x = *x + 1;
    ^
```

### Modifiers: `extern`

This keyword is used in certain circumstances to declare functions without defining them, and to tell the compiler to go ahead and link your code expecting that the function will be defined somewhere else. We will likely not need it, although you will see it a lot in header files we include.

## Structures

A structure in C is like a record or dictionary in other languages. It is a way to define a new type of object to store information that belongs together. For example, you might use the following structure to keep track of the information associated with a new data type you are defining,called point, and then declare some points.

```c
struct point {
  double x, y, z;
};
struct point p, q;
```

You can then refer to the components of a point with the `.` notation as in `p.x` or `q.z`. If you do not name the `struct` then you can declare `p` and `q` directly, but then cannot declare more structs of the same type:

```c
struct {
  double x, y, z;
} p, q;
```

## Typedef with `struct`

If you would like to avoid having to write `struct point` over and over, you can also make a type definition as in the following example:

```c
typedef struct point {
  double x, y, z;
} Point;
Point p, q;
```

which also delcared `p` and `q` to be of type `struct point`.

You can initialize a struct using either of the following notations:

```c
Point p = { .x = 1.0, .y = 2.0, .z = 3.0 };
Point q = { 1.0, 2.0, 3.0 };
```

The order in which the members of the struct were declared determines the order in which the initializers should appear.

## Unions

A `union` is like a `struct`, but with all members using the same memory location. A `union` allows you to use only **one** of the members of the union at the same time. For example,

```c
typedef union thing {
  int x;
  double y;
  float z;
} Thing;
Thing t;
```

In this case, the addresses in memory of `t.x`, `t.y` and `t.z` are all the same. If we replaced the above with a `struct`, they would all be different.

## Enums

An `enum` is a way to enumerate the possible values a variable might have. For example

```c
typedef enum status {
  IDLE, RUNNING, ERROR
} Status;
Status x = IDLE;
```

defines a variable of type `Status` whose values are either `IDLE`, `RUNNING` or `ERROR`. These values are _not_ strings. They are symbols. However, in C (but not in C++), the compiler actually just uses the integers 0, 1, 2, ... internally to represent the values in an enum. Thus, you will notice that you can treat them like integers, but you should make every effort not to do so, since other compilers may use different numbers to represent an enum's values.

# Pointers

The most difficult aspect of C is its use of pointers, which most other higher level languages like Python, Java or Javascript do not have. When you declare a variable, the C compiler has to store it in memory somewhere. The location in memory of the value of a variable is called its _address_. So a pointer variable is a variable whose value is an address.

There are two operators in C that you use to change back and forth between a variable's value and its address. The first is the `&` operator, which finds a variable's address. For example,

```c
int p = 1;
printf("The value of p is %d and the address of p is %x.\n", p, &p);
```

which would print out something like:

```bash
The value of p is 1 and the address of p is e5788eac.
```

The hexadecimal number e5788eac is the physical address in memory of the first byte of the integer `p`.

# Dereferencing

The second operator is the `*` operator, which dereferences a pointer and is also used to declare a pointer. For example, suppose `p` and `ptr` were declared as follows:

```c
int p = 1;
int * ptr = &p;
```

then `p` is an integer and `ptr` is the address of `p` in memory. If you would like to get the value pointed to by `ptr`, then you would use the syntax `*p`. For example,

```c
int q = *ptr; /* q is 1 */
*ptr = 2;     /* indirectly changes p to 2 */
```

# Pointers to `structs`

One of the places pointers are used extensively in C is with pointers to structs. This is because a struct can be quite large, and passing structs around by copying everything in them is a waste of time and space. When you have a variable that is a pointer to a struct, then you use the `->` operator instead of the `.` operator, as in the following example:

```c
typedef struct {
  double x, y, z;
} Point;
Point * ptr;
ptr->x = 3;
```

Actually, `ptr->x` is really just shorthand for

```c
(*ptr).x
```

but is more preferred.

# Pointers to functions

You can also define pointers to functions. The syntax is tricky. For example, the following defines a function `add` and a pointer `f_ptr` to it.

```c
int add(int n, int m) {
  return n+m;
}
int (* f_ptr) (int,int);
f_ptr = add;
```

You can use this syntax to send functions to other functions as arguments. For example, the following function applies a function to a value and returns the value.

```c
int apply(int (*f) (int), int x) {
  return f(x);
}
```

# Arrays

Arrays in C are contiguous regions of memory that contain strings of values.
Arrays can be declared with the `[]` operator as follows:

```c
int x[10];                             /* an array of 10 integers */
Point plist[20];                       /* An array of 20 Point structures */
double y[] = { 2.1, 3.2, 4.3, 5.4, };  /* Array of four doubles with initial values */
```

Arrays are zero indexed. Elements can be assigned and retrieved using the `[]` operator as well. For example,

```c
x[0] = 1;
x[1] = x[0];
plist[5] = (Point) { 3.1, 4.1, 5.9 };
y[3] = plist[5].y;
```

In the above cases, `x`, `plist` and `y` are just pointers to the beginning of the memory location for the arrays they represent. The `[]` operator is just shorthand for pointer arithmetic. Thus, the above code is equivalent to the following:

```c
*x = 1;
*(x+1) = *(x);
*(plist + 5) = (Point) { 3.1, 4.1, 5.9 };
*(y+3) = (plist+5)->y;
```

# Bound Checking

**Warning**: Arrays in C are not bounds checked. For example, the following code may compile just fine, but in fact contains a serious error.

```c
int a[10];
a[12] = 5;
ASSERT_EQ(a[12], 5);
ASSERT_EQ(a[13], 0);
```

This compiles and runs without error in the `cppenv` container, but it is just luck. The memory locations at `a+12` and `a+13` just happen to be unused. If you do try to write to `a[1000]` or larger, you will almost certainly encounter either

- a segmentation fault, meaning that you wrote a value outside the memory reserved by the OS for your application;
- strange behavior, meaning you wrote a value to some other data structure's portion of memory.
  To catch errors like this, you can use a tool called 'Address Sanitizer'. To use it, we modify the Makefile as follows

```c
CFLAGS      := -fsanitize=address -ggdb
LIB         := -lgtest -lpthread -lasan
```

Now, the code still compiles, but when you run it you get all sorts of useful error information from `asan`.

# Memory Allocation

When you declare arrays as with the above, you know at compile time how big they should be. However, often you do not know this, and may also need to write functions that return arrays. To dynamically allocate memory in C, you use the functions `malloc` and `calloc`, which are available in `stdlib`. For example, to dynamically allocate memory for 10 doubles, you can do:

```c
double * a = (double *) malloc(10*sizeof(double));
int * b = (int *) malloc(15*sizeof(int));
char * c = (char *) malloc(5*sizeof(char));
```

or

```c
double * a = (double *) calloc(10,sizeof(double)); /* also inits array to zeros */
```

Now `a` can be used just like a normal array, with elements `a[0]`, `a[1]` and so on. Note that 'malloc' and 'calloc' return `void *` pointers, because they do not have any way of knowing what type of array you want. Thus, we have to _type cast_ or _coerce_ the value returned into the correct type. That is what the `(double *)` notation does.

# Deallocation

It is important to note that if you declare a pointer and allocate memory for it in a function, then the pointer disappears when the function is complete, but the memory allocated does not. Thus, when you are done using the memory, your code must give the memory back to the operating sytem using the `free` function, also in `stdlib.h`. For example,

```c
void f() {
  int * a = (int *) calloc(1000,sizeof(int));
  /* do stuff with a */
  free(a);
}
```

This issue becomes particularly important when you use functions that allocate memory for you. For example, here is a function that joins two arrays together into a new array:

```c
int * join(const int * a, int length_a, const int * b, int length_b) {
    int * c = (int *) calloc(length_a+length_b, sizeof(int));
    for (int i=0; i<length_a; i++ ) {
        c[i] = a[i];
    }
    for (int i=0; i<length_b; i++ ) {
        c[i+length_a] = b[i];
    }
    return c;
}
```

# The Allocation / Free Pattern

To use this function and then free the result, you might do

```c
TEST(Examples,AlocateAndFree) {
    int a[] = { 0, 1, 2 };
    int b[] = { 3, 4, 5 };
    int * c = join(a, 3, b, 3);
    for (int i=0; i<6; i++ ) {
        ASSERT_EQ(c[i], i);
    }
    free(c);
}
```

Repeated failure to free the result of `join` would result in a _memory leak_, which will eventually use up all the RAM on your computer, causing a crash. These are hard to catch. Memory leaks are in fact one of the biggest issues plaguing modern software. Modern languages have _garbage collectors_ to clean up unused memory, but (a) they don't work in every case and (b) they are written in C or C++ by humans who make mistakes.

# Strings are arrays of chars

Strings in C are contiguous regions of one byte memory addresses whose values are usually interpreted as characters. To declare and initialize strings, you do something like:

```c
char x[] = "Hi";
char y[3] = { 'H', 'i', '\0' };
char z[3];
char z[0] = 'H';
char z[1] = 'i';
char z[2] = '\0';
```

The special character `\0` is called the **null** character, and it ris used to terminate strings so that functions that manipulate them know when to stop.

When you declare a string within a scope, its memory is allocated for the duration of that scope. If you want a string that lasts a long time, you might have to allocate it yourself, in which case you would just treat it as an allocated array of characters.

# Abstract Data Type (ADT)

An _Abstract Data Type_ (ADT) is a set of values and methods or operations that operate on those values. The interface to an ADT defines only how to build values and operate on them, but not how memory is allocated or how the operations are performed. ADTs are extremely useful to programmers. They allow you to

- avoid "reinventing the wheel" (and the explosion of many incompatible implementations of the "wheel")
- separate concerns: your user is concerned with other problems than how you implement array methods
- write cleaner code and
- provide a contract between you and other programmers.

High level, interpreted languages, such as Python or Java, come with built-in ADTs for types such as arrays, lists, stacks, dictionaries, and even functions. However, C does not have any such nicities. Many code libraries have been build in C and C++ to provide these ADTs to C programmers. This week, we will see how to build our own ADT for arrays. Later in the course, we will use more complex ADTs for data types found in embedded systems such as processes, schedulers, finite state machines, sensors, and effectors.

# Problems with C Arrays

Nothing makes you want an ADT more than raw C arrays. Here are some of the problems with arrays in C:

- Nothing prevents you from getting or setting outside of the memory allcoated for the array.
- An array does not know its own length.
- Memory allocation should not be an array method.

# An Array API

Following many standard abstract array types in other languages, we will implement an array type with the following operations on our array ADT:

- Make a new, empty, array.
- Destroy an array.
- Set elements of the array, as in `my_array[12] = 34`. Any non-negative index should be accepted.
- Get elements of the array, as in `x = my_array[54]`. Any non-negative index should be accepted, with default values for out of bounds queries.
- Return the size of the array.
- Push new values onto the beginning and ending of the array.
- Pop values from the beginning and ending of the array.
- Concatenate two arrays.
- Map functions onto arrays to get new arrays.
- Iterate through an array.
- Print an array.

# Naming and argument conventions

In languages with build-in ADTs, the convention is to access a method like `push` of an array `a` with the notation:

```c
a.push(1)
```

In C, the `dot` notation does not allow such notation. Thus, all of our methods will be declared as stand alone functions in the global namespace. To avoid naming collisions, we will use the following convention:

- Naming methods by the data type name followed by an underscore followed by the method name;
- Putting a pointer to the datum being operated upon as the first argument of the method (except for the constructor):
  For example,

```c
DynamicArray * da = DynamicArray_new();
DynamicArray_push(da, 1);
```

# Some Getting and Setting Tests

```c

    TEST(DynamicArray, SmallIndex) {
        DynamicArray * da = DynamicArray_new();
        ASSERT_EQ(DynamicArray_size(da),0);
        DynamicArray_set(da, 0, -X);
        DynamicArray_set(da, 3, X);
        ASSERT_EQ(DynamicArray_size(da),4);
        ASSERT_EQ(DynamicArray_get(da,0), -X);
        ASSERT_EQ(DynamicArray_get(da,3), X);
        DynamicArray_destroy(da);
    }

    TEST(DynamicArray, BigIndex) {
        DynamicArray * da = DynamicArray_new();
        DynamicArray_set(da, 8, X);
        ASSERT_EQ(DynamicArray_get(da,8), X);
        DynamicArray_destroy(da);
    }

    TEST(DynamicArray, ReallyBig) {
        DynamicArray * da = DynamicArray_new();
        DynamicArray_set(da, 400, X);
        DynamicArray_set(da, 200, X/2);
        ASSERT_EQ(DynamicArray_get(da,200), X/2);
        ASSERT_EQ(DynamicArray_get(da,400), X);
        DynamicArray_destroy(da);
    }
```

# A Push Test

```c

    TEST(DynamicArray, Push) {
        DynamicArray * da = DynamicArray_new();
        double x = 0;
        while ( x < 10 ) {
            DynamicArray_push(da, x);
            x += 0.25;
        }
        ASSERT_EQ(DynamicArray_size(da),40);
        printf("Push test Intermediate Result: %s\n",
               DynamicArray_to_string(da));
        while ( DynamicArray_size(da) > 0 ) {
            DynamicArray_pop(da);
        }
        ASSERT_EQ(DynamicArray_size(da),0);
        DynamicArray_destroy(da);
    }
```

# Internal Memory Allocation

We do not want the user of `DynamicArray` to worry about memory allocation. We also want array operations like getting, setting, pushing and popping to be fast, so reallocating the memory associated with a DynamicArray should happen rarely. To achieve these goals, we will use the following scheme:

![memory](https://raw.githubusercontent.com/klavins/EEP520-W20/master/week_3/images/dynamic_array_arbitrary.png)

- Start out with a buffer of given length, say 100. Call this the `capacity`.
- Keep track of two indices into the buffer, `origin` and `end`.
- Initially put `origin = end = capacity / 2`.
- As elements are added beyong `end`, change `end` to include them.
- If an element is pushed to the front, decement `origin`.
- If an element is added that is beyond the capacity, increase the buffer size by 2.

# A Dynamic Array Object

We need a place to store all of the information associated with a `DynamicArray`, which we will put in a `struct` containing the associated information:

```c
typedef struct {
    int capacity,
        origin,
        end;
    double * buffer;
} DynamicArray;
```

which appears in the header file `DynamicArray.h`.

# The Constructor

Although users could user this `typedef` to make new values, they would need to know how much memory to allocate and how to initialize the `capacity` and so on. Functions that build or construct new ADT values are called _constructors_. Here is a constructor for `DynamicArray`:

```c
DynamicArray * DynamicArray_new(void) {
    DynamicArray * da = (DynamicArray *) malloc(sizeof(DynamicArray));
    da->capacity = DYNAMIC_ARRAY_INITIAL_CAPACITY;
    da->buffer = (double *) calloc ( da->capacity, sizeof(double) );
    da->origin = da->capacity / 2;
    da->end = da->origin;
    return da;
}
```

Note that it takes new arguments are returns a pointer to a new `DynamicArray` object. It also refers to a constant that determines the initial capacity and that is defined in the header file.

# The Destructor

Once a user is done with the array, we need to free the memory associated with it. We do not want the user to call `free(da->buffer)`, because that would imply they knew about how the memory was allocated in the first place. Thus, we provide a \*destructor` as follows:

```c
void DynamicArray_destroy(DynamicArray * da) {
    free(da->buffer);
    da->buffer = NULL;
    return;
}
```

The user can thus write

```c
DynamicArray * da = DynamicArray_new();
DynamicArray_push(da, 1);
DynamicArray_destroy(da);
free(da);
```

Note the user has to do two things. First, tell the array to free its own internal memory. Second, the user has to free the struct associated with the array. If they just call `free(da)` with, then the pointer `da->buffer` will be deleted, but not the memory it points to! That would be a serious (but not uncommon) memory leak.

# Assertions

The buffer pointer is set to `NULL`, which is usually the address `x0000000` in memory. We will use this fact to test whether a given `DynamicArray` is valid. That is, suppose the user made a new array, then destroyed it but did not free it, and then tried to push something onto it. That would result in the user pushing something to the address `NULL + end`, which would be a memory violation. To avoid this happening, we will add an assertion to our methods that states that the buffer cannot be `NULL` and if it is, to produce a runtime error. We do this with the C library `assert.h` as follows:

```c
/* in the header file */
#include <assert.h>

/* at the beginning of all method definitions */
assert(da->buffer != NULL);
```

If this assert fails, you would see a runtime error such as

```bash
test: dynamic_array.c:82: void DynamicArray_set(DynamicArray*, int, double): Assertion `da->buffer != __null' failed.
Aborted
```

# Testing for Runtime Errors

Google Test includes a method called "ASSERT_DEATH" which takes a statement and a regular expression to match against the text printed out upon the code exiting. You can use this to check that your code is exiting properly when it encounters an error.

```c
    TEST(DynamicArray, DeathTests) {
        DynamicArray * a = DynamicArray_new();
        ASSERT_DEATH(DynamicArray_pop(a), "");
        ASSERT_DEATH(DynamicArray_pop_front(a), "");
        DynamicArray_destroy(a);
        ASSERT_DEATH(DynamicArray_size(a), "");
    }
```

Later in C++ we will write "Exception Handlers" to more gracefully report errors to the user.

Without exceptions handlers, our other option is to do what we did with the RPN example, and have an error state that the user has to check after each operation.

# Size Getter

A getter is a method that "gets" information about an object. A simple bit of information about a `DynamicArray` is its size, for which we define the following method:

```c
int DynamicArray_size(const DynamicArray * da) {
    assert(da->buffer != NULL);
    return da->end - da->origin;
}
```

Note the assertion at the beginning of the method.

# Element Getter

The other obvious getter is the one that returns the value of the array at a given index. This is implemented as follows:

```c
double DynamicArray_get(const DynamicArray * da, int index) {
    assert(da->buffer != NULL);
    assert(index >= 0);
    if ( index > DynamicArray_size(da) ) {
        return 0;
    } else {
        return da->buffer[index_to_offset(da,index)]; /* TODO: Write index_to_offset method */
    }
}
```

It includes another assertion: that the index is non-negative. It also checks to see if the index as out of bounds (using the `_size` method), and if it is, then it returns zero. The alternative to the out of bounds index would be to throw an error, but since we have decided that arrays can grow without bounds, we have decided to return a default value. Zero is also the value that `calloc` uses to initialize the array buffer, so this choice seems reasonable.

# Private Methods

The element getter uses a `private` function that converts an index to a buffer offset. Because this conversion happens so often in our implementation, we've added them to the `dynamic_array.c` as follows:

```c
static int index_to_offset ( const DynamicArray * da, int index ) {
    return index + da->origin;
}

static int offset_to_index ( const DynamicArray * da, int offset ) {
    return offset - da->origin;
}
```

Sure, they are simple functions, but they clearly show in the `_get` code the relationship that the index to `da->buffer` is an offset in the buffer, and not an index in the `DynamicArray`. They are also declared statically and do not have prototypes in the header file, because we don't want our user to ever call them directly.

# Setters

A setter is a method that changes the value of an object. The main setter in the `DynamicArray` is the `_set` method, which simply changes the value at a given index. If we did not care about memory allocation, we could simply write:

```c
/* TODO: Check bounds, etc */
void DynamicArray_set(DynamicArray * da, int index, double value) {
    da->buffer[index_to_offset(da, index)] = value;
}
```

However, if the user supplies a large enough index, the computed offset will exceed the bounds of the buffer. Thus, we should check the bounds first and extend the buffer if necessary.

# Setting Carefully

```c
void DynamicArray_set(DynamicArray * da, int index, double value) {
    assert(da->buffer != NULL);
    assert ( index >= 0 );
    while ( out_of_buffer(da, index_to_offset(da, index) ) ) {
        extend_buffer(da); /* TODO: Write the extend buffer method */
    }
    da->buffer[index_to_offset(da, index)] = value;
    if ( index > DynamicArray_size(da) ) {
        da->end = index_to_offset(da,index);
    }
}
```

This method has three main steps. First, while the computed offset is out of bounds, extend the buffer. The extension is done with a help function described below. Putting the entire extension code in the `_set` method would make it very long and hard to read. Plus, there may be other opportunities to use the extend method and we do not want the same code showing up twice. Second, we set the element at the desired index to the specified value. Finally, we change the `end` index if needed.

# Extending the Buffer

To extend the buffer, we want to keep the original buffer roughly in the middle of the new buffer. Here is the scheme we will use.

The extend method makes a new buffer and then makes temporary `new_origin` and `new_end` variables. The values of these must take into account the fact that the new origin offset is not the same value as the old origin offset, since the capacity has increased. We also want to reposition the array into the middle of the buffer, as the following figure shows:

![memory](https://raw.githubusercontent.com/klavins/EEP520-W20/master/week_3/images/dynamic_array_extend.png)

# Code for Extending the Buffer

The extend method will be a private method, defined statically in the impelemntation file as follows:

```c
static void extend_buffer ( DynamicArray * da ) {

    double * temp = (double *) calloc ( 2 * da->capacity, sizeof(double) );
    int new_origin = da->capacity - (da->end - da->origin)/2,
           new_end = new_origin + (da->end - da->origin);

    for ( int i=0; i<DynamicArray_size(da); i++ ) {
        temp[new_origin+i] = DynamicArray_get(da,i);
    }

    free(da->buffer);
    da->buffer = temp;
    da->capacity = 2 * da->capacity;
    da->origin = new_origin;
    da->end = new_end;

    return;
}
```

The method allocates a temporary pointer to a new buffer, defines the new offsets, copies the existing array, frees the old buffer, and then updates the struct elements that define the array. Note that the method uses the `_get` method, which is described below. The version of this method did not use the `_get` method, but as the rest of the library was built, it seemed reasonable to substitute it in for clarity.

# The DRY Principle

In programming, DRY stands for "don't repeat yourself". We have used it above in a couple of places. First, we defined the relationship between array indices and buffer offsets in one place. This is also know as "Single source of truth". Second, we define the extend buffer method separately from the `_set` method. This is because we know that other methods, such as `_push_` and `_push_front` may need to extend the array as well.

DRY practices makes code easier to maintain. If you change how you want to extend memory in the array, you only have to change the implemenation in one place, instead of tracking down every method that extends the memory.

By the way, the opposite of DRY is WET: "write everything twice" or "we enjoy typing"!

# Visualizing an Array

It is very useful to have a method that converts an ADT into a string so you can print it. To do this in C, we define a method that dynamically allocates a string, prints into it, and the returns the array. The method we use to print into a string is called `snprintf`. The name comes from `sprintf`, which stands for "string printf". The `n` comes from the second argument to `snprintf`, which sets a limit on how big the resulting string can be. We have reserved 20 bytes for each float (and possible comma between values in the array). But since we do not know how much space a printed double will take in the worse case, we have to be careful to prevent memory overflows. The `_to_string` method for `DynamicArray` is as follows:

```c
char * DynamicArray_to_string(const DynamicArray * da) {
    assert(da->buffer != NULL);
    char * str = (char *) calloc (20,DynamicArray_size(da)),
         temp[20];
    int j = 1;
    str[0] = '[';
    for ( int i=0; i < DynamicArray_size(da); i++ ) {
        if ( DynamicArray_get(da,i) == 0 ) {
            snprintf ( temp, 20, "0" );
        } else {
            snprintf ( temp, 20, "%.5lf", DynamicArray_get(da,i) );
        }
        if ( i < DynamicArray_size(da) - 1 ) {
            sprintf( str + j, "%s,", temp);
            j += strlen(temp) + 1;
        } else {
            sprintf( str + j, "%s", temp);
            j += strlen(temp);
        }

    }
    str[j] = ']';
    return str;
}
```

# Debug Information

To use this method, you need to make sure to free the string when you are done. Here is an example that uses the method to print out some debug information about a `DynamicArray`, showing the free method call at the end.

```c
void DynamicArray_print_debug_info(const DynamicArray * da) {
    char * s = DynamicArray_to_string(da);
    printf ( "%s\ncapacity: %d\norigin: %d\nend: %d\nsize: %d\n",
      s,
      da->capacity,
      da->origin,
      da->end,
      DynamicArray_size(da));
    free(s);
}
```

# Pushing

Pushing a value onto the end of the array is straightforward and can be implemented using other methods.

```c
void DynamicArray_push(DynamicArray * da, double value ) {
    DynamicArray_set(da, DynamicArray_size(da), value );
}
```

Note that we do not even need to assert that the buffer is not null or extend it since the `_set` method takes care of that.

Pushing to the front of the array is almost as easy. If memory allocation were not an issue, we would simply decrement `da->origin` and then set the value at index zero. However, we need to check that `da->origin` is not zero first, and if it is, extend the buffer:

```c
void DynamicArray_push_front(DynamicArray * da, double value) {
    assert(da->buffer != NULL);
    while ( da->origin == 0 ) {
        extend_buffer(da);
    }
    da->origin--;
    DynamicArray_set(da,0,value);
}
```

# Popping

Popping elements off of the end or beginning of the array is similarly straightforward. The main things to do are

- Check that the array is not empty
- Zero out values beyond the new end of the array on popping.

```c
double DynamicArray_pop(DynamicArray * da) {
    assert(DynamicArray_size(da) > 0);
    double value = DynamicArray_get(da, DynamicArray_size(da)-1);
    DynamicArray_set(da, DynamicArray_size(da)-1, 0.0);
    da->end--;
    return value;
}

double DynamicArray_pop_front(DynamicArray * da) {
    assert(DynamicArray_size(da) > 0);
    double value = DynamicArray_get(da, 0);
    da->origin++;
    return value;
}
```

# Mapping

Mapping a function onto an array returns another array. Using the above methods, this meta-function is simple to write:

```c
DynamicArray * DynamicArray_map ( DynamicArray * da, double (*f) (double) ) {
    DynamicArray * result = DynamicArray_new();
    for ( int i=0; i<DynamicArray_size(da); i++ ) {
        DynamicArray_set(result, i, f(DynamicArray_get(da, i)));
    }
    return result;
}
```

Note that in testing this method, one might write:

```c
DynamicArray * t = DynamicArray_new(), * y;
double s = 0.0;
for ( int i=0; i<628; i++ ) {
    DynamicArray_set(t, i, s);
    s = s + 0.1;
}
y = DynamicArray_map(t,sin);
for (int i=0; i<DynamicArray_size(t); i++) {
    ASSERT_NEAR(DynamicArray_get(y,i), sin(0.1*i), 0.0001);
}
DynamicArray_destroy(t);
DynamicArray_destroy(y);
```

# Rounding Errors in Tests

Due to rounding errors, you might get this somewhat odd error message:

```bash
Expected equality of these values:
  DynamicArray_get(y,i)
    Which is: 0.564642
  sin(0.1*i)
    Which is: 0.564642
```

In this case, Google Test is not showing the full precision of the values, but at some point the numbers are not the same. Google Test has another method you can use instead:

```c
ASSERT_NEAR(DynamicArray_get(y,i), sin(0.1*i), 0.00001);
```

The last argument is the precision you require for equality.

# Main Limitation of DynamicaArray

One of the main problems with our dynamic array type is that it only works for arrays of doubles. In most higher languages, arrays work with any data type. Unforutnately, C does not provide a good way to manage types beyond telling you how much memory they take up. For example, you cannot pass a type as an argument into the `DynamicArray` constructor. The best we can do is to pass the size of a type to the constructor and the remember to cast when necessary.

# Arbitrary Array Values

In this section, we will build a new ADT called `AbitraryArray` with the goal to enable code such as the following to work.

```c
typedef struct {
    double x, y, z;
} Point;

AribtraryArray * a = AribtraryArray_new(sizeof(Point));
Point p;

for ( int i=0; i<10; i++ ) {
  p = { i, 0.5*i, 0.25*i };
  AribtraryArray_set_from_ptr( a, i, &p );
}

Point * q = (Point *) AribtraryArray_get_ptr(a,5);
printf ( "{%lf, %lf, %lf}", q->x, q->y, q->z );
```

The `_set_from_ptr` method takes a pointer to an element. This is because we can declare the third argment to the method to have type `void *`, meaning a pointer to something whose type we don't know. Unfortunately in C we can't specify an non pointer argument of unknown type, so this is the best we can do.

The `_get_ptr` method similarly returns a pointer to an element, instead of the element itself. Note that in the assignment to q we had to cast the result of `_get_ptr` to a point, since the method itself does not know what type to return.

# Copies vs References

In this ADT we will _copy_ the value to the array upon a call to `_set_from_ptr`. A very good alternative would be instead to simply store the pointer itself. The main reason we do not do this now is because then the user would need to remember to `free` all the values pointed to by the array after every destructive operation. When we start working with C++, we will see that this is easy to do.

# Arbitrary Array Declarations

The declarations of these methods are as follows:

```c
AribtraryArray * AribtraryArray_new(int);
void AribtraryArray_set_from_ptr(AribtraryArray *, int, void *);
void * AribtraryArray_get_ptr(const AribtraryArray *, int);
```

As you can see, the constructor now takes an argument that specifies the size of an element. The `_set_from_ptr` method takes a `void *` pointer to an element we will _copy_ into the array. The `_get_ptr` returns a `void *`, which we will have to cast if we want to use it subsequently.

# Arbitrary Array Data

To implement these methods, we need to change a few things in our implementation of double arrays. First, we need to add the element size to the `struct` that describes an array and change the type of the buffer from `double *` to an arbitrary array of bytes.

```c
typedef struct {
    int capacity,
        origin,
        end,
        element_size;
    char * buffer;
} AribtraryArray;
```

# Arbitary Array Constructor

In the constructor, we can now use use the element size to allocate the buffer and set the `origin` an `end` offsets.

```c
AribtraryArray * AribtraryArray_new(int element_size) {
    AribtraryArray * da = (AribtraryArray *) malloc(sizeof(AribtraryArray));
    da->element_size = element_size;
    da->capacity = DYNAMIC_ARRAY_INITIAL_CAPACITY;
    da->buffer = (char *) calloc (
        da->capacity,
        element_size
    );
    da->origin = ( da->capacity / 2 ) * da->element_size;
    da->end = da->origin;
    return da;
}
```

# Pointer Arithmetic

In the original implementation, the `origin` and `end` offsets were essentially indices into the array of doubles, with C doing the pointer arithmetic for us. Here, however, we need to do the pointer arithmetic ourselves.

<img src="https://raw.githubusercontent.com/klavins/EEP520-W20/master/week_3/images/dynamic_array_arbitrary.png" width=50%>

The new helper methods now uses the element size.

```c
static int index_to_offset ( const AribtraryArray * da, int index ) {
    return da->element_size * index + da->origin;
}

static int offset_to_index ( const AribtraryArray * da, int offset ) {
    return ( offset - da->origin ) / da->element_size;
}
```

# Arbitrary Array Setting

To implement the `_set_from_ptr` method, we need to copy the bytes pointed to by the `ptr` argument into the appropriate place in the array. The C `<string.h>` library provides a useful method called `memcpy` that copies a certain number of bytes from one memory location into another, so we use it here.

```c
void ArbitraryArray_set_from_ptr(ArbitraryArray * da, int index, void * ptr) {
    assert(da->buffer != NULL);
    assert ( index >= 0 );
    while ( out_of_buffer(da, index_to_offset(da, index) ) ) {
        extend_buffer(da);
    }
    /*     destination,                             source, size            */
    memcpy(da->buffer + index_to_offset(da, index), ptr,    da->element_size);
    if ( index >= ArbitraryArray_size(da) ) {
        da->end = index_to_offset(da,index+1);
    }
}
```

Otherwise, this method is the same as the corresponding method in the `DynamicArray` ADT.

# Extending the Buffer

The new set method does call `extend_buffer`, however, which needs to be re-implemented.

```c
static void extend_buffer ( ArbitraryArray * da ) {

    char * temp = (char *) calloc ( 2 * da->capacity, da->element_size );
    int new_origin = da->element_size * (
        da->capacity - (
            offset_to_index(da,da->end) - offset_to_index(da,da->origin)
        )/2
    );
    int new_end = new_origin + (da->end - da->origin);

    for ( int i=0; i<ArbitraryArray_size(da); i++ ) {
        memcpy(temp + new_origin + i * da->element_size,
               da->buffer + da->origin + i * da->element_size,
               da->element_size);
    }

    free(da->buffer);
    da->buffer = temp;
    da->capacity = 2 * da->capacity;
    da->origin = new_origin;
    da->end = new_end;

}
```

Note that we use `memcpy` here as well. We also use the element size to compute the new centered offsets.

# Getting Elements

Finally, here is the implementation of `_get_ptr`:

```c
void * ArbitraryArray_get_ptr(const ArbitraryArray * da, int index) {
    assert(da->buffer != NULL);
    assert ( index >= 0 );
    if ( index >= ArbitraryArray_size(da) ) {
        return NULL;
    } else {
        return (void*) ( da->buffer + index_to_offset(da, index) );
    }
}
```

The main difference here is that we return a `void *` pointer instead of a `double`, similar to how `malloc` and `calloc` work. This is because the `ArbitraryArray` ADT does not know what the type of the value being pointed to is. We also use pointer arithmetic instead of array indexing to determine the address of the requested element.

# Next Steps

The `ArbitraryArray` ADT described here is for pedagogical purposes only. It has many problems, such as the following:

- Users have to remember to cast results.
- You have to create each element twice, one to construct it and then once to copy it into the array.

**The solution**: In C++ we will use templates, which allow new data types to depend on other datatypes. We will be able to write

```c++
    array<double> a;
    array<Point> b;
```

and so on. The code for array is only written once, and takes the type ofd the elements as an argument.

This is one of many benefits (and complexities) of C++.

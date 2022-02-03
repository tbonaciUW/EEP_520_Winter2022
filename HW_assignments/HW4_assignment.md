# EEP_520_Winter2022

Software Engineering for Embedded Applications

## HW4 Assignment

### Due by 11:59pm PT on Sunday, February 13th, 2022 using Canvas and your private GitHub repos

Within your private Github repository called `520-Assignments`, please make a new directory called `hw_4`.
Note: for this assignment, you will want to clone the code from Lecture 4, specifically packages `typed_array` and `complex`.

1. Add methods `push`, `pop`, `push_front`, and `pop_front` to the `TypedArray` class. Write your tests first. Make sure that the arguments to these functions are declared as `const`. The pop methods should throw `std::range_error` with the message "Cannot pop from an empty array".
1. Add a method

   ```c++
   TypedArray concat(const TypedArray& other);
   ```

   to the `TypedArray` class. It should return a new array that is a concatenation of the given and the argument (leaving both untouched). You should be able to do:

   ```c++
   TypedArray<int> a;
   a.set(0,0);
   a.set(1,1);
   TypedArray<int> b = a.concat(a).concat(a); // yields a new array [0,1,0,1,0,1]
                                              // Should leave a untouched.
   ```

   for example.

1. Define a `TypedArray` method called `reverse` that reverses the array. It should change the array it is called on, and return a reference to the array itself (i.e. return `*this`).

1. Define the '+' operator for the `TypedArray` class to mean concatenation. You should be able to replace the fourth line above with

   ```c++
   TypedArray<int> b = a + a + a; // yields [0,1,0,1,0,1]
   ```

1. Finish the Complex Class. You should include the following methods. - `double re();` -- returns the real part of the number. The actual datum storing the real part should be private. - `double im();` -- returns the imaginary part of the number. The actual datum storing the imaginary part should be private. - `Complex conjugate()` -- returns the complex conjugate of the number.
   In addition, you should overload `*`, `+`, and `==` operators for your class. Note that overloading the `==` operator will make your class work with Google Test's `ASSERT_EQ` method. Don't forget to write tests first. Also, ask yourself why we don't need to overload the assigment operation `=` and why we do _not_ need a destructor for this class. Finally, a hint: You should declare the overloaded binary operators in your `.h` file and implement them in your `.cc` file to avoid linker errors when compiling.

**Code Organization:**: Your `hw_4` directory should look like

```bash
  complex.h
  complex.cc
  main.cc
  Makefile
  typed_array.h
  unit_tests.cc
```

Use `unit_tests.h` for all of your tests (we'll replace it with ours later).

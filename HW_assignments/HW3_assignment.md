s# EEP_520_Winter2022

Software Engineering for Embedded Applications

## HW3 Assignment

### Due by 11:59pm PT on Sunday, February 6th, 2022 using Canvas and your private GitHub repos

Within your private Github repository called `520-Assignments`, please make a new directory called `hw_3`.

Fo this assignment, you should copy the code`week3/arrays` directory to `520-Assignments/hw_3` to use as the starting point for your homework. All function prototypes for the methods you need to define are contained in the header files.

> Note: We highly recommend that you write tests for a method you are creating _before_ you implement it.

1. Implement the following mathematical operations for `DynamicArray`. They should all assert that the array is not empty (except `_sum`).

   - `_min`: returns the minimum value in the array.
   - `_max`: returns the maximum value in the array.
   - `_mean`: returns the average value in the array.
   - `_median`: returns the median value in the array.
   - `_sum`: returns the sum of the values in the array (does not need to assert non-empty).

1. For the `DynamicArray` ADT, write the methods `_last` and `_first` which return the value at the end or beginning of the array, respectively. Note that last and first should assert that the array is not empty.

1. Write a `_copy` method that produces a copy of an array. It should return an entirely new `DynamicArray` object.

1. Write a method called `_range` that creates a new dynamic array with values as follows:

   ```c
   DynamicArray * a = DynamicArray_range(0, 1, 0.1); /* yields [ 0, 0.1, 0.2, ..., 1.0 ] */
   ```

1. Implement the method `_concat`, which concatenates two arrays to produce a new array. For example,

   ```c
   DynamicArray * a = DynamicArray_range(0, 1, 0.1);
   DynamicArray * b = DynamicArray_range(1.1, 2, 0.1);
   DynamicArray * c = DynamicArray_concat(a, b); /* yeilds [ 0, 0.1, ..., 2.0 ] */
   ```

1. Implement a method called `_take` that returns a sub-array of `n` elements. If the argument positive, it takes from the beginning. If it is negative it takes from the end. If there are not enough elements, the method should return as many as it can, and set the rest to zero. For example:

   ```c
   DynamicArray * a = DynamicArray_range(1, 5, 1);
   DynamicArray * b = DynamicArray_take(a, 2);  /* yields [ 1, 2 ] */
   DynamicArray * c = DynamicArray_take(a, -2); /* yields [ 4, 5 ] */
   DynamicArray * d = DynamicArray_take(a, 7);  /* yields [ 1, 2, 3, 4, 5, 0, 0 ] */
   ```

1. (Extra Credit) Modify the `DynamicArray` ADT so that all constructors (`_new`, `_copy`, `_range`, `_take`, `_subarray` and `_concat`) count the number of arrays that have been constructed so far.

   - Create a method `_num_arrays` that returns the number of allocated arrays.
   - Create another method `_destroy_all` that destroys all allocated arrays.
   - Create a method `_is_valid` that determines whether an array has been deallocated or not (i.e. whether its `buffer` is NULL).
     As an example, the following test should pass:

   ```c
   DynamicArray * a = DynamicArray_range(0, 1, 0.1);
   DynamicArray * b = DynamicArray_range(1.1, 2, 0.1);
   DynamicArray * c = DynamicArray_concat(a, b);
   ASSERT_EQ(DynamicArray_is_valid(a), 1);
   ASSERT_EQ(DynamicArray_num_arrays(), 3);
   DynamicArray_destroy_all();
   ASSERT_EQ(DynamicArray_is_valid(a), 0);
   ASSERT_EQ(DynamicArray_num_arrays(), 0);
   free(a);
   free(b);
   free(c);
   ```

# EEP_520_Winter2022

Software Engineering for Embedded Applications

## HW2 Assignment

### Due by 11:59pm PT on Sunday, January 30th, 2022 using Canvas and your private GitHub repos

Within your private Github repository called `520-Assignments`, please make a new directory called `hw_2`.

For this assignment, you will write a set of functions that are mostly unrelated, but for convenience, we will put them into one source file and one header file. Your homework 2 directory should look like

```bash
- 520-Assignments/
  - hw_2/
    - solutions
      - main.c
      - unit_tests.c
      - solutions.h
      - solutions.c
      - Makefile
```

In the first directory, the `solutions.h` file will include `unit_test.h`, which should have all your function declarations in it. The implementations of those functions should be put in `solutions.c`.

# Exercises

1.  Write a function called `running_total` that keeps track of the sum of the arguments it has been called with over time. Someting like the following test should pass.

        TEST(HW2,RunningTotal) {
          ASSERT_EQ(running_total(1),  1);
          ASSERT_EQ(running_total(1),  2);
          ASSERT_EQ(running_total(5),  7);
          ASSERT_EQ(running_total(-3), 4);
        }

2.  Write a function called `reverse` that takes an array and its length, and returns a new array that is the reverse of the given array. The function should use `calloc` to create space for the new array. A test for this might look like

        TEST(HW2,Reverse) {
            int x[] = {10,20,30,40,50};
            int * y = reverse(x,5);
            ASSERT_EQ(y[0],50);
            ASSERT_EQ(y[1],40);
            ASSERT_EQ(y[2],30);
            ASSERT_EQ(y[3],20);
            ASSERT_EQ(y[4],10);
            free(y);
        }

3.  Write a function called `reverse_in_place` which takes an array and its length, and reverses it in place. Something like the following tests should pass.

        TEST(HW2,ReverseInPlace) {
            int x[] = {10,20,30,40,50};
            reverse_in_place(x,5);
            ASSERT_EQ(x[0],50);
            ASSERT_EQ(x[1],40);
            ASSERT_EQ(x[2],30);
            ASSERT_EQ(x[3],20);
            ASSERT_EQ(x[4],10);
        }

4.  Write a function called `num_occurences` that takes an array of integers, a length, and a value and returns the number of occurences of that value in the array.

        TEST(HW2,NumInstances) {
          int a[] = { 1, 1, 2, 3, 1, 4, 5, 2, 20, 5 };
          ASSERT_EQ(num_occurences(a,10,1), 3);
        }

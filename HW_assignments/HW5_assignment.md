# EEP_520_Winter2022

Software Engineering for Embedded Applications

## HW5 Assignment

### Due by 11:59pm PT on Sunday, February 27th, 2022 using Canvas and your private GitHub repos

Within your private Github repository called `520-Assignments`, please make a new directory called `hw_5`.

1. Write a function called `sort_by_magnitude` that takes a reference to a vector of doubles, and sorts it with a comparison function that says that x < y if and only if the absolute value of x is less than the absolute value of y. For example, -5 would be greater than 4 because |-5| > |4|. Use the generic algorithm `sort` (which you can look up on the c++ reference web page) and a lambda expression for your comparison function. Declare this method in `utilities.h`, and implement it in `utilities.cc`.

1. In Lecture 5, we introduced a concept of templates, and we defined a templated class TypedArray:

```c++
template <typename ElementType>                      // ElementType is the type variable
class TypedArray {

public:

    TypedArray();
    TypedArray(const TypedArray& other);

    // Copy constructor
    TypedArray& operator=(const TypedArray& other);

    // Destructor
    ~TypedArray();

    // Getters
    ElementType &get(int index);
    ElementType &safe_get(int index) const;         // This is new. See Lecture5_Readme.md for a refresher.
    int size() const;

    // Setters
    void set(int index, ElementType value);

private:

    int capacity,
        origin,
        end;

    ElementType * buffer;

    const int INITIAL_CAPACITY = 10;

    int index_to_offset(int index) const;
    int offset_to_index(int offset) const;
    bool out_of_buffer(int offset) const;
    void extend_buffer(void);

};
```

1. Using class TypedArray, write a function:

   ```c++
   TypedArray<TypedArray<double>> read_matrix_csv(const string path);
   ```

   that reads a comma separated value (CSV) file of doubles into a matrix and returns it. If there are any errors in the format of the CVS file, or missing values (so the CSV does not represent a matrix), throw an exception. Spaces and tabs should be alright between commas, but newlines should only be used to terminate a row of the matrix.

   Place this function in `utilities.h` and `utilities.cc`

1. Write a function:

   ```c++
   void write_matrix_csv(const TypedArray<TypedArray<double>> &matrix, const string path);
   ```

   that writes a comma separated value (CSV) file of doubles from a matrix. Test that you can write and read a matrix and get the same matrix back.
   Place this function in `utilities.h` and `utilities.cc`

1. Write a function:

   ```c++
   map<string, int> occurrence_map(const string path);
   ```

   that reads in an ascii text file and returns an assocation where each key is a word in the text file, and each value is the number of occurences of that word. Words consist entirely of either alpha-numeric characters (`a` through `z`, `A` through `Z`, `0` through `9`) or the single quote characters `'`. This constitutes only `26*2 + 10 + 1 = 63` valid characters. Valid words include `dont'` `10xgenomics` `bob`, etc. Invalid words are `$$f(x)$$` `Sh%6fh`, `not_a_word_because_of_underscores`.

   The method should be case-insensitive, and it should store the keys as lowercase. Ignore punctuation so that `I'm done!` results in keys `i'm` and `done`. Quotes should be handled as well so that `I'm so "done"` results in keys `i'm`, `so`, and `done`.
   Place this function in `utilities.h` and `utilities.cc`, and consider the following examples:

   **example 1: invalid word**

   ```
   This is a sentence. Don't think of wier_d strings as words. Really, 123 is a nice number.
   ```

   _valid keys_: `this is a sentence don't think of strings as words really 123 is a nice number`

   _invalid strings_: `wier_d`

   **example 2: single quotes**

   ```
   'I should use double quotes'
   ```

   _valid keys_: `'I should use double quotes'`

   _invalid strings_: None

   **example 3: single and double quotes & punctuation**

   ```
   George Mallory's famous quote: "My mind is in a state of constant rebellion."
   ```

   _valid keys_: `george mallory's famouse quote my mind is in a state of constant rebellion`

   _invalid strings_: None

   **example 4: parentheses, quotes, punctuation**

   ```
   10XGenomics (a biotech company) is quoted as saying "blah blah blah."
   ```

   _valid keys_: `10xgenomics a biotech company is quoted as saying blah`

   _invalid strings_: None

   **example 5: invalid words**

   ```
   the)s are no%y6 wo!e4
   ```

   _valid keys_: `are`

   _invalid string_: `the)s no%y6 wo!e4`

   Your homework directory should include

```c++
    utilities.h
    utilities.cc
    main.cc
    Makefile
    unit_tests.cc
```

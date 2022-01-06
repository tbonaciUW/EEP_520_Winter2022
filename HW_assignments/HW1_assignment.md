# EEP_520_Winter2022

Software Engineering for Embedded Applications

## HW1 Assignment

### Due by 11:59pm PT on Sunday, January 23rd, 2022 using Canvas and your private GitHub repos

Note: To avoid committing compiled objects to github, you should include the following extensions into a .gitignore file in the root of your repo (i.e. in 520_Assignments):

- bin/ 
- build/
- html/
- latex/
- tmp/
- ~\*
- _.o
- _.a

Within your private Github repository called `520-Assignments`, please make a new directory called `hw_1`.

### Problem 1:

#### Part I

Use the command `script hw1.problem1` to start a shell and save the input and output into a file `hw1.problem1`. Then run the commands needed to answer the following questions. If you make a few small mistakes, you don't need to start over, but either edit the file to remove any substantial errors, or include comments (input lines starting with #) next to short goofs so we can follow your output easily.

It would also be helpful if you included some comments to identify answers to the different sets of questions to make it easier for us to navigate through the output.

#### Getting started

1. Enter a command to print "Hello 2022 and the new quarter!" on the screen.
2. Display the current date and time on the screen.

#### File paths and directory navigation

3. Display the full path of your home directory.
4. Go to your home directory.
5. List all the files in your current directory.
6. Now, create a new folder called ‘temp’.
7. Rename this folder to ‘temp_new’.
8. Go to the ‘temp_new’ folder.
9. Create a file called 'file5' without using a text editor. (There are at least three easy ways to do this.)
10. Rename 'file5' to 'test'
11. Go up one directory.
12. Delete the ‘temp_new’ folder with a single command. (The command you use should also delete 
all of the files inside of the folder.)

#### Permissions

13. Create a file called 'secretfile' containing the text “It's a secret!” using the echo command.
14. Change the permission of this file in such a way so that no one can write to it. Use the ls command to show the file name and permissions.
15. Try to add more text to 'secretfile' using the cat command.

### Problem 2:

Make a new directory in your `hw_1` directory called `complex`. Make a header file called `complex.h`. Similar to the `fraction.h` file, define a structure with a two members, real and im which should have type double. Add function prototypes for `add`, `negate`, `multiply`, and `magnitude`. Functions that return a scalar should return double.

Next, make a source file called `complex.c` and put the definitions of the functions into it. For the magnitude function you will need a square root function, which can be obtained by adding the C math library as follows:

` #include <math.h> `
 

at the top of your `complex.c` file. The function itself is called `sqrt` and takes one argument.

Write tests for each of your functions.

Note that you will need to edit the Makefile to include the header and source files and edit main.c and unit_test.c to include your new header file.

Remember, we will compile your code against our own tests, so be sure to name your functions and struct members exactly as specified here and to test edges cases.

Note: Your hw_1 directory should have one  sub-directory - complex, with its own code and Makefile. You can copy Makefile, main.c, and unit_tests.h from fractions to complex initially. You will need to edit the Makefile to change the word fractions to the word complex. And you will need to replace the tests in unit_test.h with your own tests.

Software Engineering For Embedded Applications
===

Introduction and Build Environment
---

Modern embedded systems programming starts with the hardware and extends all the way to the cloud. The resulting myriad of programming languages, libraries, tools, data structures, and algorithms may seem difficult for any one programmer to master. In this course, we introduce the fundamentals of programming languages and software engineering common to all levels of embedded systems programming, giving students the conceptual tools they need to tackle any project.

- **Instructor**: Tamara Bonaci (tbonaci@uw.edu)
    - Office hour: Fridays 2:30-3:30pm using UW Zoom
- **TA**: Trung Le (tle45@uw.edu)
    - Office hours: TBD

Today
---
- Course structure and goals
- Build environment (docker, git, ubuntu, gcc, make, test)

Syllabus
===
- Week 1: Build environment and course overview
- Week 2: C syntax and data types
- Week 3: The C programming language II - dynamic arrays. Abstract Data Types
- Week 4: Fundamentals of C++ and object oriented programming
- Week 5: The C++ Standard Template Library
- Week 6: STL II
- Week 7: Event loops and the Elma API
- Week 8: Events and Finite State Machines
- Week 9: Mobile Robot Control
- Week 10:  Networking

Course Structure
===

- Live lectures on Thursdays from 6-7:30pm
- Two hours of pre-recorded material posted on Canvas, and expected to be viewed before the next lecture.
- Homework assigned every Thursday. Due on Sundays the following week.
- Turned in via Github. We clone your code and run unit tests in a Docker container.
- You may not copy or otherwise incorporate other people’s code into your own. 
- Discussing strategies and ideas is alright.
- Discussions: on Piazza

Resources
---
- **Canvas**: https://canvas.uw.edu/courses/1520187
- **Github**
    - Course repo: https://github.com/tbonaciUW/EEP_520_Winter2022
        - Lecture notes in week_1/README.md (go there now)
    - Your homework repo (more on this later)
- **Docker**: https://docs.docker.com/get-docker/
    - You need it on your laptop

Build Environment
===
- To keep track of, and share code, we will use Github
- To unify our build environment, we will use Docker
    - UNIX environment
    - bash shell
    - gnu C and C++ compiler
    - make build tool
- We will try to all use the same editor

Code Editor
===

- A very good editor for the C and C++ code we will write.
- Free: [https://code.visualstudio.com/](https://code.visualstudio.com/)
- Has syntax highlighting, autocomplete, extensions.
- Other editors we like: Atom, Sublime.
- You might like vim, emacs – but we expect you know how to use them.
- Avoid: Xcode, Visual Studio, etc.

Git
===

**Git** is a version control software. You can:
- Save a specific version of your code.
- See differences with previous versions.
- Tag versions.
- Roll back.
- Fork, branch, merge, etc.

**Github** is a way for collaborative teams to work on the same code base.

Github Accounts
===
- Make an account (if you don't have one) [here](https://github.com/join?source=header-home).

Installing Git
===

Mac
---
- Install the Xcode Command Line Tools. These tools include git.

Windows
---
- Go to [https://git-scm.com/download/win](https://git-scm.com/download/win)

Course Code
===

Make a code directory for the course and make it your working directory (us Terminal for Macs or the Command line for PCs).

Get the code for the course:

```bash
git clone https://github.com/tbonaciUW/EEP_520_Winter2022

Each week, we will add more code and README files. So you should do

```bash
git pull origin main
```

to get the latest code.

Git setup for homework
===

- Git is a version control system.
- Git should be easy to install on any OS
- Go to Github and make a new repo called 520-Assignments (with no README)
- Make the repo *private*
- Invite Trung and me to your repo

Then do

```
mkdir 520-Assignments
cd 520-Assignments
touch README.md                # on a PC, make an empty file called README.md (using VS Code for example)
git init
git add .
git commit -m "first commit"
git remote add origin https://github.com/yourusername/520-Assignments.git
git push -u origin main
// edit and/or add some files
git add .
git commit -m "Changed some files"
git push origin main
```

Go back to Github to see your changes. Your student account should give you the ability to make private repos (so other students will not see your code).

Resulting set up
===

Somewhere on your hard drive you should now have

```bash
  Code/
    EEP_520_Winter2022/
      README.md
      week_1/
        fractions/
        hello_c/
        hello_cpp/
        Dockerfile
        README.md
    520-Assignments/
      README.md
```

The Github repo for `EEP_520_Winter2022` is managed by Dr. Bonaci. The Github repo for `520-Assignments` is managed by you.

How we will use Git
===

- We will assign exercises each week.
- Put the code for exercises in, for example, `520-Assignments/hw_1`.
- We will clone your repo on Mondays mid-day (or there about).
- For C and C++ exercises, we will replace `unit_test.c` with our own `unit_test.c` and recompile your code. We will put our scripts up on Github (leaving out some details) for you to use test your code.
- <span style="color: red">It is important to name your functions and classes exactly as we specify so that the tests will compile.</span>

Docker
===

**Problem:**
- You all run various versions of Windows, Mac OS, and Linux.
- We need a common environment.
- So does any company that develops software.
- Production environment should match development environment.

**The solution:** Docker
- Let’s you define a unix environment with exactly the pre-requisites your code needs.

Virtualization
===

<img width=70%
     src="https://www.nakivo.com/blog/wp-content/uploads/2019/05/Docker-containers-are-not-lightweight-virtual-machines.png">

- Requires Virtualization (e.g. Intel, AMD, ARM and MIPS)
- On Linux, Docker uses the same kernel, but with a different namespace and filesystem.
- On Mac, Docker uses xhyve, a hypervisor that creates a completely sandboxed kernel space.
- On Windows, Docker uses Hyper-V. Also gives you a Unix like command shell.

Example Dockerfile
===

```
# Layer 1: Start with ubuntu with the gnu development environment installed
FROM gcc  

# Layer 2: Packages managed by ubuntu's package manager
RUN apt-get update && \
    apt-get install -y cmake && \
    apt-get install -y cppcheck && \
    apt-get install -y graphviz && \
    apt-get install -y doxygen && \
    apt-get install -y nano

# Layer 3: Google test
WORKDIR /usr/src
RUN git clone https://github.com/google/googletest.git
WORKDIR /usr/src/googletest/install
RUN cmake ../ && make && make install

# Layer 3: Move to a good directory to start working
WORKDIR /source
```

You can either use docker to build an "image" with all this stuff installed.

Or you can use pre-build images in the Dockerfile for this course, available at

> [https://hub.docker.com/r/klavins/520w20](https://hub.docker.com/r/klavins/520w20).

Installing Docker
===

Mac
---
- Go to https://www.docker.com/

Download `Docker.dmg` and double click on it. Once the Docker icon shows up in your status bar, you can open a terminal and start using docker.

Windows 10 Professional or Enterprise
---
- Go to https://www.docker.com/
- Download and run installer
- Note: Go to UWARE/Microsoft ad upgrade from Win 10 to "Win 10 for education"

Windows 10 Home and possibly Windows 8
---
- Go to https://docs.docker.com/toolbox/toolbox_install_windows/
- Download and run installer
- Run Docker Quickstart

Unix
---
```
sudo apt install docker.io
sudo systemctl start docker
```

Starting the Docker Image for the course
===

Vocabulary:
- **image**: stateless set of files in a filesystem
- **container**: A running unix kernel using that filesystem

To mount the `week_1` directory in a docker container and access the shell within the container, do:
```bash
docker run -v $PWD/week_1:/source -it klavins/520w20:cpp bash
```
on a Mac or Linux.

On windows you will have to use the full path, as in
```bash
docker run -v /c/Users/You/Code/EEP520-W20/week_1:/source -it klavins/ecep520:cppenv bash
```

The current directory will be mounted as though it were a disk drive at the container directory `/source`, which is also where you'll be when you start the image.

**Note:** Files is the mounted directory can be edited from the host machine.

**Note:** If you start and stop and restart a container, all changes to the image are lost.

UNIX: Filesystem
===

- `pwd`: Print the current directory. Should be `/source` when you start
- `cd`: Change to a new directory
    - `..` is the parent directory
    - `.` is the current directory
    - `/` is the root directory
    - `~` is your home directory
- `ls`: List the current directory
    - Try `ls -laF` to see more details.
    - Try `ls /usr/bin/g*` to list all files in `/usr/bin` that start with the letter `g`
- `mkdir`: Make a new directory
    - Try `mkdir temp`
- `mv a b`: move `a` to file name or directory `b`
- `cp a b`: copy a file from a to `b`
    - To move a directory, do `mv -r a b`

**Exercise:**
  - Copy the file README.md to `/tmp/README_copy.md`
  - List the `/tmp` directory and find out how big the copied file is and when it was last modified

UNIX: Important Places
===
- `/usr` stands for Unix System Resources
- `/usr/lib`, `/usr/local/lib`: Command line tools, like `ls`. `bin` means binary.
- `/usr/lib`, `/usr/local/lib`: Library files like DLLs.
- `/usr/include`, `/usr/local/include`: Header files that declare function and data types in libraries.
- `/etc` important configuration files
- `/dev` devices. These are not really files. Unix makes devices look like files, so you read and write to them to control devices.

**Exercises:**
- The `cat x` command outputs the contents of the file x. Look in the file `/usr/include/limits.h` and determin the maximume side of an integer, by finding the definition of the macro `INT_MAX`.

UNIX: Text files
===
- `cat`: Display the contents of a file
- `more`: Display large files. Use space bar to advance.
- `head`: Show the begninning of a file. Try `head -5 /usr/include/limits.h`
- `tail`: Show the end of a file. Try `tails -5 /usr/include/limits.h`
- `nano`: Edit a file.
- `a > b` put output of command `a` in file `b`
- `a >> b` append output of command `a` to file `b`

**Exercises:**
- Create a file called `/tmp/repeats.txt` containing 10 lines all equal to the first line of `/usr/include/limits.h`. Use `head` and `>>`.

UNIX: Searching
===
- `grep x y`: Find occurances of the string (or regular expression) x in file y.
    - Can also do a set of files as in `grep MAX /usr/include/*.h`
- `|` send the output of one command to the input of another, as in `tail -100 /usr/include/limits.h | grep POSIX`

**Exercises:**
- Use `grep` to find the definition of `INT_MIN` in `limits.h`

UNIX: Processes
===
- `ps`: List running processes. Use `ps waux` for more all processes (not just yours). Note, containers are not usually running many system processes.
- `jobs`: Similar to `ps` but lists processes you have started and put in the background.
- `kill x`: Kill process `x`
- `ctrl-Z`: pause the currently running, forground process.
- `&`: To run a process in the background initially, add `&` to the end of the command.
- `bg`: Put the most recently paused process in the background.
- `fg`: Put the most recently backgrounded process in the foreground.

**Exercises:**
- Run the command `sleep 1000`, which does nothing, for 1000 seconds.
- Put the process into the background.
- Use ps to find out its process id
- Use kill to kill the process.

More UNIX Resources
===

If you are new to Linux, go through a tutorial, such as the one at [linuxcommand.org](http://linuxcommand.org/).

Do the "Learning the Shell" part.

Don't worry about writing scripts for now.

Compiling and Running C Code
===

To compile the hello world example, do the following from within in the `cppenv` bash shell:
```bash
cd hello_c                      # change directory
ls                              # see what's in the directory
gcc -c hello.c                  # compile hello.c into hello.o
gcc -c main.c                   # compile main.c into main.o
gcc -o hello *.o                # link the two .o files to make an executable
ls -l                           # show all the new files
./hello                         # run the new execuable
```

Compiling C++ Code
===

From within in the `cppenv` bash shell,
```bash
cd hello_cpp
g++ -c hello.cc
g++ -c main.cc
g++ -o hello *.o
./hello
```

Compiling Tests and Linking Libraries
===

In the fractions directory is a simple Fraction api and some unit tests for it. These tests use [Google Test](https://github.com/google/googletest), a library that the `cppenv` Docker container already has installed in it. To build the executable for the fractions tests, do
```bash
cd fractions
g++ -c fraction.c -std=c++17
g++ -c unit_tests.c -std=c++17
g++ -c main.c -std=c++17
g++ -o test *.o fraction.h -lgtest -lpthread
```
Note that although the Fraction api is written in C, the test library is written in C++, so we use the `g++` compiler and linker to build the code. Also, Google Test uses the C++11 standard, which is not the default for C++, so we have to tell g++ top use it. Finally, the linker needs to know to link the shared object library for Google Test, called `gtest`, and also the PThread library, called `pthread`, which Google Test users.

To run the tests in `unit_tests.c`, simply do
```bash
./test
```

How the Fractions code works
===

**Exercise:** Write a test to verify that 1/2 plus 1/2 is 1.

Using a Makefile
===
Having to type `g++` commands over and over is tedious. To manage the build process, we will use [make](https://www.gnu.org/software/make/). The fractions directory has a Makefile in it with generic definitions for making a new project. To run make, just do
```bash
make
```
from within the fractions directory. This will make two new directories, `bin` and `build`. The former will have the executable, test, in it. The latter will have all the `*.o` files. Then it builds and links everything.

To documentation, which turns comments in the code into an html browsable form, you can do
```bash
make docs
```
Don't worry about documentation for now.

You can clean up by doing
```bash
make clean
```
or
```bash
make spotless
```
to really clean up.


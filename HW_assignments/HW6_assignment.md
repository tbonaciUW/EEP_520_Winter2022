# EEP_520_Winter2022

Software Engineering for Embedded Applications

## HW6 Assignment

### Due by 11:59pm PT on Sunday, March 13th, 2022 using Canvas and your private GitHub repos

Within your private Github repository called `520-Assignments`, please make a new directory called `hw_6`.

# Exercises

1. Develop a Stopwatch class that can be used to time functions. The class should have the following methods:

   ```c++
   void start();              // starts the timer
   void stop();               // stops the timer
   void reset();              // sets stopwatch to zero
   double get_minutes();      // number of minutes counted, as a double
   double get_seconds();      // number of seconds counted, as a double
   double get_milliseconds(); // number of milliseconds counted, as a double
   double get_nanoseconds();  // number of nanoseconds counted, as a double
   ```

   All `get_` methods should return values accurate to the nanosecond. Here is an example usage:

   ```c++
   #define SLEEP std::this_thread::sleep_for(std::chrono::milliseconds(1500))

   Stopwatch w; // should set the stopwatch to 0 seconds
   w.start();
   SLEEP;
   w.stop();
   std::cout << w.get_seconds() << "\n"; // about 1.5
   SLEEP;
   std::cout << w.get_seconds() << "\n"; // still about 1.5
   w.start();
   SLEEP;
   w.stop();
   std::cout << w.get_seconds() << "\n"; // about 3.0
   w.reset();
   std::cout << w.get_seconds() << "\n"; // 0.0
   ```

   To test your method, we will use assertions that test that `get_seconds` (for example) is approximately equal to the number of seconds that the stopwatch should have counted after various sleep operations.

1. In this exercise you will define two processes, and compose them with a channel.

   - First, define a process called `RandomProcess` that sends random doubles between 0 and 1 (inclusive) to a channel called link. It should send a new value to the channel every time it updates.
   - Define another process called `Filter` that reads from the `link` channel, and keeps a running average (presumably in a private variable) of the last 10 numbers sent to it (if 10 numbers have not yet been received, the running average should be of the numbers received so far). Initialize the running average to 0.
   - Add a new method to the `Filter` process called `double value()` that returns the current running average.
   - The following code should compile.

   ```c++
   elma::Manager m;

   RandomProcess r("random numbers");
   Filter f("filter");
   elma::Channel link("link");

   m.schedule(r, 1_ms)
    .schedule(f, 1_ms)
    .add_channel(link)
    .init()
    .run(100_ms);
   ```

   To test your two processes, we will create various test process classes. For example, we might create a process that alternatively sends 0.25 and 0.75 to the `link` channel. Then we would check that after 100 steps, your `Filter` channel's `value` method returns 0.5.

1. Define an `Integrator` process that numerically integrates whatever values it reads from a channel called `link`. The integrator should have an initial value equal to zero. When it reads a value `v` from the `link` channel, it should add `delta() * v` to the integrated value (presumably a private variable). Add a new method to the `Integrator` process called `double value()` that returns the current integral. We will test your process by composing it with a process that sends values to the `link` channel and checks that your process is computing the integral correctly. Thus, you should write such tests as well. For example, you could make a process that outputs a constant value, and check that your integrator outputs a ramp.

Your homework directory should contain:

```
stopwatch.cc
stopwatch.h
random_process.cc
random_process.h
filter.cc
filter.h
integrator.cc
integrator.h
Makefile
main.cc
unit_tests.cc
```

The .cc files are optional. You can put all the implementations in the .h if you want.

# EEP_520_Winter2022

Software Engineering for Embedded Applications

## Optional HW7 Assignment

### Due by 11:59pm PT on Tuesday, March 15th, 2022 using Canvas and your private GitHub repos (please notice the unuusal due _day_).

Note: this is an optional HW7. You are not required to attempt it, but if you choose to work on this assignment, you can use HW7 score to
replace your lowest score on HW1-HW6.

If you choose to work on this assignment, please make a new directory called `hw_7` within your private Github repository called `520-Assignments`,
and push your work there.

1. Define a `StateMachine` called 'Robot' that models the following diagram:

   <img src="https://raw.githubusercontent.com/klavins/EEP520-W20/master/week_7/images/robot.png" width="620"></image>

   Please define this code as a class called `Robot` in `hw7/robot.h` (optionally `robot.cc` if you want). You will need to inherit a State and StateMachine classes a lot like the `Microwave` example in `week8` does. Make a `Mode` class for states. Use only this one state class for all our states, like with the `ToggleSwitch` example. Initialization of a `Robot` should set up all of the states and transitions. You should be able to initialize and test your robot via the following:

   ```
   Robot robot("What a very nice robot.");

   Manager m;
    m.schedule(robot, 10_ms)
     .init()
     .start();

   EXPECT_EQ(robot.current().name(), "Wander");
   m.emit(Event("intruder detected"));
   EXPECT_EQ(robot.current().name(), "Make Noise;
   m.emit(Event("proximity warning"));
   EXPECT_EQ(robot.current().name(), "Evade");
   m.emit(Event("battery full"));
   EXPECT_EQ(robot.current().name(), "Evade);

   /// ETC

   ```

1. Create a new class derived from `StateMachine` called `BetterStateMachine` that has the one extra method

   ```json
   json to_json() const;
   ```

   This method should return a representation of a `StateMachine` as a json object. For example, in the `toggle-switch` example, if we had written

   ```json
   BetterStateMachine fsm("toggle switch");
   // etc.
   ```

   then `fsm.to_json().dump()` would return something like the following.

   ```json
   {
     "name": "binary counter",
     "states": ["on", "off"],
     "transitions": [
       {
         "from": "on",
         "to": "off",
         "when": "switch"
       },
       {
         "from": "off",
         "to": "on",
         "when": "switch"
       }
     ]
   }
   ```

   The order of the elements does not matter. We might test your code by building something like the `ToggleSwitch` example, but inheriting from `BetterStateMachine` and then checking that your method returns a `json` object with the correct structure. For example,

   ```c++
   json j = toggle_switch.to_json();
   ASSERT_EQ(j["name"], "toggle switch");
   ASSERT_EQ(j["state"].size(), 2);
   // ETC.
   ```

   Note that your `to_json` method is allowed to and will need to access the `protected` variables `_transitions` and `_initial`.

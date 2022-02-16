# EEP_520_Winter2022

Software Engineering for Embedded Applications

## Project

### Due by 11:59pm PT on Thursday, March 17th, 2022 using Canvas and your private GitHub repos.

## Requirements

The goal of your project is to build up an example system the uses Elma and Enviro. The general ingredients are:

- Uses enviro, or extends enviro significantly
- A new or _private_ folder within Github repo with all the code for your project
- Well documented code, with all public methods described. See the Elma project for an example.
- A `README.md` file, to be included on the front page of your github repo with
  - An overview and general usage information
  - Examples, if applicable
  - Installation / setup instructions

Projects are due by 11:59pm on Thursday, March 17th. All of the above criteria, as well as the overall usability of the code, will be used to grade your project. To submit your project, please submit the github URL for your repo on Canvas so we can clone your project and run it.

# Project Options

For the course project, you will want to build a project that implements <u>one</u> of the suggested behaviors.
We will clone your repo into a `klavins/enviro:alpha` image and run

```bash
esm start
enviro
```

to see to what extend you got the behavior to work. You are, of course, welcome to try more than one problem.

Note: below is a list of suggestions for your project that build upon material covered starting with _week 7_. However, anything similar to the ideas below are fine. You could, for example, create a video game, or a more realistic simulation of an actual robot, etc.

1. Using static elements, create a _maze like environment_.
   1. Put a robot at the start and have it explore until it finds the end of the maze. Use range sensors, wall following behaviors, and perhaps some kind of memory of where the robot has been.
   2. Next, extend or improve the maze environment example. For example, use more complicated mazes. Generate random mazes. Build a map of the environment and navigate within it.
1. Place a grid of about 20 blocks all over the arena.
   1. Create two robots each of which attempts to sweep the blocks to their respective sides of the field. You may want to change the shape of the robot from what we used in class so that it can better push blocks around.
   2. Next, extend or improve the blocks sweeping example. For example:
      1. Have blocks disappear once deposited in a specific place.
      2. Make new blocks periodically. Use more varied sizes and shapes.
      3. Have robots collaborate.
      4. Include obstacles in the environment.
1. Make a robot that moves around the arena in long, looping path. Call this robot the `Leader`.
   1. Create a `Follower` robot that uses a range finder to stay a respectful distance behind the leader.
   2. Next, add more `Follower` robots to create a train of robots.
   3. Extend or improve the example in which a robot moves around the arena with robots following it. Create a longer train of robots. Create flocking behaviors, and so on.

# Project Rubric

Goal: Be creative. Make a fun, engaging, and or informative simulation or game. Learn some C++ and multi-agent systems in the process.

Projects are due on Wednesday, March 17th by 11:59pm.

| Points | Description                                                                                                                                                 |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 5      | Uses enviro, or extends enviro in some way.                                                                                                                 |
| 5      | Code is well documented. Each method and class should have inline comments say what it does. See [here](https://github.com/klavinslab/elma) for an example. |
| 5      | Project is shared wit us as a new project within a student's individual repo.                                                                                                           |
| 5      | Repo has a licence file (e.g. the [mit licence](https://opensource.org/licenses/MIT)                                                                        |
| 5      | Repo has a README.md file                                                                                                                                   |
| 10     | README has an overview of what the goal of the project is                                                                                                   |
| 10     | README has a description of key challenges and how they were addressed                                                                                      |
| 10     | README describes how to install and run the code (assuming the user has docker installed)                                                                   |
| 10     | README describes how to run and/or use the project                                                                                                          |
| 5      | README acknowedges all sources of information or code used                                                                                                  |
| 20     | Project compiles                                                                                                                                            |
| 20     | Project runs without crashing. Occasional crashes will not result in partial deductions.                                                                    |
| 20     | Project shows a substantial behavior                                                                                                                        |
| 20     | Behavior is mostly bug free                                                                                                                                 |
| ----   | ----                                                                                                                                                        |
| Total  | 150                                                                                                                                                         |

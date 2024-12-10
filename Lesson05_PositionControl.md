# Position Control and Diagnostics with Shuffleboard

We practiced tuning a motor with [Rev Robotics Hardware Client](https://docs.revrobotics.com/rev-hardware-client) Was it easy to use? Today, we will experiment with closed loop position control.

> # Java Learning Resource
>
> Before we get into today's task, I need to share an excellent resource I found for learning the Java language:
>
> [Learn Computer Science](https://www.learncs.online)
>
> This is an incredible resource that you should all check out over the winter break!

## Position Control

During autonomous mode, we need to be able to command the robot to move from one position to another without losing track of our position (this is called odometry).  How do we accomplish that?

### Setup for this lesson:
1. Open up an empty VSCode window.
2. Clone the Git repository for: [2025_MotorDemo](https://github.com/FRC-Team8744/2025_MotorDemo)
3. Make sure that the main branch will build on your computer.
4. Create your own branch!

## Test, Explore, Experiment

This code is designed to run on the mini robot (AKA Cookie Monster).  The Neo needs to be strapped down!

Most of the interesting stuff is in the subsystem MotorNeo.  There are a few things to talk about:
* Shuffleboard debug display
  - For a quick debug, normally we just add a quick `SmartDashboard` call
  - When you have lots of information, we need to organize it.  First we start with a new tab in Shuffleboard.
  - "Layouts" are used to automatically add items to the screen with less coding.
  - Data that changes must be updated in a `Periodic` method!
  - It's possible to change the values displayed in Shuffleboard and read them back into your code.
  - Buttons in Shuffleboard can be used to trigger Commands.
  - Make be carefull with changes.  Shuffleboard is notoriously buggy and sometimes crashes your code.
* Default values are assigned in the constructor (including Shuffleboard)
* Methods are used to access private variables.
* Earlier, we used `MotorDriver.set(speed);` but we will be changing control types, so this time we are using the equivalent `Ctrl.setReference(position, CANSparkMax.ControlType.kPosition);`
* `updateShuffleBoardValues` is only complicated because we are looking for changes to values by humans.

Try it out - the A button moves the Neo the old way.  The Y button moves to an exact position.  Change the values.

> NOTE: In order to minimize mistakes, you must set Enable to 1 in order to update PID values.

What do you notice?  Is it accurate?  Is it fast?  If this was on a drive wheel, what would happen at the beginning of the move and at the end?

### Velocity and acceleration limits
1. Use the X button to test out a motion example using velocity and acceleration limits.
2. PID VALUES MUST BE CHANGED! "SmartMotion" requires MUCH smaller P gain.
  * Try: P = 0.00005, I = 0, D = 0, FF = 0.0001

## Programming challenge!
1. Create a `SequentialCommandGroup` command group.
2. Make the motor move in sequence to at least 3 different positions.
3. Commit your branch to GitHub!

### Learn about merging other people's code
[Merge in VSCode](https://www.youtube.com/watch?v=HosPml1qkrg)

### Resources
* [FRC documentation for Git](https://docs.wpilib.org/en/stable/docs/software/basic-programming/git-getting-started.html)
* [VSCode introduction to Git](https://code.visualstudio.com/docs/sourcecontrol/overview)
* [How do I do this with Git?](https://github.com/k88hudson/git-flight-rules/blob/master/README.md)

* [Shuffleboard documentation in WPILIB](https://docs.wpilib.org/en/stable/docs/software/dashboards/shuffleboard/index.html)
* [Rev Robotics (SparkMax) Software Examples](https://github.com/REVrobotics/SPARK-MAX-Examples/tree/master/Java)


# Saturday projects

## Turn to angle

Tune the turn to angle code.  Add telemetry to be more precise.

## Drive distance

Using velocity mode as an example, create a command that drives a set distance.  Then use velocity and acceleration limiting!

## Autos with parameters

How can you create a command that takes the required travel distance as a parameter?

## LED strips

The LEDs will need a subsystem to control them.

[Want an example?](https://docs.wpilib.org/en/stable/docs/software/examples-tutorials/wpilib-examples.html#miscellaneous-examples)

[How about another one?](https://github.com/FRC-Team8744/2024_Swivels_Crescendo/blob/main/src/main/java/frc/robot/subsystems/LEDS.java)

## Add slew rate limiter to joystick

```java
  // Slew rate limiters to make joystick inputs more gentle; 1/3 sec from 0 to 1.
  private final SlewRateLimiter m_speedLimiter = new SlewRateLimiter(3);
  private final SlewRateLimiter m_rotLimiter = new SlewRateLimiter(3);
```

## Add kinematics model

[WPILib example](https://github.com/wpilibsuite/allwpilib/blob/main/wpilibjExamples/src/main/java/edu/wpi/first/wpilibj/examples/differentialdrivebot/Drivetrain.java)

## Attempt PathPlanner?

[This is PathPlanner](https://pathplanner.dev/home.html)

To do that, we need the following methods in the drive base:
* getPose - Returns the current robot pose as a Pose2d
* resetPose - Resets the robot's odometry to the given pose
* getRobotRelativeSpeeds or getCurrentSpeeds - Returns the current robot-relative ChassisSpeeds. This can be calculated using one of WPILib's drive kinematics classes
* driveRobotRelative or drive - Outputs commands to the robot's drive motors given robot-relative ChassisSpeeds. This can be converted to module states or wheel speeds using WPILib's drive kinematics classes.

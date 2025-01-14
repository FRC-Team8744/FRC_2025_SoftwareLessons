# KitBot can move!

Good job on the weekend - KitBot can drive around in teleop.  What else do we need to make it useful?

## Next steps

In order to be effective in the game, the robot needs to be able to score, and move in autonomous mode.  How do we get there?
* Fix the issue with the roller motor.
* Make the drive motor encoders measure distance in meters.
* Measure the gyro angle.
* Make the robot report it's map position (odometry).
* Reset the robot's map position and gyro angle at program load (when the robot is turned on).

## Measure distance

1. Get your robot driving (comment out non-working code, like the roller subsystem)
2. Create encoder objects for both right and left sides.
    * Hint1 - class level variables and objects in the constructor
    * [Hint2 - Rev examples](https://github.com/REVrobotics/SPARK-MAX-Examples/tree/master/Java)
3. Send the position of both motor encoders to Shuffleboard.
    * Hint - use the `periodic` method
4. Reset the encoders when needed
    * Make a `resetEncoders()` method
    * Call your method in the constructor
5. Put tape marks on the floor an exact distance apart (in meters).
6. Starting from zero, measure how far your robot thinks it is going.
7. Scale the encoders so that the travel distance is measured correctly.
8. Bonus points - have your code calculate the correct scale factor
    * [Drive Base Gear Ratio Info](https://cdn.andymark.com/media/W1siZiIsIjIwMjQvMDEvMDkvMTQvMTYvNTQvMTAyMTAwY2UtOTczYi00ZTA2LTg3NTMtMGM0MDA4OTQxYWZmL0FNMTRVNV9Vc2VyR3VpZGVfSzI0RGVjMjAyMy5wZGYiXV0/AM14U5_UserGuide_K24Dec2023.pdf?sha=17d7bb8604f367c9)
    * [Wheel diameter hints](https://www.andymark.com/products/higrip-wheels-options?sku=am-0940b)

## Measure the gyro angle

1. Insert code to read the gyro angle (in mentor Alex's branch).
2. Post the gyro angle measurements to Shuffleboard.
3. Is it accurate?  How do you reset it to zero at the start of the match?
4. Verify that your code properly displays gyro angles in degrees.

## Odometry

1. [Which way is forward?](https://docs.wpilib.org/en/stable/docs/software/basic-programming/coordinate-system.html)
2. [What's odometry?](https://docs.wpilib.org/en/stable/docs/software/kinematics-and-odometry/intro-and-chassis-speeds.html)
3. [What are kinematics?](https://docs.wpilib.org/en/stable/docs/software/kinematics-and-odometry/differential-drive-kinematics.html)
4. [How do we apply this to KitBot?](https://docs.wpilib.org/en/stable/docs/software/kinematics-and-odometry/differential-drive-odometry.html)
5. [Old team code for odometry](https://github.com/FRC-Team8744/ThomasCanRam/blob/main/src/main/java/frc/robot/Drivetrain.java)

## Hints - from Miss Piggy's competition code

```java
public class DriveSubsystem extends SubsystemBase {
  // Odometry class for tracking robot pose
  SwerveDriveOdometry m_odometry;

  // Create Field2d for robot and trajectory visualizations.
  public Field2d m_field;
```

```java
  public DriveSubsystem() {
  // Odometry class for tracking robot pose
  m_odometry =
      new SwerveDriveOdometry(
          SwerveConstants.kDriveKinematics,
          m_imu.getRotation2d(),
          new SwerveModulePosition[] {
            m_frontLeft.getPosition(),
            m_frontRight.getPosition(),
            m_rearLeft.getPosition(),
            m_rearRight.getPosition()
          });

          // Create and push Field2d to SmartDashboard.
    m_field = new Field2d();
    SmartDashboard.putData(m_field);

```

```java
  @Override
  public void periodic() {
    // Update the odometry in the periodic block
    m_odometry.update(
        m_imu.getRotation2d(),
        new SwerveModulePosition[] {
          m_frontLeft.getPosition(),
          m_frontRight.getPosition(),
          m_rearLeft.getPosition(),
          m_rearRight.getPosition()
        });
    
    // Update robot position on Field2d.
    m_field.setRobotPose(getPose());
```

```java
  /**
   * Returns the currently-estimated pose of the robot.
   *
   * @return The pose.
   */
  public Pose2d getPose() {
    return m_odometry.getPoseMeters();
  }

  public void zeroIMU() {
    m_imu.setYaw(0.0);
  }

  /**
   * Resets the odometry to the specified pose.
   *
   * @param pose The pose to which to set the odometry.
   */
  public void resetOdometry(Pose2d pose) {
    m_odometry.resetPosition(
        m_imu.getRotation2d(),
        new SwerveModulePosition[] {
          m_frontLeft.getPosition(),
          m_frontRight.getPosition(),
          m_rearLeft.getPosition(),
          m_rearRight.getPosition()
        },
        pose);
  }

  /** Resets the drive encoders to currently read a position of 0. */
  public void resetEncoders() {
    m_frontLeft.resetEncoder();
    m_rearLeft.resetEncoder();
    m_frontRight.resetEncoder();
    m_rearRight.resetEncoder();
  }

  public void zeroGyro() {
    m_imu.setYaw(0);
    resetOdometry(new Pose2d());
  }
```
# Saturday projects

## Autos with parameters

Ira and Solomon started developing Auto routine commands.  But when they needed to drive straight at two different distances, they made two different Auto commands.  This can fill up our program with huge numbers of files quickly.  How can you create a command that takes the required travel distance as a parameter?

## LED strips

We need a cable harness to connect the LED strips to the robot.  But before we can do that - where do we put the LEDs?

The LEDs will need a subsystem to control them.

[Want an example?](https://docs.wpilib.org/en/stable/docs/software/examples-tutorials/wpilib-examples.html#miscellaneous-examples)

[How about another one?](https://github.com/FRC-Team8744/2024_Swivels_Crescendo/blob/main/src/main/java/frc/robot/subsystems/LEDS.java)

## Turn to angle

Here is some example code for turning the robot to a gyro angle.  We will work our way through it.

```java
// Copyright (c) FIRST and other WPILib contributors.
// Open Source Software; you can modify and/or share it under the terms of
// the WPILib BSD license file in the root directory of this project.

package frc.robot.commands;

import edu.wpi.first.math.MathUtil;
import edu.wpi.first.math.controller.PIDController;
import edu.wpi.first.wpilibj.smartdashboard.SmartDashboard;
import edu.wpi.first.wpilibj2.command.CommandBase;
import frc.robot.Constants.DriveConstants;
import frc.robot.subsystems.DriveSubsystem;

public class TurnSimplePID extends CommandBase {
  private final DriveSubsystem m_drive;
  PIDController m_turnCtrl = new PIDController(DriveConstants.kTurnP, DriveConstants.kTurnI, DriveConstants.kTurnD);
  private double m_output;
  private double m_heading;
  private double m_goalAngle;

  /** Creates a new TurnSimplePID. */
  public TurnSimplePID(double targetAngleDegrees, DriveSubsystem drive) {
    // Use addRequirements() here to declare subsystem dependencies.
    m_drive = drive;
    addRequirements(m_drive);

    m_goalAngle = targetAngleDegrees;
  }

  // Called when the command is initially scheduled.
  @Override
  public void initialize() {
    m_turnCtrl.enableContinuousInput(-180, 180);
    m_turnCtrl.setTolerance(1.0);
    m_turnCtrl.setSetpoint(m_goalAngle);
    m_turnCtrl.reset();

    // Debug information
    // double kP = SmartDashboard.getNumber("kP", DriveConstants.kTurnP);
    // double kI = SmartDashboard.getNumber("kI", DriveConstants.kTurnI);
    // double kD = SmartDashboard.getNumber("kD", DriveConstants.kTurnD);
    // m_turnCtrl.setPID(kP, kI, kD);
    // Things to remember:
    //   input squaring in arcade drive
    //   changing values in shuffleboard PID works - but robot must be disabled

    SmartDashboard.putData("PID", m_turnCtrl);
  }

  // Called every time the scheduler runs while the command is scheduled.
  @Override
  public void execute() {
    m_heading = m_drive.getHeading();
    m_output = MathUtil.clamp(m_turnCtrl.calculate(m_heading), -1.0, 1.0);
    // Send PID output to drivebase
    m_drive.arcadeDrive(0.0, -m_output, false);
    // m_drive.tankDriveVolts(m_output, -m_output);

    // Debug information
    SmartDashboard.putNumber("PID setpoint", m_goalAngle);
    SmartDashboard.putNumber("PID output", m_output);
    SmartDashboard.putNumber("PID setpoint error", m_turnCtrl.getPositionError());
    SmartDashboard.putNumber("PID velocity error", m_turnCtrl.getVelocityError());
    SmartDashboard.putNumber("PID measurement", m_heading);

    // SmartDashboard.putNumber("kP", m_turnCtrl.getP());
    // SmartDashboard.putNumber("kI", m_turnCtrl.getI());
    // SmartDashboard.putNumber("kD", m_turnCtrl.getD());
  }

  // Called once the command ends or is interrupted.
  @Override
  public void end(boolean interrupted) {
    m_drive.arcadeDrive(0.0, 0.0);
  }

  // Returns true when the command should end.
  @Override
  public boolean isFinished() {
    return m_turnCtrl.atSetpoint();
  }
}
```

[Link to original SimplePID code](https://github.com/FRC-Team8744/ThomasCanTurn2/blob/Command-with-generic-PID/src/main/java/frc/robot/commands/TurnSimplePID.java)

[Link to matching RobotContainer](https://github.com/FRC-Team8744/ThomasCanTurn2/blob/Command-with-generic-PID/src/main/java/frc/robot/RobotContainer.java)

One of the things we will have to fix in our code is how we drive the robot with the joystick - so that it is easier to understand.

Add to the end of CANDriveSubsystem:

```java
  // sets the speed of the drive motors
  public void driveArcade(double xSpeed, double zRotation) {
    drive.arcadeDrive(xSpeed, zRotation);
  }
```

Add to the end of CANRollerSubsystem:

```java
  /** This is a method that makes the roller spin */
  public void runRoller(double forward, double reverse) {
    rollerMotor.set(forward - reverse);
  }
```

Now we have to update RobotContainer:

```java
  private void configureBindings() {
    // Set the A button to run the "RollerCommand" command with a fixed
    // value ejecting the gamepiece while the button is held

    // before
    operatorController.a()
        .whileTrue(new RollerCommand(() -> RollerConstants.ROLLER_EJECT_VALUE, () -> 0, rollerSubsystem));

    // Set the default command for the drive subsystem to an instance of the
    // DriveCommand with the values provided by the joystick axes on the driver
    // controller. The Y axis of the controller is inverted so that pushing the
    // stick away from you (a negative value) drives the robot forwards (a positive
    // value). Similarly for the X axis where we need to flip the value so the
    // joystick matches the WPILib convention of counter-clockwise positive
    driveSubsystem.setDefaultCommand(new DriveCommand(
        () -> -driverController.getLeftY() *
            (driverController.getHID().getRightBumperButton() ? 1 : 0.5),
        () -> -driverController.getRightX(),
        driveSubsystem));

    // Set the default command for the roller subsystem to an instance of
    // RollerCommand with the values provided by the triggers on the operator
    // controller
    rollerSubsystem.setDefaultCommand(new RollerCommand(
        () -> operatorController.getRightTriggerAxis(),
        () -> operatorController.getLeftTriggerAxis(),
        rollerSubsystem));
  }
```

Here are some examples of ways to do the same thing, but take a bit more work to understand:

[Example of TurnToAngle](https://github.com/FRC-Team8744/ThomasCanTurn2/blob/Command-with-generic-PID/src/main/java/frc/robot/commands/TurnToAngle.java)

[Example of TurnToAngleProfiled](https://github.com/FRC-Team8744/ThomasCanTurn2/blob/Command-with-generic-PID/src/main/java/frc/robot/commands/TurnToAngleProfiled.java)

Here are some examples of code for driving to an exact distance:

[Link to original DriveDistance code](https://github.com/FRC-Team8744/ThomasCanGo/blob/Velocity_Profile/src/main/java/frc/robot/commands/DriveDistance.java)

[Link to original DriveDistProfiled code](https://github.com/FRC-Team8744/ThomasCanGo/blob/Velocity_Profile/src/main/java/frc/robot/commands/DriveDistProfiled.java)

## Velocity mode driving

[Example of velocity mode driving of SparkMax](https://github.com/FRC-Team8744/ThomasCanGo/blob/Velocity_Profile/src/main/java/frc/robot/subsystems/DriveSubsystem.java)

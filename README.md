# Two_Wheel_Balancing_Robot
A two-wheeled Arduino robot that balances itself using a PID controller. The most important components are an MPU6050 (accelerometer + gyroscope), a TB6612FNG motor driver, two DC motors, and a custom-cut MDF structure.

The MPU6050 measures the robot's tilt angle. The difference between this angle and the balance angle is sent into a PID controller, which calculates how fast and in which direction the wheels must spin to keep the robot upright.

## Detailed Order of Operations
The Arduino reads the accelerometer and gyroscope values from the MPU6050 and combines them with a complementary filter to get the tilt angle. The error between the target angle and the measured angle is processed by a PID controller. The sign of the PID output chooses the direction of the motors, and its size sets their speed (PWM) through the TB6612FNG. Since the motors don't spin at low PWM values, any output above zero is mapped to start at a minimum value (deadzone = 60).

## Complementary Filter
The accelerometer gives the angle from gravity but is noisy, and the gyroscope is smooth but slowly drifts. The filter combines both:

**angle = α × (previous angle + gyro rate × dt) + (1 − α) × accelerometer angle**

- **α** = τ / (τ + dt), with τ = 0.75 s
- **gyro rate × dt** = how much the angle changed since the last cycle

## PID Controller
The final motor output is calculated as:

**P value + I value + D value**

- **P value** = error × P constant
- **I value** = integral × I constant — where integral is the accumulated sum of (error × dt) per cycle
- **D value** = gyro rate × D constant — the gyroscope gives the tilt rate directly, so it doesn't need to be calculated from the error

The constants were tuned by trial and error. The I constant is currently 0, so the robot works as a PD controller.

## Development
The structure was drawn and planned before building, and the MDF pieces were custom-cut. At first, the robot couldn't stand up, even after adjusting the code and redistributing the weight. The problem was the motor driver: the original L298N was slow and inefficient, losing about 2 V as heat. After replacing it with a TB6612FNG, which uses MOSFETs, the robot stood up.

## Materials Used
- Arduino
- MPU6050 (accelerometer + gyroscope)
- TB6612FNG motor driver
- Two DC motors with wheels
- Two 18650 Li-ion batteries (3.7 V, 2000 mAh each)
- Custom-cut MDF pieces

## Programs Used
- Arduino IDE

## Future Improvements
- Tuning the I term

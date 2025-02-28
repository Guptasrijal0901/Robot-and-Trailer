# Robot and Trailer Simulation

## Overview
This project simulates a **robot towing a trailer**, where the robot moves in a 2D space using proximity sensors to estimate its traveled distance. The simulation also evaluates the accuracy of the estimated motion using error metrics such as **Mean Absolute Error (MAE)** and **Root Mean Squared Error (RMSE)**.

The simulation includes:
- A **Robot** equipped with two proximity sensors.
- A **Trailer** attached to the robot.
- A **Proximity Sensor Model** to simulate noisy distance measurements.
- **Error Analysis** to compare estimated and actual travel distances.

---

## Features
- Simulates a robot moving in a 2D space.
- Uses **Gaussian noise** in sensor measurements.
- Tracks both the **robot's position** and **trailer’s position**.
- Estimates travel distance using **sensor readings**.
- Computes **error metrics (MAE & RMSE)** for motion accuracy.
- Visualizes robot and trailer trajectory along with distance estimation.

---

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/Guptasrijal0901/Robot-and-Trailer.git
   ```
2. Navigate to the project directory:
   ```bash
   cd Robot-and-Trailer
   ```
3. Install dependencies (requires Python 3.x):
   ```bash
   pip install numpy matplotlib
   ```

---

## Code Explanation

### 1. **Proximity Sensor**
The **ProximitySensor** class simulates a real-world sensor with **Gaussian noise**:
```python
class ProximitySensor:
    def __init__(self, position_offset, noise_level=0.02):
        self.position_offset = position_offset
        self.noise_level = noise_level
        self.measured_value = 0

    def sense_distance(self, actual_distance):
        self.measured_value = actual_distance + np.random.normal(0, self.noise_level)
```
- **`sense_distance`**: Measures distance with added noise to simulate real sensor errors.

### 2. **Trailer Model**
The **Trailer** class follows the robot with a given hitch length:
```python
class Trailer:
    def __init__(self, hitch_length=1.6):
        self.hitch_length = hitch_length
        self.position_x = 0
        self.position_y = -hitch_length
        self.orientation_angle = 0

    def update_position(self, robot_x, robot_y, robot_heading):
        tow_angle = np.arctan2(robot_y - self.position_y, robot_x - self.position_x)
        self.orientation_angle = tow_angle
        self.position_x = robot_x - self.hitch_length * np.cos(self.orientation_angle)
        self.position_y = robot_y - self.hitch_length * np.sin(self.orientation_angle)
```
- **`update_position`**: Adjusts the trailer’s position based on the robot’s motion.

### 3. **Robot Model**
The **Robot** class simulates the movement and sensor data collection:
```python
class Robot:
    def __init__(self, position_x=0, position_y=0, heading_angle=0, step_size=1.0, sensor_spacing=0.6, wheel_spacing=1.2):
        self.position_x = position_x
        self.position_y = position_y
        self.heading_angle = heading_angle
        self.step_size = step_size
        self.wheel_spacing = wheel_spacing
        self.left_proximity_sensor = ProximitySensor(-sensor_spacing)
        self.right_proximity_sensor = ProximitySensor(sensor_spacing)
        self.trailer = Trailer()
```
- **Uses two proximity sensors** to estimate motion.
- **Updates position and heading** using sensor data.

### 4. **Simulation Loop**
The main loop simulates **100 movement steps**:
```python
robot = Robot()
robot_trajectory = [

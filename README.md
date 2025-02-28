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
robot_trajectory = [(robot.position_x, robot.position_y)]
trailer_trajectory = [(robot.trailer.position_x, robot.trailer.position_y, robot.trailer.orientation_angle)]

for _ in range(100):
    robot.move()
    robot_trajectory.append((robot.position_x, robot.position_y))
    trailer_trajectory.append((robot.trailer.position_x, robot.trailer.position_y, robot.trailer.orientation_angle))
```
- Runs **100 iterations** of robot motion.
- Tracks robot and trailer positions.

### 5. **Error Computation**
The difference between **estimated** and **actual distances** is measured using **MAE & RMSE**:
```python
mean_absolute_error = np.mean(np.abs(estimated_distances - actual_distances))
root_mean_squared_error = np.sqrt(np.mean((estimated_distances - actual_distances)**2))
print(f"MAE: {mean_absolute_error:.4f}, RMSE: {root_mean_squared_error:.4f}")
```

### 6. **Visualization**
Plots **trajectory and error analysis**:
```python
plt.subplot(1, 2, 1)
plt.plot(robot_trajectory[:, 0], robot_trajectory[:, 1], marker='o', label='Robot Path')
plt.plot(trailer_trajectory[:, 0], trailer_trajectory[:, 1], marker='s', linestyle='dashed', label='Trailer Path')
plt.xlabel('X')
plt.ylabel('Y')
plt.title('Trajectory')
plt.legend()
plt.grid()

plt.subplot(1, 2, 2)
plt.plot(range(len(estimated_distances)), estimated_distances, label='Estimated Distance')
plt.plot(range(len(actual_distances)), actual_distances, linestyle='dashed', label='Actual Distance')
plt.xlabel('Step')
plt.ylabel('Distance')
plt.title('Distance Error')
plt.legend()
plt.grid()
plt.show()
```
---

## Results
- **Trajectory Plot:** Shows how the robot and trailer move in 2D space.
- **Distance Estimation Plot:** Compares estimated vs. actual travel distance.
- **Error Metrics:**
  - **MAE (Mean Absolute Error)**: Measures average absolute deviation.
  - **RMSE (Root Mean Squared Error)**: Penalizes large errors more heavily.

---

## Usage
To run the simulation:
```bash
python robot_trailer_simulation.py
```

---

## Future Improvements
- Implement **more advanced motion models**.
- Add **sensor fusion techniques** for better estimation.
- Improve **real-world applicability** by integrating real sensor data.

---

## Author
**Srijal Gupta**  
Final-Year B.Tech (Electronics & Communication)  
Madan Mohan Malaviya University of Technology, Gorakhpur  
📧 [srijalgupta360@gmail.com]  
🔗 [Guptasrijal0901]




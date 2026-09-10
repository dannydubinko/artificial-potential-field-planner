# PyBullet Quick Reference & Useful Documentation

PyBullet is an easy-to-use Python module for physics simulation for robotics, games, visual effects, and machine learning. It is built on top of the Bullet Physics SDK.

---

## 1. Installation & Setup

### Standard Installation
Inside your virtual environment:
```bash
pip install pybullet
```

### macOS / Apple Silicon Build Fix (If Compile Fails)
If building from source on macOS (Clang 16+) fails due to an `fdopen` macro redefinition conflict in the bundled `zlib` library, you can install PyBullet using precompiled binaries via `conda-forge`, or download the package and patch `zutil.h`:

1. Download PyBullet:
   ```bash
   pip download --no-deps pybullet
   tar -xzf pybullet-*.tar.gz
   ```
2. Open `examples/ThirdPartyLibs/zlib/zutil.h` and wrap the `fdopen` macro declaration with a `!defined(__APPLE__)` condition:
   ```c
   #else
   #if !defined(__APPLE__)
   #ifndef fdopen
   #define fdopen(fd, mode) NULL /* No fdopen() */
   #endif
   #endif
   #endif
   ```
3. Install from your patched folder:
   ```bash
   pip install ./pybullet-<version>
   ```

---

## 2. Core Concepts & Simulation Flow

### Connecting to Physics Service
PyBullet can run in either interactive 3D visualizer mode (`GUI`) or headless simulation mode (`DIRECT`).

```python
import pybullet as p
import pybullet_data

# GUI Mode (creates a window)
physicsClient = p.connect(p.GUI)

# Headless / Direct Mode (no window, much faster)
# physicsClient = p.connect(p.DIRECT)

# Set additional search paths (useful for finding default robot files like plane, r2d2, etc.)
p.setAdditionalSearchPath(pybullet_data.getDataPath())
```

### Configuring Simulation Environment
```python
# Set gravity vector (x, y, z)
p.setGravity(0, 0, -9.81)

# Set time step (default is 1/240 seconds)
p.setTimeStep(1.0 / 240.0)
```

### Stepping the Simulation
If you do not enable real-time simulation, you must step the simulation manually:
```python
# Enable real-time simulation (not recommended for deterministic training/RL)
# p.setRealTimeSimulation(1)

# Step simulation manually (recommended)
for i in range(1000):
    p.stepSimulation()
```

### Disconnecting
```python
p.disconnect()
```

---

## 3. Loading Objects (URDF, SDF)

Objects are loaded from files such as URDF (Unified Robot Description Format) or SDF.

```python
# Load static ground plane
planeId = p.loadURDF("plane.urdf")

# Load a robot with custom base position and orientation (quaternion)
startPos = [0, 0, 1]
startOrientation = p.getQuaternionFromEuler([0, 0, 0]) # Roll, Pitch, Yaw

robotId = p.loadURDF("r2d2.urdf", startPos, startOrientation)

# Get number of joints
numJoints = p.getNumJoints(robotId)
print(f"Robot loaded with {numJoints} joints.")
```

---

## 4. Querying and Controlling Joint States

### Joint Information
Each joint has an index from `0` to `numJoints - 1`.
```python
jointInfo = p.getJointInfo(robotId, jointIndex)
# Returns a tuple containing: joint index, joint name, joint type, etc.
```

### Reading Joint State
```python
jointState = p.getJointState(robotId, jointIndex)
# Returns: joint position, joint velocity, joint reaction forces, applied joint motor torque
joint_position = jointState[0]
joint_velocity = jointState[1]
```

### Controlling Actuators (Motors)
You can set control modes: Position Control, Velocity Control, or Torque Control.
```python
# Position Control
p.setJointMotorControl2(bodyUniqueId=robotId,
                        jointIndex=jointIndex,
                        controlMode=p.POSITION_CONTROL,
                        targetPosition=1.5, # target angle in radians
                        force=500)          # maximum motor force

# Velocity Control
p.setJointMotorControl2(bodyUniqueId=robotId,
                        jointIndex=jointIndex,
                        controlMode=p.VELOCITY_CONTROL,
                        targetVelocity=2.0, # target speed rad/s
                        force=100)

# Torque/Force Control
p.setJointMotorControl2(bodyUniqueId=robotId,
                        jointIndex=jointIndex,
                        controlMode=p.TORQUE_CONTROL,
                        force=10.0)          # target torque in N-m
```

---

## 5. Body Base States & Coordinates

```python
# Get current position and orientation of the base link
pos, ortho = p.getBasePositionAndOrientation(robotId)
euler_angles = p.getEulerFromQuaternion(ortho) # converts to [roll, pitch, yaw]

# Reset position/orientation instantly (teleporting, breaks physics continuity)
p.resetBasePositionAndOrientation(robotId, [0, 0, 2], ortho)
```

---

## 6. Rendering / Camera API

You can capture synthetic camera images (RGB, depth, segmentation mask).

```python
# Define view and projection matrices
viewMatrix = p.computeViewMatrix(
    cameraEyePosition=[1, 1, 1],
    cameraTargetPosition=[0, 0, 0],
    cameraUpVector=[0, 0, 1]
)
projectionMatrix = p.computeProjectionMatrixFOV(
    fov=60,
    aspect=1.0,
    nearVal=0.1,
    farVal=10.0
)

# Retrieve images
width, height, rgbImg, depthImg, segImg = p.getCameraImage(
    width=320,
    height=320,
    viewMatrix=viewMatrix,
    projectionMatrix=projectionMatrix
)
```

---

## 7. Minimal Working Example

Save this to `simulation.py` and run it:

```python
import pybullet as p
import pybullet_data
import time

# Connect to GUI visualizer
physicsClient = p.connect(p.GUI)
p.setAdditionalSearchPath(pybullet_data.getDataPath())
p.setGravity(0, 0, -9.81)

# Load ground plane and a model
planeId = p.loadURDF("plane.urdf")
robotId = p.loadURDF("r2d2.urdf", [0, 0, 1])

# Run visualizer loop
print("Stepping simulation... Press Ctrl+C in terminal to stop.")
try:
    while True:
        p.stepSimulation()
        time.sleep(1./240.)
except KeyboardInterrupt:
        p.disconnect()
        print("Simulation ended.")
```

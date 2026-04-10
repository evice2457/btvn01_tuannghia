# btvn01_tuannghia
# distance_warning

A ROS2 (Humble) package that demonstrates the core communication patterns of ROS2: **Publisher/Subscriber**, **Service**, and **Action**, built around a distance-based warning system.

---

## Overview

This package simulates a sensor that continuously publishes distance readings. A listener node monitors those readings and triggers warnings when objects get too close. The warning threshold can be adjusted at runtime via a service, and individual distance checks can be requested through an action server with step-by-step feedback.

```
[distance_publisher] ──/distance_topic──► [distance_listener]
                                                   ▲
[set_threshold_service] ◄──── /set_threshold ──────┘
[distance_action_server] ◄─── /check_distance (action)
```

---

## Package Structure

```
distance_warning/
├── src/
│   ├── distance_publisher.cpp        # Publishes random distances at 1 Hz
│   ├── distance_listener.cpp         # Subscribes and warns on threshold breach
│   ├── set_threshold_service.cpp     # Service server to adjust threshold
│   ├── distance_action_server.cpp    # Action server with 5-step feedback
│   └── distance_action_client.cpp    # Action client to trigger a distance check
├── action/
│   └── CheckDistance.action          # Custom action definition
├── srv/
│   └── SetThreshold.srv              # Custom service definition
├── launch/
│   └── distance_warning.launch.py   # Launches all 4 server-side nodes
├── CMakeLists.txt
└── package.xml
```

---

## Custom Interfaces

### `srv/SetThreshold.srv`
```
bool increase        # true = increase threshold, false = decrease
---
bool success
float32 new_threshold
string message
```

### `action/CheckDistance.action`
```
float32 distance_to_check
---
bool is_safe
string result_message
---
int32 step
int32 total_steps
string feedback_msg
```

---

## Nodes

| Node | Type | Topic / Service / Action |
|------|------|--------------------------|
| `distance_publisher` | Publisher | `/distance_topic` |
| `distance_listener` | Subscriber | `/distance_topic` |
| `set_threshold_service` | Service Server | `/set_threshold` |
| `distance_action_server` | Action Server | `/check_distance` |
| `distance_action_client` | Action Client | `/check_distance` |

---

## Prerequisites

- ROS2 Humble
- Ubuntu 22.04
- Packages: `rclcpp`, `rclcpp_action`, `std_msgs`, `rosidl_default_generators`

---

## Build

```bash
cd ~/ros2_ws
colcon build --packages-select distance_warning
source install/setup.bash
```

---

## Run

### Launch all nodes (publisher + listener + service + action server)

```bash
ros2 launch distance_warning distance_warning.launch.py

# With a custom threshold (default is 0.5 m)
ros2 launch distance_warning distance_warning.launch.py threshold:=0.8
```

### Run the action client (in a separate terminal)

```bash
source ~/ros2_ws/install/setup.bash

# Check if 0.3 m is safe
ros2 run distance_warning distance_action_client 0.3

# Check if 0.8 m is safe
ros2 run distance_warning distance_action_client 0.8
```

### Call the threshold service manually

```bash
# Increase threshold by 0.1
ros2 service call /set_threshold distance_warning/srv/SetThreshold '{increase: true}'

# Decrease threshold by 0.1
ros2 service call /set_threshold distance_warning/srv/SetThreshold '{increase: false}'
```

---

## Expected Output

### Terminal 1 — Launch file
```
[distance_publisher]: Publishing: 0.82 m
[distance_listener]: Distance received: 0.82 m
[distance_publisher]: Publishing: 0.34 m
[distance_listener]: Distance received: 0.34 m
[distance_listener]: Warning: Object too close! (0.34 m < threshold: 0.50 m)
```

### Action client
```
[INFO] [distance_action_client]: Sending goal: check 0.30 m
[INFO] [distance_action_client]: Feedback [1/5]: Receiving distance value...
[INFO] [distance_action_client]: Feedback [2/5]: Fetching threshold parameter...
[INFO] [distance_action_client]: Feedback [3/5]: Comparing values...
[INFO] [distance_action_client]: Feedback [4/5]: Generating result...
[INFO] [distance_action_client]: Feedback [5/5]: Done.
[WARN] [distance_action_client]: RESULT: NOT SAFE — NOT SAFE: distance is below threshold.
```

---

## Verification

```bash
# Check publish rate (should be 1.0 Hz)
ros2 topic hz /distance_topic

# List all active nodes
ros2 node list

# Check current threshold on a node
ros2 param get /distance_listener threshold

# Visualize the node graph
rqt_graph
```

---

## Key ROS2 Concepts Demonstrated

| Concept | Where used |
|---------|-----------|
| Publisher / Subscriber | `distance_publisher` → `distance_listener` via `/distance_topic` |
| Service (request-response) | `set_threshold_service` via `/set_threshold` |
| Action (long-running + feedback) | `distance_action_server` via `/check_distance` |
| Parameters | `threshold` declared and managed per-node |
| Launch file | `distance_warning.launch.py` starts 4 nodes with shared initial parameters |

---

## Notes

- The `threshold` parameter is managed **independently** by each node. Calling the `/set_threshold` service updates the service node's own parameter and syncs it to `distance_listener` and `distance_action_server` via async parameter clients.
- `distance_action_client` is intentionally **excluded** from the launch file — it is a one-shot client meant to be run manually.
- The `CMake Deprecation Warning` about `rosidl_target_interfaces` is harmless and does not affect the build.

---

## Author

evice2457 — ROS2 Humble, Ubuntu 22.04

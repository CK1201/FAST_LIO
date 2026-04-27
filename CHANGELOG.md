## [Unreleased] - 2026-04-27
### Features
- Ported high-rate IMU odometry publication to ROS2 as `/Odometry_imu`, seeded from the lidar-updated EKF state and propagated on each incoming IMU.
- Added a `mapping.known_map_en` localization mode for ROS2 that matches scans against a prebuilt PCD map loaded from `mapping.known_map_path`.
- Added `/initialpose`-driven pose initialization and relocalization for known-map mode, with frame switching to `mapping.map_frame` for odometry, TF, path, and registered clouds.

### Design Rationale
- Reused the existing FAST-LIO scan-to-map EKF pipeline so the ROS2 branch gains the new behaviors without introducing a parallel localization stack.
- Split the static map input path (`mapping.known_map_path`) from the existing save path (`map_file_path`) to avoid ambiguous configuration and accidental overwrite.
- Rebuilt a read-only local submap from the static PCD around the current estimate to preserve the existing `ikdtree` matching flow while disabling online map growth in localization mode.

### Notes & Caveats
- Known-map mode requires a valid `mapping.known_map_path` and an initial pose published in `mapping.map_frame`.
- Relocalization resets pose, velocity, local submap state, and high-rate IMU odom seeding, but intentionally preserves the existing IMU bias, gravity, and extrinsic calibration state.
- Build verification in this workspace still depends on ROS2 packages such as `pcl_ros` being installed and sourced in the local environment.

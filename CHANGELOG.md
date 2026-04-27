## [Unreleased] - 2026-04-27
### Features
- Added a `mapping/known_map_en` mode that localizes against a prebuilt PCD map instead of incrementally growing the FAST-LIO map.
- Added `/initialpose`-driven pose initialization and relocalization using `geometry_msgs/PoseWithCovarianceStamped`.
- Switched global odometry, path, TF, and registered cloud frame output to a configurable map frame when known-map mode is enabled.

### Design Rationale
- Reused the existing FAST-LIO scan-to-map ICP/EKF pipeline so the known-map feature stays close to the original estimator rather than introducing a parallel localization stack.
- Kept `ikdtree` as the nearest-neighbor backend, but fed it a read-only local submap cropped from the static PCD to avoid mixing online mapping with known-map localization.
- Treated relocalization as a pose/velocity reset while preserving IMU bias, gravity, and extrinsics to keep runtime convergence stable.

### Notes & Caveats
- Known-map mode requires a valid `map_file_path` and an initial pose expressed in `mapping/map_frame`.
- The initial pose resets position, orientation, and velocity only; it does not rerun full static IMU initialization.
- Large maps are handled by rebuilding a local read-only submap around the current estimate rather than keeping the full PCD in the active matching tree.

# custos-interfaces

ROS2 message, service, and action definitions for the Custos drone stack. **Nothing else.**

This is the only repo other Custos packages may depend on for the wire contract. Splitting interfaces into a dedicated repo prevents the classic polyrepo trap where messages live inside the package that emits them and consumers end up pulling unrelated dependencies just to compile a listener.

## Packages

| Package | Purpose |
|---|---|
| `custos_common_msgs` | Shared types: header extensions, enums, generic geometry helpers |
| `custos_perception_msgs` | Vision pipeline outputs, sensor fusion results, detections |
| `custos_control_msgs` | Pixhawk bridge surface, mission state, flight commands |
| `custos_navigation_msgs` | SLAM/VIO output, planning, trajectory representation |

## Versioning

- Semver per package (release-please manifest mode)
- **Major bump = wire-format break**
- Consumers pin by tag in `custos-bringup/ros2.repos`

## CI

Any PR here triggers `interfaces-consumers.yml` (in `custos-infra`) which rebuilds every consumer repo against the PR's ref. Breaking consumer builds blocks merge.

For the system overview, see [`custos-bringup`](https://github.com/NCKU-custos/custos-bringup).

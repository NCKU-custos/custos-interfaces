# custos-interfaces — CLAUDE.md

ROS2 msg/srv/action definitions for the Custos drone stack. **Nothing else.** This is the wire contract every other Custos package depends on.

Workspace-wide rules and state caveats live at `../CLAUDE.md` (= `/space/drone/CLAUDE.md`). Read it before designing new messages. If you are in a standalone clone, the single most important rule is: any rename or breaking change here triggers a build in every consumer (ADR 0009), and is a major-version bump.

## State of this repo

- Four sub-packages by domain (ADR 0012): `custos_common_msgs`, `custos_perception_msgs`, `custos_control_msgs`, `custos_navigation_msgs`. Each has `package.xml` and `CMakeLists.txt` populated.
- **All `msg/`, `srv/`, `action/` directories are empty.** `find . -name '*.msg' -o -name '*.srv' -o -name '*.action'` returns nothing. The contract is shape-only today.
- `release-please-config.json` is in multi-package monorepo mode with the `linked-versions` plugin: all four packages bump together under group `custos-interfaces`.
- `.release-please-manifest.json` carries one entry per package.

## Cross-repo edges

- **Depends on (ROS2 base only):** `builtin_interfaces`, `std_msgs`, `geometry_msgs`, `sensor_msgs`, `nav_msgs`. Zero Custos dependencies — this is the root of the Custos dependency graph (ADR 0002).
- **Within this repo:** `custos_perception_msgs`, `custos_control_msgs`, and `custos_navigation_msgs` each depend on `custos_common_msgs`. The three domain packages do **not** depend on each other.
- **Depended on by:** `custos-perception` (`perception_mock`), `custos-control` (`control_pixhawk`), `custos-navigation` (`navigation_planner`), `custos-simulation` (`simulation_gazebo`, exec only), `custos-novatek-sdk-wrapper` (`novatek_wrapper`).

## Repo-specific hard rules

- **Field rename = wire break.** A rename in any package is a major-version bump for that package's `release-please` group. `interfaces-consumers.yml` (in `custos-infra`) dispatches builds in every consumer repo against your PR's ref; merge is blocked until every consumer is green. Coordinate consumer pin updates in the same merge cycle (ADR 0009).
- **No cross-domain `<depend>` inside interfaces, except on `common_msgs`.** If you reach for a perception type from a navigation message, the right move is almost always to lift the shared type into `custos_common_msgs`. Cross-domain depends create cycles in the build graph (ADR 0012).
- **No `<depend>` on anything outside `custos-interfaces` and ROS2 base.** This repo must remain depend-free of every other Custos repo, forever. Anything else makes the graph a cycle.
- **Pick the right sub-package on the first try.** Moving a message between packages later renames its fully-qualified name (`custos_perception_msgs/msg/Foo` → `custos_navigation_msgs/msg/Foo`) and breaks every consumer's `#include` and Python import. Acceptable when paired with a major bump and consumer coordination; otherwise avoid.
- **NOVATEK isolation (ADR 0010) is satisfied here, not enforced.** Anything the NOVATEK wrapper exposes — frames, services, parameters — must be expressed as a public message/service in `custos_perception_msgs`. The wrapper has no other path to the outside world.

## Build / test cheat sheet

```bash
# From the workspace root after vcs import:
colcon build --packages-up-to custos_navigation_msgs   # builds common + navigation
colcon test --packages-select custos_perception_msgs

# Build just this repo in place (pre-first-commit):
cd /space/drone
colcon build --packages-select custos_common_msgs custos_perception_msgs custos_control_msgs custos_navigation_msgs
```

When proposing a new message:

1. Identify the right sub-package (perception/control/navigation), or lift to `common_msgs` if shared.
2. Add the file under `<pkg>/msg/` (or `srv/`, `action/`).
3. Register it in `<pkg>/CMakeLists.txt` under `rosidl_generate_interfaces(...)`.
4. Add any new base-message `<depend>` to `<pkg>/package.xml`.
5. Verify `colcon build --packages-up-to <pkg>` succeeds.

## Pointers specific to this repo

- Why a separate repo: ADR 0002
- Multi-package layout: ADR 0012
- Cross-repo CI: ADR 0009
- release-please config: `release-please-config.json` (multi-package, linked-versions)

> TODO(post-first-commit): replace this whole repo's "empty contract" note once real `.msg`/`.srv`/`.action` files land.
> TODO(post-first-release): record the first stable tag here and confirm consumer pins in `custos-bringup/ros2.repos` resolve.
> TODO(post-active): document any *Draft suffix convention if we end up adopting it for in-flight messages (planning record §"Open follow-ups").

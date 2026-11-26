# rosdistro (test)

A small rosdistro written for testing an experimental ROS 2 build system.

**This is a test project. It is not the ROS distribution index, it is not an
official ROS project, and nothing here is connected to official ROS
infrastructure.** The real one is at https://github.com/ros/rosdistro.

It was written from scratch and is not a fork. It holds only what the test
workspaces need.

| Path | What it is |
|---|---|
| `index-v4.yaml` | The index. Point `ROSDISTRO_INDEX_URL` at its raw URL. |
| `rolling/distribution.yaml` | The test Rolling distribution, with its releases. |
| `rolling/conda/constraints.yaml` | The versions conda packages are held to for Rolling. |
| `rosdep/base.yaml`, `rosdep/python.yaml` | rosdep rules. |
| `rosdep/sources.list.d/20-default.list` | The rosdep sources list that names those rule files. |

## Releases

A release is an entry in `rolling/distribution.yaml`:

```yaml
ament_cmake:
  release:
    tags:
      release: '{version}'
    url: https://github.com/ros2ci-test-ros-world/ament_cmake.git
    version: 2.9.3
```

It has the shape of the entries in ros/rosdistro, so existing tools still
read the file, but it means something else:

- `url` is the source repository. There is no release repository.
- `version` is the version in every `package.xml` at the tag, with no
  increment after it. Making a release changes this one line.
- `tags: release:` says how a version's tag is named. Left out, the tag is
  the version.
- `skip` lists packages of the repository that are not released.
- The packages are not listed. The build system reads them from the source
  at the tag. An existing tool that reads this file therefore sees only the
  repository's own name as its package.

The source repositories named here are copies made for testing, in the same
organization as this repository. They are not forks.

## rosdep rules

The `ubuntu` and `rhel` entries are copied from ros/rosdistro for the same
keys. The `conda` entries are new: conda is a platform in these rules, as
Ubuntu is, and one conda rule serves every operating system.

A key resolves on conda only if every conda package its rule names is also in
the constraints file. The rule gives the name and the constraints give the
version, as an Ubuntu release does for apt.

Resolving against conda needs the rosdep in
https://github.com/ros2ci-test-ros-world/rosdep:

```
export ROSDISTRO_INDEX_URL=https://raw.githubusercontent.com/ros2ci-test-ros-world/rosdistro/main/index-v4.yaml
rosdep update
rosdep install --os=conda:rolling --rosdistro rolling --from-paths src --ignore-src -y
```

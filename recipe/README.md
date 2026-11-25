# `lerobot` conda-forge packaging

This README documents the design choices used in the `lerobot` conda-forge package.

If you are an agent that is updating metadata and recipe for a new release of `lerobot`, please always update this file updating the skipped extras and metadata modifications.

## Python extras

In the `conda-forge` packaging of `lerobot`, supported Python extras like `lerobot[pygame-dep]` are modeled by separate `lerobot-pygame-dep` outputs. Underscores in an upstream extra name become hyphens in the output name (e.g. `lerobot[multi_task_dit]` becomes `lerobot-multi-task-dit`).

The following extras do not have dedicated outputs because one or more required dependencies are not yet available on conda-forge:

| Upstream extra | Blocking dependency or extra |
| --- | --- |
| `lancedb-convert` | `lerobot-lancedb >=0.3.1,<0.4.0` |
| `motorbridge-dep` | `motorbridge >=0.5,<0.6` |
| `motorbridge-smart-servo-dep` | `motorbridge-smart-servo >=0.0.4,<0.1.0` |
| `reachy2` | `reachy2-sdk >=1.0.15,<1.1.0` |
| `rebot` | `motorbridge-dep` and `motorbridge-smart-servo-dep` |
| `yam` | `motorbridge-dep` |
| `phone` | `hebi-py >=2.8.0,<2.12.0` and `teleop >=0.1.0,<0.2.0` |
| `fineart_vla_kernels` | `kernels >=0.14.1,<0.15.0` |
| `hilserl` | `gym-hil >=0.1.14,<0.2.0` |
| `libero` | `hf-libero >=0.1.4,<0.2.0` (Linux dependency) |
| `metaworld` | `metaworld ==3.0.0` |

## Dependencies metadata changes

The `pypi` ecosystem (used by lerobot upstream) and `conda-forge` ecosystem are quite different, for a number of reasons (see https://pypackaging-native.github.io/ for in-depth discussion on this):
* D1: In `pypi`, the metadata of packages is contained in the package itself, so the solver needs to download many packages if there are constraints conflicts, while in conda-forge all the metadata is downloaded in one shot by the solver. For this reason, sometimes upstreams adds version constraint bounds on dependency to help the `pypi` solver that it does not make sense to have in `conda-forge` packaging
* D2: In `pypi` C++ or in general native libraries are vendored inside Python dependencies, that can lead to conflict among different package, while in `conda-forge` in general native dependencies are modeled explicitly, so sometimes bounds are added in `pypi` metadata to account for those conflicts, that it does not make sense to have in `conda-forge`.
* D3: In `pypi`, dependencies for which no wheel is available are built from source, so it is sometimes necessary to ensure that build dependencies (such as `setuptools` or `cmake`) are available. In `conda-forge`, packages are never automatically built, so we can remove any build dependencies from the `run` dependency.
* D4: In `pypi` metadata of packages is immutable, so if a new version of a dependency is a released that breaks a package, the only way to fix the `pypi` metadata is to release a new package, and that is why `pypi` packages frequently have quite conservative upper bounds. On `conda-forge`, if necessary metadata can be also patched for existing packages, using repodata patching (see https://conda-forge.org/docs/maintainer/understanding_conda_forge/life_cycle/#post-publication-particularities-1 and https://prefix.dev/blog/repodata_patching).
* D5: In `pypi` ABI constraints across packages typically are manually managed and not reflected in `metadata` (for example to install matching `torch`, `torchvision` and `torchcodec` packates). In `conda-forge` all these constraints are explicitly managed, so it is not necessary to manually managing those.

For these reasons, we may patch the upstream `pypi` dependencies metadata of `lerobot`. Each modification is documented in the following.

### `lerobot` 

* The `cmake` and `setuptools` run dependencies are required in `pypi` due to `D3`, but they are not required on conda-forge, so they are removed.
* `torchvision` and `torchcodec` has complex upper bounds for `D5`. Upper bounds for `torchvision` and `torchcodec` are thus removed in `conda-forge` packaging. As `torchcodec` is available on all conda-forge platforms, all platform specific selectors are removed.
* `py-opencv` in `pypi` has an upper bound due to `D4`, that is removed in `conda-forge` as we anyhow include `opencv` related tests in the test that we run as part of the `lerobot-tests`.
* A `numpy` upper bound is present for simplifying solver life in `pypi` (`D1`), upper bound that is removed in `conda-forge`. The lower bound is also relaxed from `>=2.0.0` to `>=1.26.0` for compatibility with IsaacSim 5.1, whose `isaacsim-kernel` package pins `numpy==1.26.0`; no numpy 2.0-only API is used by `lerobot` itself.
  When using NumPy 1.26.0 with SciPy-dependent extras, pin `scipy<1.17`: SciPy 1.17's Python metadata requires NumPy 1.26.4 or newer.
* `requires-python` is `>=3.12` in `pypi`. A source patch (`patch_py311_compat.patch`) rewrites PEP 695 declarations, avoids evaluating a Python 3.11-incompatible queue type, and backports `itertools.batched` using `more-itertools`, so the package supports Python 3.11 (needed for IsaacSim 5.1 compatibility).
* The `lerobot` output only carries the true unconditional `dependencies` from `pyproject.toml`. The CLI scripts backed by the `dataset`, `training`, `hardware` and `viz` extras (e.g. `lerobot-record`, `lerobot-train`, `lerobot-dataset-viz`) are still exposed as entry points on the base package, but their extra run dependencies are not force-installed; they are packaged separately as `lerobot-dataset`, `lerobot-training`, `lerobot-hardware` and `lerobot-viz`, which users who need those scripts should install alongside `lerobot`.

### `lerobot[intelrealsense]`

* `pypi` has an upper bound for the `pyrealsense2` packages due to `D4`, which is removed. The PyPI metadata retains its platform-specific package names and markers; the conda output uses `pyrealsense2` on every platform and drops the PyPI-specific fourth version component.

### `lerobot[placo-dep]`

* `pypi` has an upperbound for `placo` due to `D1`, `D4` and `D5`, that is not required in `conda-forge`, so the upper bound is removed.
* `pypi` also pins `cmeel-urdfdom` and `cmeel-tinyxml2` to work around an ABI mismatch (`D2`); neither is available as a conda-forge pypi-mapped package, and on conda-forge the corresponding native libraries are pulled in as regular dependencies of the `placo` conda package, so both are removed.

### `lerobot[grpcio-dep]`

* `pypi` has upper bounds on `grpcio` (`<2.0.0`) and `protobuf` (`<8.0.0`) due to `D4`. Neither `ValidateProtobufRuntimeVersion` in `lerobot.transport.services_pb2` nor the gRPC runtime check in `lerobot.transport.services_pb2_grpc` enforces an upper bound, and conda-forge's `protobuf` increments its major version with each `libprotobuf` release, so both upper bounds are removed while retaining the generated-code lower bounds (`grpcio>=1.73.1`, `protobuf>=6.31.1`). Transport and gRPC actor tests are included in `lerobot-tests`.

### `lerobot[training]`

* `pypi` uses `trackio[gpu]` (adding `psutil` and `nvidia-ml-py`) on non-macOS platforms and `trackio[apple-gpu]` (adding only `psutil`) on macOS. `lerobot-training` mirrors this: `psutil` is always required, while `nvidia-ml-py` is required on all platforms except macOS.

### `lerobot[groot]`

* `pypi` restricts `decord` to x86 (`platform_machine`) because of wheel availability (`D3`). conda-forge builds `decord` for Linux and macOS on all architectures, so `lerobot-groot` requires it on every platform except Windows, where `decord` is not packaged. The patched Python metadata uses the matching `sys_platform != 'win32'` marker. As a consequence `lerobot-groot` and `lerobot-all` resolve on Windows without `decord`.

### `lerobot[viz]`

* `pypi` requires `foxglove-sdk >=0.25.1,<0.26.0`, which is not available on conda-forge, so it is removed. `lerobot-viz` supplies Rerun visualization; Foxglove functionality is unavailable through this output.
* `rerun-sdk` bundles `rerun-notebook`, whose own (unconditional) PyPI metadata requires `ipykernel<7.0.0` (`D2`); conda-forge's `rerun-sdk` package does not declare this, so `ipykernel<7.0.0` is added explicitly to `lerobot-viz`.

### `lerobot[aloha]`

* `pypi` has an additional dependency on `lerobot[scipy-dep]` to "flatten the dependency tree" (i.e. help on `D1`), so in `conda-forge` we can remove the `lerobot[scipy-dep]` dependency.
* Runtime dependencies such as `dm-control` and `imageio` are supplied by `gym-aloha`, rather than duplicated in `lerobot-aloha`.

### `lerobot[test]`

* `mock-serial >=0.0.1,<0.1.0 ; sys_platform != 'win32'` is unavailable on conda-forge, so it is removed from both the `lerobot-test` output and the patched Python metadata. The output includes the three pytest dependencies; tests needing mock serial ports require a separate installation of `mock-serial`.

### `lerobot[all]`

* `pypi` has an additional dependency on `scipy` to "helps pip's resolver " (i.. help on `D1`), so in `conda-forge` we can remove the `scipy` dependency.
* In `conda-forge`, we remove from `lerobot[all]` all the extras that we do not package in `conda-forge`. The patched `[all]` removes only `reachy2`, `rebot`, `yam`, `hilserl`, `phone`, `libero` and `metaworld`, keeping it in sync with `lerobot-all`.

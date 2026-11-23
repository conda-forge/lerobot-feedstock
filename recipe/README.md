# `lerobot` conda-forge packaging

This README documents the design choices used in the `lerobot` conda-forge package.

If you are an agent updating the metadata or recipe for a release, update this file with the extras audit, skipped extras, and metadata modifications.

## Python extras: LeRobot 0.7.0 audit

The audit covers all **85 extras** in the tagged [upstream `pyproject.toml`](https://github.com/huggingface/lerobot/blob/v0.7.0/pyproject.toml), including dependency helpers, CLI bundles, and development tools. There are **74 extra outputs**, **11 extras blocked by unavailable dependencies**, and two packaging outputs (`lerobot` and `lerobot-tests`).

Each supported `lerobot[extra]` maps to `lerobot-extra`; underscores become hyphens. Each output installs the base package and exactly pinned sibling outputs for nested extras. Shared dependencies have their own helper outputs, matching upstream's dependency graph. Extra names that share requirements still have separate outputs so that installation instructions translate directly.

Package availability and compatible version ranges were checked against the [conda-forge package API](https://api.anaconda.org/package/conda-forge/eclipse-zenoh) on 2026-11-18. Availability is specific to dependency versions and platforms; `noarch` metapackages do not make their native dependencies available on every platform.

### Supported extra-to-output mapping

`viz` and `test` are partial: Foxglove and mock-serial are omitted as explained below. The other mappings preserve the extra requirements subject to the documented conda metadata changes.

| Upstream extra | Conda output |
| --- | --- |
| `lancedb` | `lerobot-lancedb` |
| `dataset` | `lerobot-dataset` |
| `training` | `lerobot-training` |
| `hardware` | `lerobot-hardware` |
| `viz` | `lerobot-viz` |
| `core_scripts` | `lerobot-core-scripts` |
| `evaluation` | `lerobot-evaluation` |
| `dataset_viz` | `lerobot-dataset-viz` |
| `av-dep` | `lerobot-av-dep` |
| `pygame-dep` | `lerobot-pygame-dep` |
| `placo-dep` | `lerobot-placo-dep` |
| `transformers-dep` | `lerobot-transformers-dep` |
| `sentencepiece-dep` | `lerobot-sentencepiece-dep` |
| `grpcio-dep` | `lerobot-grpcio-dep` |
| `accelerate-dep` | `lerobot-accelerate-dep` |
| `can-dep` | `lerobot-can-dep` |
| `peft-dep` | `lerobot-peft-dep` |
| `scipy-dep` | `lerobot-scipy-dep` |
| `diffusers-dep` | `lerobot-diffusers-dep` |
| `qwen-vl-utils-dep` | `lerobot-qwen-vl-utils-dep` |
| `matplotlib-dep` | `lerobot-matplotlib-dep` |
| `pyserial-dep` | `lerobot-pyserial-dep` |
| `rustypot-dep` | `lerobot-rustypot-dep` |
| `deepdiff-dep` | `lerobot-deepdiff-dep` |
| `pynput-dep` | `lerobot-pynput-dep` |
| `pyzmq-dep` | `lerobot-pyzmq-dep` |
| `timm-dep` | `lerobot-timm-dep` |
| `serial-motors` | `lerobot-serial-motors` |
| `feetech` | `lerobot-feetech` |
| `dynamixel` | `lerobot-dynamixel` |
| `damiao` | `lerobot-damiao` |
| `robstride` | `lerobot-robstride` |
| `openarms` | `lerobot-openarms` |
| `gamepad` | `lerobot-gamepad` |
| `hopejr` | `lerobot-hopejr` |
| `lekiwi` | `lerobot-lekiwi` |
| `unitree_g1` | `lerobot-unitree-g1` |
| `kinematics` | `lerobot-kinematics` |
| `intelrealsense` | `lerobot-intelrealsense` |
| `diffusion` | `lerobot-diffusion` |
| `wallx` | `lerobot-wallx` |
| `pi` | `lerobot-pi` |
| `dm05` | `lerobot-dm05` |
| `molmoact2` | `lerobot-molmoact2` |
| `smolvla` | `lerobot-smolvla` |
| `multi_task_dit` | `lerobot-multi-task-dit` |
| `groot` | `lerobot-groot` |
| `sarm` | `lerobot-sarm` |
| `robometer` | `lerobot-robometer` |
| `topreward` | `lerobot-topreward` |
| `xvla` | `lerobot-xvla` |
| `eo1` | `lerobot-eo1` |
| `longwam` | `lerobot-longwam` |
| `fastwam` | `lerobot-fastwam` |
| `flux3` | `lerobot-flux3` |
| `evo1` | `lerobot-evo1` |
| `fineart_vla` | `lerobot-fineart-vla` |
| `vla_jepa` | `lerobot-vla-jepa` |
| `lingbot_va` | `lerobot-lingbot-va` |
| `lingbot_vla_v2` | `lerobot-lingbot-vla-v2` |
| `wall_oss_05` | `lerobot-wall-oss-05` |
| `g05` | `lerobot-g05` |
| `lawam` | `lerobot-lawam` |
| `cosmos3` | `lerobot-cosmos3` |
| `remote` | `lerobot-remote` |
| `peft` | `lerobot-peft` |
| `annotations` | `lerobot-annotations` |
| `dev` | `lerobot-dev` |
| `notebook` | `lerobot-notebook` |
| `test` | `lerobot-test` |
| `video_benchmark` | `lerobot-video-benchmark` |
| `aloha` | `lerobot-aloha` |
| `pusht` | `lerobot-pusht` |
| `all` | `lerobot-all` |

### Development extras

`dev`, `test`, `video_benchmark`, and `notebook` have dedicated conda outputs. The patched `all` extra includes `dev`, `test`, and `video_benchmark`; `notebook` is included through `dev`. These are all the extras under upstream’s Development section. The [published PyPI 0.7.0 wheel metadata](https://pypi.org/pypi/lerobot/0.7.0/json) declares all 85 extras found in the tagged source, including these four; no additional source-only extras were found.

### Extras blocked by unavailable dependencies

The following dependencies were not available from conda-forge at audit time. Composite extras are also omitted when a required helper is unavailable; dependencies are not silently dropped to create those outputs.

| Upstream extra | Blocking dependency or extra |
| --- | --- |
| `lancedb-convert` | `lerobot-lancedb >=0.3.1,<0.4.0` |
| `motorbridge-dep` | `motorbridge >=0.5,<0.6` |
| `motorbridge-smart-servo-dep` | `motorbridge-smart-servo >=0.0.4,<0.1.0` |
| `reachy2` | `reachy2-sdk >=1.0.15,<1.1.0` |
| `rebot` | `motorbridge-dep and motorbridge-smart-servo-dep` |
| `yam` | `motorbridge-dep` |
| `phone` | `hebi-py >=2.8.0,<2.12.0 and teleop >=0.1.0,<0.2.0` |
| `fineart_vla_kernels` | `kernels >=0.14.1,<0.15.0` |
| `hilserl` | `gym-hil >=0.1.14,<0.2.0` |
| `libero` | `hf-libero >=0.1.4,<0.2.0 (Linux dependency)` |
| `metaworld` | `metaworld ==3.0.0` |

`lerobot-test` provides upstream's pytest, pytest-timeout, and pytest-cov dependencies. `mock-serial`, required by upstream outside Windows, is unavailable on conda-forge and is explicitly omitted from the output and patched Python metadata. Tests that use mock serial ports need that dependency installed separately. `lerobot-tests` remains the separate feedstock test bundle that runs selected upstream tests.

`fineart_vla_kernels` also requires `liger-kernel` on Linux. That package is available, but Hugging Face `kernels` in the required range is missing. `fineart_vla` itself is supported.

`vlabench`, `robomme`, and `robocasa` are explicitly not extras in upstream 0.7.0. They do not receive outputs. Upstream also no longer defines `async` or its old `lerobot.async_inference` API. Installing the old gRPC dependency bundle cannot restore it, so `lerobot-async` is dropped rather than retained as a compatibility alias. `lerobot-grpcio-dep` remains as a published dependency helper for runtime features such as `hilserl`, even though that feature is currently blocked by its unavailable `gym-hil` dependency.

### Installation examples

Install the outputs for the task and add the relevant policy, robot, motor, or environment output:

```sh
# Dataset tools
conda install -c conda-forge lerobot-dataset
# Training (choose the policy separately)
conda install -c conda-forge lerobot-training lerobot-smolvla
# Record, replay, calibrate, and teleoperate with Feetech motors
conda install -c conda-forge lerobot-core-scripts lerobot-feetech
# Dataset visualization with Rerun
conda install -c conda-forge lerobot-dataset-viz
# Remote inference transport plus a policy
conda install -c conda-forge lerobot-remote lerobot-pi
# Base evaluation plus a policy and a simulation environment
conda install -c conda-forge lerobot-evaluation lerobot-pi lerobot-pusht
```

`lerobot-all` follows the **supported subset of upstream's `all` dependency list**, not every output and not the complete upstream `lerobot[all]`. It includes development tools and the supported simulation extras because upstream `all` includes them. It excludes blocked extras, and upstream `all` itself does not include every extra (for example, `annotations`, `eo1`, `unitree_g1`, and `lancedb-convert`). Install these separately when supported. Prefer task-specific outputs to the large `all` bundle.

`lerobot-groot` requires `decord`, currently available on Unix but not Windows. Consequently, `lerobot-all`, which includes `groot`, also inherits that platform limitation. `eclipse-zenoh` 1.10.2 currently has x86-64 builds for Linux, macOS, and Windows; its availability limits `remote` on other architectures. The feedstock currently builds and tests on Linux x86-64.

`lerobot-unitree-g1` models the declared Python extra. Upstream comments out `unitree-sdk2` and requires separate SDK installation; the metapackage does not supply it. `flux3` declares only Transformers, so `lerobot-flux3` does not provide the separately documented NATTEN kernels. Hardware access, model weights, simulator assets, and optional accelerated kernels require their usual upstream setup.

The top-level `README.md` is generated by conda-smithy and lists outputs as a catalog. Keep installation guidance and packaging exceptions here.

## Package tests

`representative_tests` selects fast upstream tests that run on the CPU using local fixtures. The test bundle copies `tests/**` from the release and runs against the installed package. It does not need physical robots, cameras, model weights, or Hub downloads. The OpenCV tests use the four separately downloaded, checksum-verified PNG fixtures.

| Selection | Coverage |
| --- | --- |
| `cameras/test_opencv.py` | OpenCV loading, image reads, rotations, color conversion, camera lifecycle and error handling |
| `configs/test_plugin_loading.py` | Config/plugin discovery and CLI integration using temporary local modules |
| `scripts/test_edit_dataset_parsing.py` | Dataset editing CLI operation types, flags, and depth encoder config |
| `utils/test_random_utils.py` | Python, NumPy, and CPU PyTorch RNG serialization and checkpoint continuation |
| `transport/test_transport_utils.py` | Legacy gRPC chunking and tensor/object serialization |
| `transport/test_protocol_codec.py`, `transport/test_protocol_envelope.py` | Current remote protocol tensor/image encoding, payload limits, identity validation, and error handling; client queries are mocked |
| `utils/test_io_utils.py`, `utils/test_feature_spec.py` | JSON deserialization and feature shape/dtype validation |
| `test_streaming_compact.py` | Synthetic MP4 indexing, compact integer storage, and local sidecar/cache round trips |
| `envs/test_envs.py::test_factory[pusht]`, `envs/test_envs.py::test_env[pusht-PushT-v0-state]` | PushT environment construction and Gymnasium checks, including the older packaged simulator and Pymunk compatibility |
| `optim/test_schedulers.py` | Learning-rate scheduler construction, stepping, and state save/load |

The recipe explicitly selects CPU execution and deselects tests marked `gpu`. Each test has a 60-second timeout via `pytest-timeout`, supplied by `lerobot-test` through `lerobot-all`. Upstream's intentionally skipped zero-timeout camera test and its unavailable-MPS RNG test may still be reported as skipped on Linux.

The reviewed selection was run on Linux x86-64 with Python 3.11, CPU PyTorch 2.13.0, NumPy 1.26.0, SciPy 1.16.3, gym-pusht 0.1.6, and Pymunk 6.10.0, with Hub and dataset access in offline mode: **263 passed, 2 skipped, 1 GPU test deselected in 8.06 seconds**. This validates the patched release source with the same copied test layout as the recipe; it is not a full conda package build or a cross-platform run.

This is representative coverage, not a full validation of every extra. Physical hardware, live distributed transports, GPU policies, and model downloads are outside this selection. The LanceDB integration tests also require the unavailable `lerobot-lancedb` converter, so the recipe retains a separate Python 3.11 Lance backend compatibility smoke check instead.

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

### `lerobot[viz]`

* `pypi` requires `foxglove-sdk >=0.25.1,<0.26.0`, which is not available on conda-forge, so it is removed. `lerobot-viz` supplies Rerun visualization; Foxglove functionality is unavailable through this output.
* `rerun-sdk` bundles `rerun-notebook`, whose own (unconditional) PyPI metadata requires `ipykernel<7.0.0` (`D2`); conda-forge's `rerun-sdk` package does not declare this, so `ipykernel<7.0.0` is added explicitly to `lerobot-viz`.

### `lerobot[aloha]`

* `pypi` has an additional dependency on `lerobot[scipy-dep]` to "flatten the dependency tree" (i.e. help on `D1`), so in `conda-forge` we can remove the `lerobot[scipy-dep]` dependency.
* The minimum `gym-aloha` version is temporarily relaxed to 0.1.4 until 0.1.5 is available on conda-forge. The two releases contain the same environment code. Runtime dependencies such as `dm-control` and `imageio` are supplied by `gym-aloha`, rather than duplicated with historical bounds in `lerobot-aloha`.

### `lerobot[pusht]`

* The minimum `gym-pusht` version is temporarily relaxed to 0.1.6 until 0.1.8 is available on conda-forge. The direct `pymunk >=6.6.0,<7.0.0` requirement is retained in the recipe and patched Python metadata: `gym-pusht` 0.1.6 calls `Space.add_collision_handler`, which Pymunk 7 removed. Its transitive dependency alone does not enforce this compatibility bound. Version 0.1.6 returns `info["goal_pose"]` as a tuple rather than an array; installing 0.1.8 restores the upstream behavior.

### Package-name and platform translations

* PyPI `msgpack` maps to conda `msgpack-python`; PyPI `meshcat` maps to `meshcat-python`; PyPI `matplotlib` maps to `matplotlib-base`.
* `groot` restricts PyPI `decord` to x86-64 because of wheel availability. The conda output depends on `decord >=0.6.0,<1.0.0` on every architecture where conda-forge supplies it, including ARM. No Windows builds are available.
* Trackio's platform-specific `gpu` / `apple-gpu` extras are represented by `trackio`, `psutil`, and `nvidia-ml-py` in the noarch training output, preserving the previous recipe behavior.

### `lerobot[test]`

* `mock-serial >=0.0.1,<0.1.0` is unavailable on conda-forge, so it is removed. The output includes the three pytest dependencies; tests needing mock serial ports require a separate installation of `mock-serial`.

### `lerobot[all]`

* `pypi` has an additional dependency on `scipy` to "helps pip's resolver " (i.. help on `D1`), so in `conda-forge` we can remove the `scipy` dependency.
* In `conda-forge`, we remove from `lerobot[all]` all the extras that we do not package in `conda-forge`.

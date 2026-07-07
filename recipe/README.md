# `lerobot` conda-forge packaging 

This README documents the design choices used in the `lerobot` conda-forge package.

If you are an agent that is updating metadata and recipe for a new release of `lerobot`, please always update this file updating the skipped extras and metadata modifications.

## Python extras

In the `conda-forge` packaging of `lerobot`, Python extras like  `lerobot[pygame-dep]` are modeled by separate `lerobot-pygame-dep` output. Note that any underscores in the upstream extra name are converted to hyphens in the conda-forge package name (e.g. `lerobot[multi_task_dit]` becomes `lerobot-multi-task-dit`).

Almost all the extra in the lerobot package are packaged as `lerobot-*` outputs in this recipe, with the exception of:
* `dev`, `test`, `video_benchmark` and `notebook`: as they are listed as "dev" extras
* `reachy2`: as `reachy2-sdk` is not available on conda-forge
* `feetech`: as direct dependency `feetech-servo-sdk` is not available on conda-forge
* `hopejr`: as indirect dependency `feetech-servo-sdk` is not available on conda-forge
* `lekiwi`: as indirect dependency `feetech-servo-sdk` is not available on conda-forge
* `libero`: as the `hf-libero` dependency is not available on conda-forge
* `metaworld`: as the `metaworld` dependency is not available on conda-forge
* `groot`: as it is not clear which `flash-attn` and `decord` dependencies 
         are actually there, and it is not included in `lerobot[all]`
* `phone`: as `teleop` and `hebi-py` are not in conda-forge
* `hilserl`: as it requires `gym-hil` that is not in conda-forge
* `rebot`: as `motorbridge` and `motorbridge-smart-servo` are not available on conda-forge
* `dataset`, `training`, `hardware`, `viz`, `core_scripts`, `evaluation` and `dataset_viz`: these upstream feature-scoped/composite extras back the CLI scripts exposed as entry points on the base `lerobot` output (e.g. `lerobot-record`, `lerobot-train`, `lerobot-dataset-viz`), but their run dependencies are not packaged as a dedicated output; install the underlying deps manually if you need those scripts
* `unitree_g1`, `wallx`, `sarm`, `robometer`, `eo1`, `vla_jepa`, `diffusion`, `molmoact2`, `topreward`, `fastwam`, `evo1`, `lingbot_va` and `annotations`: new extras introduced upstream that are not yet packaged here; their dependencies (including `qwen-vl-utils`, now available on conda-forge) are otherwise packageable, this is just pending work

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
* A `numpy` upper bound is present for simplifying solver life in `pypi` (`D1`), upper bound that is removed in `conda-forge`.
* The `lerobot` output only carries the true unconditional `dependencies` from `pyproject.toml`. The CLI scripts backed by the `dataset`, `training`, `hardware` and `viz` extras (e.g. `lerobot-record`, `lerobot-train`, `lerobot-dataset-viz`) are still exposed as entry points on the base package, but their extra run dependencies are not force-installed; users who need those scripts install the relevant extra dependencies themselves.

### `lerobot[intelrealsense]`

* `pypi` has an upper bound for the `pyrealsense2` package due to `D4`, that is not necessary in `conda-forge`. As `pyrealsense2` is available on all conda-forge platforms, all platform specific selectors are removed. Furthermore, the fourth version number (i.e. y in x.x.x.y) of `pyrealsense2` should be dropped as `pypi` specific detail.

### `lerobot[placo-dep]`

* `pypi` has an upperbound for `placo` due to `D1`, `D4` and `D5`, that is not required in `conda-forge`, so the upper bound is removed.

### `lerobot[aloha]`

* `pypi` has an additional dependency on `lerobot[scipy-dep]` to "flatten the dependency tree" (i.e. help on `D1`), so in `conda-forge` we can remove the `lerobot[scipy-dep]` dependency.

### `lerobot[all]`

* `pypi` has an additional dependency on `scipy` to "helps pip's resolver " (i.. help on `D1`), so in `conda-forge` we can remove the `scipy` dependency.
* In `conda-forge`, we remove from `lerobot[all]` all the extras that we do not package in `conda-forge`.

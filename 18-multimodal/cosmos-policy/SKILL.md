---
name: evaluating-cosmos-policy
description: Evaluates NVIDIA Cosmos Policy on LIBERO and RoboCasa simulation environments. Use when setting up cosmos-policy for robot manipulation evaluation, running headless GPU evaluations with EGL rendering, or profiling inference latency on cluster or local GPU machines.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Cosmos Policy, VLA, Robotics, LIBERO, RoboCasa, Simulation, Evaluation, Profiling, EGL Rendering]
dependencies: [torch>=2.1.0, mujoco>=3.0.0, robosuite>=1.4.0, "robocasa @ git+https://github.com/moojink/robocasa-cosmos-policy.git", transformers>=4.40.0, "cosmos-policy @ git+https://github.com/NVlabs/cosmos-policy.git"]
---

# Cosmos Policy Evaluation

Evaluation workflows for NVIDIA Cosmos Policy on LIBERO and RoboCasa simulation environments from the public `cosmos-policy` repository. Covers blank-machine setup, headless GPU evaluation, and inference profiling.

## Quick start

Run a minimal LIBERO evaluation using the official public eval module:

```bash
uv run --extra cu128 --group libero --python 3.10 \
  python -m cosmos_policy.experiments.robot.libero.run_libero_eval \
    --config cosmos_predict2_2b_480p_libero__inference_only \
    --ckpt_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B \
    --config_file cosmos_policy/config/config.py \
    --use_wrist_image True \
    --use_proprio True \
    --normalize_proprio True \
    --unnormalize_actions True \
    --dataset_stats_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B/libero_dataset_statistics.json \
    --t5_text_embeddings_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B/libero_t5_embeddings.pkl \
    --trained_with_image_aug True \
    --chunk_size 16 \
    --num_open_loop_steps 16 \
    --task_suite_name libero_10 \
    --num_trials_per_task 1 \
    --local_log_dir cosmos_policy/experiments/robot/libero/logs/ \
    --seed 195 \
    --randomize_seed False \
    --deterministic True \
    --run_id_note smoke \
    --ar_future_prediction False \
    --ar_value_prediction False \
    --use_jpeg_compression True \
    --flip_images True \
    --num_denoising_steps_action 5 \
    --num_denoising_steps_future_state 1 \
    --num_denoising_steps_value 1 \
    --data_collection False
```

## Core concepts

**What Cosmos Policy is**: NVIDIA Cosmos Policy is a vision-language-action (VLA) model that uses Cosmos Tokenizer to encode visual observations into discrete tokens, then predicts robot actions conditioned on language instructions and visual context.

**Key architecture choices**:

| Component | Design |
|-----------|--------|
| Visual encoder | Cosmos Tokenizer (discrete tokens) |
| Language conditioning | Cross-attention to language embeddings |
| Action prediction | Autoregressive action token generation |

**Public command surface**: The supported evaluation entrypoints are `cosmos_policy.experiments.robot.libero.run_libero_eval` and `cosmos_policy.experiments.robot.robocasa.run_robocasa_eval`. Keep reproduction notes anchored to these public modules and their documented flags.

## Compute requirements

| Task | GPU | VRAM | Typical wall time |
|------|-----|------|-------------------|
| LIBERO smoke eval (1 trial) | 1x A40/A100 | ~16 GB | 5-10 min |
| LIBERO full eval (50 trials) | 1x A40/A100 | ~16 GB | 2-4 hours |
| RoboCasa single-task (2 trials) | 1x A40/A100 | ~18 GB | 10-15 min |
| RoboCasa all-tasks | 1x A40/A100 | ~18 GB | 4-8 hours |

## When to use vs alternatives

**Use this skill when:**
- Evaluating NVIDIA Cosmos Policy on LIBERO or RoboCasa benchmarks
- Profiling inference latency and throughput for Cosmos Policy
- Setting up headless EGL rendering for robot simulation on GPU clusters

**Use alternatives when:**
- Training or fine-tuning Cosmos Policy from scratch (use official Cosmos training docs)
- Working with OpenVLA-based policies (use `fine-tuning-openvla-oft`)
- Working with Physical Intelligence pi0 models (use `fine-tuning-serving-openpi`)
- Running real-robot evaluation rather than simulation

---

## Workflow 1: LIBERO evaluation

Copy this checklist and track progress:

```text
LIBERO Eval Progress:
- [ ] Step 1: Install environment and dependencies
- [ ] Step 2: Configure headless EGL rendering
- [ ] Step 3: Run smoke evaluation
- [ ] Step 4: Validate outputs and parse results
- [ ] Step 5: Run full benchmark if smoke passes
```

**Step 1: Install environment**

```bash
git clone https://github.com/NVlabs/cosmos-policy.git
cd cosmos-policy
# Follow SETUP.md to build and enter the supported Docker container.
# Then, inside the container:
uv sync --extra cu128 --group libero --python 3.10
```

**Step 2: Configure headless rendering**

```bash
export CUDA_VISIBLE_DEVICES=0
export MUJOCO_EGL_DEVICE_ID=0
export MUJOCO_GL=egl
export PYOPENGL_PLATFORM=egl
```

**Step 3: Run smoke evaluation**

```bash
uv run --extra cu128 --group libero --python 3.10 \
  python -m cosmos_policy.experiments.robot.libero.run_libero_eval \
    --config cosmos_predict2_2b_480p_libero__inference_only \
    --ckpt_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B \
    --config_file cosmos_policy/config/config.py \
    --use_wrist_image True \
    --use_proprio True \
    --normalize_proprio True \
    --unnormalize_actions True \
    --dataset_stats_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B/libero_dataset_statistics.json \
    --t5_text_embeddings_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B/libero_t5_embeddings.pkl \
    --trained_with_image_aug True \
    --chunk_size 16 \
    --num_open_loop_steps 16 \
    --task_suite_name libero_10 \
    --num_trials_per_task 1 \
    --local_log_dir cosmos_policy/experiments/robot/libero/logs/ \
    --seed 195 \
    --randomize_seed False \
    --deterministic True \
    --run_id_note smoke \
    --ar_future_prediction False \
    --ar_value_prediction False \
    --use_jpeg_compression True \
    --flip_images True \
    --num_denoising_steps_action 5 \
    --num_denoising_steps_future_state 1 \
    --num_denoising_steps_value 1 \
    --data_collection False
```

**Step 4: Validate and parse results**

```python
import json
import glob

# Find latest evaluation result from the official log directory
log_files = sorted(glob.glob("cosmos_policy/experiments/robot/libero/logs/**/*.json", recursive=True))
with open(log_files[-1]) as f:
    results = json.load(f)

print(results)
```

**Step 5: Scale up**

Run across all four LIBERO task suites with 50 trials:

```bash
for suite in libero_spatial libero_object libero_goal libero_10; do
  uv run --extra cu128 --group libero --python 3.10 \
    python -m cosmos_policy.experiments.robot.libero.run_libero_eval \
      --config cosmos_predict2_2b_480p_libero__inference_only \
      --ckpt_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B \
      --config_file cosmos_policy/config/config.py \
      --use_wrist_image True \
      --use_proprio True \
      --normalize_proprio True \
      --unnormalize_actions True \
      --dataset_stats_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B/libero_dataset_statistics.json \
      --t5_text_embeddings_path nvidia/Cosmos-Policy-LIBERO-Predict2-2B/libero_t5_embeddings.pkl \
      --trained_with_image_aug True \
      --chunk_size 16 \
      --num_open_loop_steps 16 \
      --task_suite_name "$suite" \
      --num_trials_per_task 50 \
      --local_log_dir cosmos_policy/experiments/robot/libero/logs/ \
      --seed 195 \
      --randomize_seed False \
      --deterministic True \
      --run_id_note "suite_${suite}" \
      --ar_future_prediction False \
      --ar_value_prediction False \
      --use_jpeg_compression True \
      --flip_images True \
      --num_denoising_steps_action 5 \
      --num_denoising_steps_future_state 1 \
      --num_denoising_steps_value 1 \
      --data_collection False
done
```

---

## Workflow 2: RoboCasa evaluation

Copy this checklist and track progress:

```text
RoboCasa Eval Progress:
- [ ] Step 1: Install RoboCasa assets and verify macros
- [ ] Step 2: Run single-task smoke evaluation
- [ ] Step 3: Validate outputs
- [ ] Step 4: Expand to multi-task runs
```

**Step 1: Install RoboCasa**

```bash
git clone https://github.com/moojink/robocasa-cosmos-policy.git
uv pip install -e robocasa-cosmos-policy
python -m robocasa.scripts.setup_macros
python -m robocasa.scripts.download_kitchen_assets
```

This fork installs the `robocasa` Python package expected by Cosmos Policy while preserving the patched environment changes used in the public RoboCasa eval path. Verify `macros_private.py` exists and paths are correct.

**Step 2: Single-task smoke evaluation**

```bash
uv run --extra cu128 --group robocasa --python 3.10 \
  python -m cosmos_policy.experiments.robot.robocasa.run_robocasa_eval \
    --config cosmos_predict2_2b_480p_robocasa_50_demos_per_task__inference \
    --ckpt_path nvidia/Cosmos-Policy-RoboCasa-Predict2-2B \
    --config_file cosmos_policy/config/config.py \
    --use_wrist_image True \
    --num_wrist_images 1 \
    --use_proprio True \
    --normalize_proprio True \
    --unnormalize_actions True \
    --dataset_stats_path nvidia/Cosmos-Policy-RoboCasa-Predict2-2B/robocasa_dataset_statistics.json \
    --t5_text_embeddings_path nvidia/Cosmos-Policy-RoboCasa-Predict2-2B/robocasa_t5_embeddings.pkl \
    --trained_with_image_aug True \
    --chunk_size 32 \
    --num_open_loop_steps 16 \
    --task_name TurnOffMicrowave \
    --obj_instance_split A \
    --num_trials_per_task 2 \
    --local_log_dir cosmos_policy/experiments/robot/robocasa/logs/ \
    --seed 195 \
    --randomize_seed False \
    --deterministic True \
    --run_id_note smoke \
    --use_variance_scale False \
    --use_jpeg_compression True \
    --flip_images True \
    --num_denoising_steps_action 5 \
    --num_denoising_steps_future_state 1 \
    --num_denoising_steps_value 1 \
    --data_collection False
```

**Step 3: Validate outputs**

- Confirm the eval log prints the expected task name, object split, and checkpoint/config values.
- Inspect the final `Success rate:` line in the log.

**Step 4: Expand scope**

Increase `--num_trials_per_task` or add more tasks. Keep `--obj_instance_split` fixed across repeated runs for comparability.

---

## Workflow 3: Blank-machine cluster launch

```text
Cluster Launch Progress:
- [ ] Step 1: Clone the public repo and enter the supported runtime
- [ ] Step 2: Sync the benchmark-specific dependency group
- [ ] Step 3: Export rendering and cache environment variables before eval
```

**Step 1: Clone and enter the supported runtime**

```bash
git clone https://github.com/NVlabs/cosmos-policy.git
cd cosmos-policy
# Follow SETUP.md, start the Docker container, and enter it before continuing.
```

**Step 2: Sync dependencies**

```bash
uv sync --extra cu128 --group libero --python 3.10
# or, for RoboCasa:
uv sync --extra cu128 --group robocasa --python 3.10
# then install the Cosmos-compatible RoboCasa fork:
git clone https://github.com/moojink/robocasa-cosmos-policy.git
uv pip install -e robocasa-cosmos-policy
```

**Step 3: Export runtime environment**

```bash
export CUDA_VISIBLE_DEVICES=0
export MUJOCO_EGL_DEVICE_ID=0
export MUJOCO_GL=egl
export PYOPENGL_PLATFORM=egl
export HF_HOME=${HF_HOME:-$HOME/.cache/huggingface}
export TRANSFORMERS_CACHE=${TRANSFORMERS_CACHE:-$HF_HOME}
```

---

## Expected performance benchmarks

Reference values from official evaluation (tied to specific setup and seeds):

| Task Suite | Success Rate | Notes |
|-----------|-------------|-------|
| LIBERO-Spatial | 98.1% | Official LIBERO spatial result |
| LIBERO-Object | 100.0% | Official LIBERO object result |
| LIBERO-Goal | 98.2% | Official LIBERO goal result |
| LIBERO-Long | 97.6% | Official LIBERO long-horizon result |
| LIBERO-Average | 98.5% | Official average across LIBERO suites |
| RoboCasa | 67.1% | Official RoboCasa average result |

**Reproduction note**: Published success rates still depend on checkpoint choice, task suite, seeds, and simulator setup. Record the exact command and environment alongside any reported number.

---

## Non-negotiable rules

- **EGL alignment**: Always set `CUDA_VISIBLE_DEVICES`, `MUJOCO_EGL_DEVICE_ID`, `MUJOCO_GL=egl`, and `PYOPENGL_PLATFORM=egl` together on headless GPU nodes.
- **Official runtime first**: If host-Python installs hit binary compatibility issues, fall back to the supported container workflow from `SETUP.md` before debugging package internals.
- **Cache consistency**: Use the same cache directory across setup and eval so Hugging Face and dependency caches are reused.
- **Run comparability**: Keep task name, object split, seed, and trial count fixed across repeated runs.

---

## Common issues

**Issue: binary compatibility or loader failures on host Python**

Fix: rerun inside the official container/runtime from `SETUP.md`. Do not assume host-package rebuilds will match the public release environment.

**Issue: LIBERO prompts for config path in a non-interactive shell**

Fix: pre-create `LIBERO_CONFIG_PATH/config.yaml`:

```python
import os, yaml

config_dir = os.path.expanduser("~/.libero")
os.makedirs(config_dir, exist_ok=True)
with open(os.path.join(config_dir, "config.yaml"), "w") as f:
    yaml.dump({"benchmark_root": "/path/to/libero/datasets"}, f)
```

**Issue: EGL initialization or shutdown noise**

Fix: align EGL environment variables first. Treat teardown-only `EGL_NOT_INITIALIZED` warnings as low-signal unless the job exits non-zero.

**Issue: Kitchen object sampling NaNs or asset lookup failures in RoboCasa**

Fix: rerun asset setup and confirm the patched robocasa install is intact:

```bash
python -m robocasa.scripts.download_kitchen_assets
python -c "import robocasa; print(robocasa.__file__)"
```

**Issue: MuJoCo rendering mismatch**

Fix: verify GPU device alignment:

```python
import os
cuda_dev = os.environ.get("CUDA_VISIBLE_DEVICES", "not set")
egl_dev = os.environ.get("MUJOCO_EGL_DEVICE_ID", "not set")
assert cuda_dev == egl_dev, f"GPU mismatch: CUDA={cuda_dev}, EGL={egl_dev}"
print(f"Rendering on GPU {cuda_dev}")
```

---

## Advanced topics

**LIBERO command matrix**: See [references/libero-commands.md](references/libero-commands.md)
**RoboCasa command matrix**: See [references/robocasa-commands.md](references/robocasa-commands.md)

## Resources

- Cosmos Policy repository: https://github.com/NVlabs/cosmos-policy
- LIBERO benchmark: https://github.com/Lifelong-Robot-Learning/LIBERO
- Cosmos-compatible RoboCasa fork: https://github.com/moojink/robocasa-cosmos-policy
- Upstream RoboCasa project: https://github.com/robocasa/robocasa
- MuJoCo documentation: https://mujoco.readthedocs.io/

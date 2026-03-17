# LIBERO Command Matrix

Command variations for running Cosmos Policy LIBERO evaluation on local machines, interactive GPU shells, or batch systems. All commands use the official public `cosmos_policy.experiments.robot.libero.run_libero_eval` module.

## Preferred path: interactive GPU shell

Acquire one GPU, then run evaluations directly:

```bash
# Slurm example
srun --partition=gpu --gpus-per-node=1 \
  --time=01:00:00 --mem=64G --cpus-per-task=8 --pty bash

cd /path/to/cosmos-policy

# Set headless rendering environment
export CUDA_VISIBLE_DEVICES=0
export MUJOCO_EGL_DEVICE_ID=0
export MUJOCO_GL=egl
export PYOPENGL_PLATFORM=egl

# Smoke eval (1 trial, single suite)
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

# Full eval (50 trials, single suite)
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
    --num_trials_per_task 50 \
    --local_log_dir cosmos_policy/experiments/robot/libero/logs/ \
    --seed 195 \
    --randomize_seed False \
    --deterministic True \
    --run_id_note full \
    --ar_future_prediction False \
    --ar_value_prediction False \
    --use_jpeg_compression True \
    --flip_images True \
    --num_denoising_steps_action 5 \
    --num_denoising_steps_future_state 1 \
    --num_denoising_steps_value 1 \
    --data_collection False

# All four suites
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

## Local GPU workstation path

Skip `srun` and run the same `uv run ... python -m` commands directly. Set EGL env vars first. If host-Python binaries are unstable, prefer the official container/runtime from `SETUP.md`.

## Blank-machine setup reminder

Before running any command below:

- clone `https://github.com/NVlabs/cosmos-policy.git`
- follow `SETUP.md` and enter the supported Docker container
- run `uv sync --extra cu128 --group libero --python 3.10`

## Batch fallback

Only use batch submission after the direct command path works interactively:

```bash
sbatch --partition=gpu --time=04:00:00 --wrap="
  export CUDA_VISIBLE_DEVICES=0 MUJOCO_EGL_DEVICE_ID=0 MUJOCO_GL=egl PYOPENGL_PLATFORM=egl
  cd /path/to/cosmos-policy
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
      --num_trials_per_task 50 \
      --local_log_dir cosmos_policy/experiments/robot/libero/logs/ \
      --seed 195 \
      --randomize_seed False \
      --deterministic True \
      --run_id_note batch \
      --ar_future_prediction False \
      --ar_value_prediction False \
      --use_jpeg_compression True \
      --flip_images True \
      --num_denoising_steps_action 5 \
      --num_denoising_steps_future_state 1 \
      --num_denoising_steps_value 1 \
      --data_collection False
"
```

## High-signal gotchas

- If host-Python binaries fail to import cleanly, return to the official container/runtime from `SETUP.md` before debugging Python package state.
- Always align `CUDA_VISIBLE_DEVICES` and `MUJOCO_EGL_DEVICE_ID` to the same GPU index.
- Keep the full config block with the command because upstream eval depends on many explicit flags, not only task suite and trial count.

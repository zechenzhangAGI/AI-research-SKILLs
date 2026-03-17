# RoboCasa Command Matrix

Command variations for running Cosmos Policy RoboCasa evaluation on local machines, interactive GPU shells, or batch systems. All commands use the official public `cosmos_policy.experiments.robot.robocasa.run_robocasa_eval` module.

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

# Smoke eval on one task (2 trials)
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

# Full eval on one task (50 trials)
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
    --num_trials_per_task 50 \
    --local_log_dir cosmos_policy/experiments/robot/robocasa/logs/ \
    --seed 195 \
    --randomize_seed False \
    --deterministic True \
    --run_id_note full \
    --use_variance_scale False \
    --use_jpeg_compression True \
    --flip_images True \
    --num_denoising_steps_action 5 \
    --num_denoising_steps_future_state 1 \
    --num_denoising_steps_value 1 \
    --data_collection False
```

## Local GPU workstation path

Skip `srun` and run the same `uv run ... python -m` commands directly. Set EGL env vars first. If host-Python binaries are unstable, prefer the official container/runtime from `SETUP.md`.

## Blank-machine setup reminder

Before running any command below:

- clone `https://github.com/NVlabs/cosmos-policy.git`
- follow `SETUP.md` and enter the supported Docker container
- run `uv sync --extra cu128 --group robocasa --python 3.10`
- clone `https://github.com/moojink/robocasa-cosmos-policy.git` and install it with `uv pip install -e robocasa-cosmos-policy`
- run `python -m robocasa.scripts.setup_macros` and `python -m robocasa.scripts.download_kitchen_assets` before the first eval

## Batch fallback

Only use batch submission after the direct command path works interactively:

```bash
sbatch --partition=gpu --time=01:00:00 --wrap="
  export CUDA_VISIBLE_DEVICES=0 MUJOCO_EGL_DEVICE_ID=0 MUJOCO_GL=egl PYOPENGL_PLATFORM=egl
  cd /path/to/cosmos-policy
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
      --num_trials_per_task 50 \
      --local_log_dir cosmos_policy/experiments/robot/robocasa/logs/ \
      --seed 195 \
      --randomize_seed False \
      --deterministic True \
      --run_id_note batch \
      --use_variance_scale False \
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
- Keep task name, object split, seed, and trial count fixed across repeated runs for comparability.
- Always align `CUDA_VISIBLE_DEVICES` and `MUJOCO_EGL_DEVICE_ID` to the same GPU index.

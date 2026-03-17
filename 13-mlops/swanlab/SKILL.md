---
name: experiment-tracking-swanlab
description: Provides guidance for experiment tracking with SwanLab. Use when you need open-source run tracking, local or self-hosted dashboards, and lightweight media logging for ML workflows.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [MLOps, SwanLab, Experiment Tracking, Open Source, Visualization, PyTorch, Transformers, PyTorch Lightning, Fastai, Self-Hosted]
dependencies: [swanlab>=0.7.11, pillow>=9.0.0, soundfile>=0.12.0]
---

# SwanLab: Open-Source Experiment Tracking

## When to Use This Skill

Use SwanLab when you need to:
- **Track ML experiments** with metrics, configs, tags, and descriptions
- **Visualize training** with scalar charts and logged media
- **Compare runs** across seeds, checkpoints, and hyperparameters
- **Work locally or self-hosted** instead of depending on managed SaaS
- **Integrate** with PyTorch, Transformers, PyTorch Lightning, or Fastai

**Deployment**: Cloud, local, or self-hosted | **Media**: images, audio, text, GIFs, point clouds, molecules | **Integrations**: PyTorch, Transformers, PyTorch Lightning, Fastai

## Installation

```bash
# Install SwanLab plus the media dependencies used in this skill
pip install "swanlab>=0.7.11" "pillow>=9.0.0" "soundfile>=0.12.0"

# Add local dashboard support for mode="local" and swanlab watch
pip install "swanlab[dashboard]>=0.7.11"

# Optional framework integrations
pip install transformers pytorch-lightning fastai

# Login for cloud or self-hosted usage
swanlab login
```

`pillow` and `soundfile` are the media dependencies used by the Image and Audio examples in this skill. `swanlab[dashboard]` adds the local dashboard dependency required by `mode="local"` and `swanlab watch`.

## Quick Start

### Basic Experiment Tracking

```python
import swanlab

run = swanlab.init(
    project="my-project",
    experiment_name="baseline",
    config={
        "learning_rate": 1e-3,
        "epochs": 10,
        "batch_size": 32,
        "model": "resnet18",
    },
)

for epoch in range(run.config.epochs):
    train_loss = train_epoch()
    val_loss = validate()

    swanlab.log(
        {
            "train/loss": train_loss,
            "val/loss": val_loss,
            "epoch": epoch,
        }
    )

run.finish()
```

### With PyTorch

```python
import torch
import torch.nn as nn
import torch.optim as optim
import swanlab

run = swanlab.init(
    project="pytorch-demo",
    experiment_name="mnist-mlp",
    config={
        "learning_rate": 1e-3,
        "batch_size": 64,
        "epochs": 10,
        "hidden_size": 128,
    },
)

model = nn.Sequential(
    nn.Flatten(),
    nn.Linear(28 * 28, run.config.hidden_size),
    nn.ReLU(),
    nn.Linear(run.config.hidden_size, 10),
)
optimizer = optim.Adam(model.parameters(), lr=run.config.learning_rate)
criterion = nn.CrossEntropyLoss()

for epoch in range(run.config.epochs):
    model.train()
    for batch_idx, (data, target) in enumerate(train_loader):
        optimizer.zero_grad()
        logits = model(data)
        loss = criterion(logits, target)
        loss.backward()
        optimizer.step()

        if batch_idx % 100 == 0:
            swanlab.log(
                {
                    "train/loss": loss.item(),
                    "train/epoch": epoch,
                    "train/batch": batch_idx,
                }
            )

run.finish()
```

## Core Concepts

### 1. Projects and Experiments

**Project**: Collection of related experiments  
**Experiment**: Single execution of a training or evaluation workflow

```python
import swanlab

run = swanlab.init(
    project="image-classification",
    experiment_name="resnet18-seed42",
    description="Baseline run on ImageNet subset",
    tags=["baseline", "resnet18"],
    config={
        "model": "resnet18",
        "seed": 42,
        "batch_size": 64,
        "learning_rate": 3e-4,
    },
)

print(run.id)
print(run.config.learning_rate)
```

### 2. Configuration Tracking

```python
config = {
    "model": "resnet18",
    "seed": 42,
    "batch_size": 64,
    "learning_rate": 3e-4,
    "epochs": 20,
}

run = swanlab.init(project="my-project", config=config)

learning_rate = run.config.learning_rate
batch_size = run.config.batch_size
```

### 3. Metric Logging

```python
# Log scalars
swanlab.log({"loss": 0.42, "accuracy": 0.91})

# Log multiple metrics
swanlab.log(
    {
        "train/loss": train_loss,
        "train/accuracy": train_acc,
        "val/loss": val_loss,
        "val/accuracy": val_acc,
        "lr": current_lr,
        "epoch": epoch,
    }
)

# Log with custom step
swanlab.log({"loss": loss}, step=global_step)
```

### 4. Media and Chart Logging

```python
import numpy as np
import swanlab

# Image
image = np.random.randint(0, 255, (224, 224, 3), dtype=np.uint8)
swanlab.log({"examples/image": swanlab.Image(image, caption="Augmented sample")})

# Audio
wave = np.sin(np.linspace(0, 8 * np.pi, 16000)).astype("float32")
swanlab.log({"examples/audio": swanlab.Audio(wave, sample_rate=16000)})

# Text
swanlab.log({"examples/text": swanlab.Text("Training notes for this run.")})

# GIF video
swanlab.log({"examples/video": swanlab.Video("predictions.gif", caption="Validation rollout")})

# Point cloud
points = np.random.rand(128, 3).astype("float32")
swanlab.log({"examples/point_cloud": swanlab.Object3D(points, caption="Point cloud sample")})

# Molecule
swanlab.log({"examples/molecule": swanlab.Molecule.from_smiles("CCO", caption="Ethanol")})
```

```python
# Custom chart with swanlab.echarts
line = swanlab.echarts.Line()
line.add_xaxis(["epoch-1", "epoch-2", "epoch-3"])
line.add_yaxis("train/loss", [0.92, 0.61, 0.44])
line.set_global_opts(
    title_opts=swanlab.echarts.options.TitleOpts(title="Training Loss")
)

swanlab.log({"charts/loss_curve": line})
```

See [references/visualization.md](references/visualization.md) for more chart and media patterns.

### 5. Local and Self-Hosted Workflows

```python
import os
import swanlab

# Self-hosted or cloud login
swanlab.login(
    api_key=os.environ["SWANLAB_API_KEY"],
    host="http://your-server:5092",
)

# Local-only logging
run = swanlab.init(
    project="offline-demo",
    mode="local",
    logdir="./swanlog",
)

swanlab.log({"loss": 0.35, "epoch": 1})
run.finish()
```

```bash
# View local logs
swanlab watch -l ./swanlog

# Sync local logs later
swanlab sync ./swanlog
```

## Integration Examples

### HuggingFace Transformers

```python
from transformers import Trainer, TrainingArguments

training_args = TrainingArguments(
    output_dir="./results",
    per_device_train_batch_size=8,
    evaluation_strategy="epoch",
    logging_steps=50,
    report_to="swanlab",
    run_name="bert-finetune",
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
)

trainer.train()
```

See [references/integrations.md](references/integrations.md) for callback-based setups and additional framework patterns.

### PyTorch Lightning

```python
import pytorch_lightning as pl
from swanlab.integration.pytorch_lightning import SwanLabLogger

swanlab_logger = SwanLabLogger(
    project="lightning-demo",
    experiment_name="mnist-classifier",
    config={"batch_size": 64, "max_epochs": 10},
)

trainer = pl.Trainer(
    logger=swanlab_logger,
    max_epochs=10,
    accelerator="auto",
)

trainer.fit(model, train_loader, val_loader)
```

### Fastai

```python
from fastai.vision.all import accuracy, resnet34, vision_learner
from swanlab.integration.fastai import SwanLabCallback

learn = vision_learner(dls, resnet34, metrics=accuracy)
learn.fit(
    5,
    cbs=[
        SwanLabCallback(
            project="fastai-demo",
            experiment_name="pets-classification",
            config={"arch": "resnet34", "epochs": 5},
        )
    ],
)
```

See [references/integrations.md](references/integrations.md) for fuller framework examples.

## Best Practices

### 1. Use Stable Metric Names

```python
# Good: grouped metric namespaces
swanlab.log({
    "train/loss": train_loss,
    "train/accuracy": train_acc,
    "val/loss": val_loss,
    "val/accuracy": val_acc,
})

# Avoid mixing flat and grouped names for the same metric family
```

### 2. Initialize Early and Capture Config Once

```python
run = swanlab.init(
    project="image-classification",
    experiment_name="resnet18-baseline",
    config={
        "model": "resnet18",
        "learning_rate": 3e-4,
        "batch_size": 64,
        "seed": 42,
    },
)
```

### 3. Save Checkpoints Locally

```python
import torch
import swanlab

checkpoint_path = "checkpoints/best.pth"
torch.save(model.state_dict(), checkpoint_path)

swanlab.log(
    {
        "best/val_accuracy": best_val_accuracy,
        "artifacts/checkpoint_path": swanlab.Text(checkpoint_path),
    }
)
```

### 4. Use Local Mode for Offline-First Workflows

```python
run = swanlab.init(project="offline-demo", mode="local", logdir="./swanlog")
# ... training code ...
run.finish()

# Inspect later with: swanlab watch -l ./swanlog
```

### 5. Keep Advanced Patterns in References

- Use [references/visualization.md](references/visualization.md) for advanced chart and media patterns
- Use [references/integrations.md](references/integrations.md) for callback-based and framework-specific integration details

## Resources

- [Official docs (Chinese)](https://docs.swanlab.cn)
- [Official docs (English)](https://docs.swanlab.cn/en)
- [GitHub repo](https://github.com/SwanHubX/SwanLab)
- [Self-hosted repo](https://github.com/SwanHubX/self-hosted)

## See Also

- [references/integrations.md](references/integrations.md) - Framework-specific examples
- [references/visualization.md](references/visualization.md) - Charts and media logging patterns

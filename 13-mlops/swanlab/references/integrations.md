# SwanLab Framework Integrations

This document focuses on framework patterns that align with the public SwanLab docs.

## PyTorch

### Basic Training Loop

```python
import torch
import torch.nn as nn
import torch.optim as optim
import swanlab

run = swanlab.init(
    project="pytorch-training",
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

### Minimal Callback Wrapper

```python
import swanlab

class SwanLabTracker:
    def __init__(self, project, experiment_name=None, config=None):
        self.run = swanlab.init(
            project=project,
            experiment_name=experiment_name,
            config=config,
        )

    def log_metrics(self, metrics, step=None):
        swanlab.log(metrics, step=step)

    def log_images(self, name, images, captions=None):
        if captions is None:
            payload = [swanlab.Image(image) for image in images]
        else:
            payload = [
                swanlab.Image(image, caption=caption)
                for image, caption in zip(images, captions)
            ]
        swanlab.log({name: payload})

    def log_note(self, name, text):
        swanlab.log({name: swanlab.Text(text)})

    def finish(self):
        self.run.finish()
```

This wrapper deliberately omits fake histogram and file helpers that are not present in current SwanLab APIs.

## Transformers

### `transformers>=4.50.0`: official one-line integration

Prefer `report_to="swanlab"` on recent Transformers releases. This is the primary path documented by SwanLab.

```python
from transformers import (
    AutoModelForSequenceClassification,
    AutoTokenizer,
    Trainer,
    TrainingArguments,
)

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
model = AutoModelForSequenceClassification.from_pretrained(
    "bert-base-uncased",
    num_labels=2,
)

training_args = TrainingArguments(
    output_dir="./results",
    num_train_epochs=3,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=16,
    evaluation_strategy="epoch",
    logging_steps=100,
    report_to="swanlab",
    run_name="bert-imdb",
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
)

trainer.train()
```

Set `SWANLAB_PROJ_NAME` and `SWANLAB_WORKSPACE` environment variables when you need custom routing without switching away from the official integration path.

### `transformers<4.50.0` or custom control: `SwanLabCallback`

Use `SwanLabCallback` as the fallback path for older Transformers versions, or when you want SwanLab-specific control without `report_to="swanlab"`.

```python
from transformers import (
    AutoModelForSequenceClassification,
    AutoTokenizer,
    Trainer,
    TrainingArguments,
)
from swanlab.integration.transformers import SwanLabCallback

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
model = AutoModelForSequenceClassification.from_pretrained(
    "bert-base-uncased",
    num_labels=2,
)

training_args = TrainingArguments(
    output_dir="./results",
    evaluation_strategy="epoch",
    logging_steps=100,
    report_to="none",
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
    callbacks=[
        SwanLabCallback(
            project="text-classification",
            experiment_name="bert-imdb",
            config={
                "model": "bert-base-uncased",
                "batch_size": 16,
                "epochs": 3,
            },
        )
    ],
)

trainer.train()
```

## PyTorch Lightning

`SwanLabLogger` can create the run for you. Prefer passing project metadata directly to the logger.

```python
import pytorch_lightning as pl
import torch
import torch.nn as nn
from swanlab.integration.pytorch_lightning import SwanLabLogger

class LitClassifier(pl.LightningModule):
    def __init__(self, learning_rate=1e-3):
        super().__init__()
        self.save_hyperparameters()
        self.model = nn.Sequential(
            nn.Flatten(),
            nn.Linear(28 * 28, 128),
            nn.ReLU(),
            nn.Linear(128, 10),
        )
        self.criterion = nn.CrossEntropyLoss()

    def forward(self, x):
        return self.model(x)

    def training_step(self, batch, batch_idx):
        x, y = batch
        logits = self(x)
        loss = self.criterion(logits, y)
        self.log("train/loss", loss, prog_bar=True)
        return loss

    def validation_step(self, batch, batch_idx):
        x, y = batch
        logits = self(x)
        loss = self.criterion(logits, y)
        acc = (torch.argmax(logits, dim=1) == y).float().mean()
        self.log("val/loss", loss, prog_bar=True)
        self.log("val/accuracy", acc, prog_bar=True)

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=self.hparams.learning_rate)

swanlab_logger = SwanLabLogger(
    project="lightning-demo",
    experiment_name="mnist-classifier",
    config={"learning_rate": 1e-3, "max_epochs": 10},
)

trainer = pl.Trainer(
    logger=swanlab_logger,
    max_epochs=10,
    accelerator="auto",
)

trainer.fit(LitClassifier(), train_loader, val_loader)
```

## Fastai

`SwanLabCallback` accepts the same run metadata you would normally pass to `swanlab.init(...)`.

```python
from fastai.vision.all import URLs, ImageDataLoaders, Resize, accuracy, get_image_files, resnet34, untar_data, vision_learner
from swanlab.integration.fastai import SwanLabCallback

path = untar_data(URLs.PETS)
dls = ImageDataLoaders.from_name_func(
    path,
    get_image_files(path / "images"),
    valid_pct=0.2,
    label_func=lambda x: x[0].isupper(),
    item_tfms=Resize(224),
    bs=64,
)

learn = vision_learner(dls, resnet34, metrics=accuracy)
learn.fit(
    5,
    cbs=[
        SwanLabCallback(
            project="fastai-demo",
            experiment_name="pets-classification",
            config={"arch": "resnet34", "epochs": 5, "batch_size": 64},
        )
    ],
)
```

### Fastai Text

```python
from fastai.text.all import AWD_LSTM, TextDataLoaders, accuracy, text_classifier_learner, untar_data, URLs
from swanlab.integration.fastai import SwanLabCallback

path = untar_data(URLs.IMDB)
dls = TextDataLoaders.from_folder(path, valid="test", bs=64)

learn = text_classifier_learner(
    dls,
    AWD_LSTM,
    drop_mult=0.5,
    metrics=accuracy,
)

learn.fit_one_cycle(
    3,
    cbs=[
        SwanLabCallback(
            project="fastai-text",
            experiment_name="imdb-sentiment",
            config={"arch": "AWD_LSTM", "epochs": 3, "batch_size": 64},
        )
    ],
)
```

## Best Practices

1. Initialize as early as possible so config and environment metadata are captured once.
2. Use stable metric names such as `train/loss` and `val/accuracy` across runs.
3. Save checkpoints locally with your framework and log the checkpoint path or score separately.
4. Prefer `run.finish()` when you manage the run yourself; let framework integrations finalize runs when they own the lifecycle.
5. Use `mode="local"` plus `swanlab watch -l ./swanlog` when you want an offline-first workflow.

# SwanLab Visualization Guide

This guide covers chart objects and validated media types in the public SwanLab docs.

## Chart Objects with `swanlab.echarts`

SwanLab accepts `pyecharts` chart objects through `swanlab.echarts`. Log the chart object directly instead of wrapping a raw option dictionary.

### Line Chart

```python
import swanlab

loss_chart = swanlab.echarts.Line()
loss_chart.add_xaxis(["epoch-1", "epoch-2", "epoch-3", "epoch-4"])
loss_chart.add_yaxis("train/loss", [0.95, 0.63, 0.41, 0.29])
loss_chart.set_global_opts(
    title_opts=swanlab.echarts.options.TitleOpts(title="Training Loss")
)

swanlab.log({"charts/loss": loss_chart})
```

### Multi-Series Line Chart

```python
comparison = swanlab.echarts.Line()
comparison.add_xaxis(["1", "2", "3", "4"])
comparison.add_yaxis("train/loss", [0.95, 0.63, 0.41, 0.29])
comparison.add_yaxis("val/loss", [1.02, 0.72, 0.55, 0.49])
comparison.set_global_opts(
    title_opts=swanlab.echarts.options.TitleOpts(title="Train vs Val Loss")
)

swanlab.log({"charts/comparison": comparison})
```

### Bar Chart

```python
bar = swanlab.echarts.Bar()
bar.add_xaxis(["cat", "dog", "bird", "fish"])
bar.add_yaxis("accuracy", [95, 92, 88, 91])
bar.set_global_opts(
    title_opts=swanlab.echarts.options.TitleOpts(title="Per-Class Accuracy")
)

swanlab.log({"charts/per_class_accuracy": bar})
```

### HeatMap

```python
heatmap = swanlab.echarts.HeatMap()
heatmap.add_xaxis(["Class A", "Class B", "Class C"])
heatmap.add_yaxis(
    "count",
    ["Class A", "Class B", "Class C"],
    [
        [0, 0, 50], [0, 1, 2], [0, 2, 1],
        [1, 0, 3], [1, 1, 45], [1, 2, 2],
        [2, 0, 1], [2, 1, 3], [2, 2, 48],
    ],
)
heatmap.set_global_opts(
    title_opts=swanlab.echarts.options.TitleOpts(title="Confusion Matrix"),
    visualmap_opts=swanlab.echarts.options.VisualMapOpts(min_=0, max_=50),
)

swanlab.log({"charts/confusion_matrix": heatmap})
```

## Image Logging

### Single Images

```python
import numpy as np
import swanlab
from PIL import Image

swanlab.log({"image/path": swanlab.Image("path/to/image.png")})

image_array = np.random.randint(0, 255, (224, 224, 3), dtype=np.uint8)
swanlab.log({"image/numpy": swanlab.Image(image_array, caption="Random image")})

pil_image = Image.open("photo.jpg")
swanlab.log({"image/pil": swanlab.Image(pil_image)})
```

### Image Batches

```python
samples = [img1, img2, img3]
captions = ["sample-1", "sample-2", "sample-3"]

swanlab.log(
    {
        "image/batch": [
            swanlab.Image(img, caption=caption)
            for img, caption in zip(samples, captions)
        ]
    }
)
```

`swanlab.Image` does not support inline box metadata in current SwanLab releases. For detection tasks, draw overlays yourself before logging the image.

## Audio Logging

```python
import numpy as np
import swanlab

swanlab.log({"audio/file": swanlab.Audio("recording.wav", sample_rate=16000)})

sample_rate = 16000
audio = np.sin(np.linspace(0, 8 * np.pi, sample_rate)).astype("float32")
swanlab.log({"audio/generated": swanlab.Audio(audio, sample_rate=sample_rate)})

swanlab.log(
    {
        "audio/captioned": swanlab.Audio(
            "generated.wav",
            sample_rate=22050,
            caption="Generated speech sample",
        )
    }
)
```

## GIF Video Logging

Current SwanLab releases only accept GIF paths for `swanlab.Video`.

```python
import swanlab

swanlab.log({"video/demo": swanlab.Video("demo.gif")})
swanlab.log(
    {
        "video/predictions": swanlab.Video(
            "predictions.gif",
            caption="Validation rollout",
        )
    }
)
```

## Text Logging

```python
import swanlab

swanlab.log({"text/generated": swanlab.Text("The quick brown fox jumps over the lazy dog.")})
swanlab.log(
    {
        "text/llm_output": swanlab.Text(
            "This is a generated response.",
            caption="Prompt: summarize the dataset",
        )
    }
)
```

## 3D Objects

### Point Clouds from Numpy

```python
import numpy as np
import swanlab

points = np.random.rand(256, 3).astype("float32")
swanlab.log({"object3d/points": swanlab.Object3D(points, caption="Random point cloud")})
```

This guide intentionally sticks to numpy point clouds for `Object3D`. File-based constructors may exist in some package versions, but they are not the default public API path used in this skill. `Object3D` also does not accept `.obj` or `.ply` paths directly.

## Molecules

Use the documented helper constructor instead of passing raw strings directly to `swanlab.Molecule(...)`.

```python
import swanlab

swanlab.log({"molecule/smiles": swanlab.Molecule.from_smiles("CCO", caption="Ethanol")})
```

Some package versions expose additional molecule file helpers, but this guide does not rely on them because the public API page does not make them the default path.

## Experiment Comparison

```python
import swanlab

baseline = swanlab.init(project="comparison-demo", experiment_name="baseline")
for step in range(5):
    swanlab.log({"val/loss": 1.0 / (step + 1)}, step=step)
baseline.finish()

improved = swanlab.init(project="comparison-demo", experiment_name="improved")
for step in range(5):
    swanlab.log({"val/loss": 0.8 / (step + 1)}, step=step)
improved.finish()
```

Then compare the runs in the SwanLab UI.

## Troubleshooting

### Chart does not render

Log a `swanlab.echarts.*` object directly. Do not pass raw dictionaries through an old wrapper API.

### Images look wrong

Convert arrays to HWC `uint8` before wrapping them in `swanlab.Image`.

```python
import numpy as np

image = np.transpose(image, (1, 2, 0))
image = np.clip(image * 255, 0, 255).astype(np.uint8)
```

### Media imports fail

Install the media dependencies used in this skill:

```bash
pip install "swanlab>=0.7.11" "pillow>=9.0.0" "soundfile>=0.12.0"
```

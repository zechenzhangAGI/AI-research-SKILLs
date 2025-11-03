# Claude AI Research Skills

A curated collection of open-source Claude Code skills for AI research, covering deep learning frameworks, reinforcement learning, model training, and deployment tools.

## 📚 Overview

This repository provides production-ready skills for Claude Code to help AI researchers and engineers accelerate their workflows. Each skill includes comprehensive documentation, code templates, and real-world examples.

## 🎯 Skill Categories

### Deep Learning Training
- **GRPO/RL Training** - Train language models using GRPO and reinforcement learning with TRL
- **Megatron-LM** - Large-scale model training with Megatron framework
- **DeepSpeed** - Distributed training optimization with DeepSpeed
- **HuggingFace Fine-tuning** - Fine-tune transformer models with best practices

### Model Inference & Serving
- **vLLM Inference** - High-performance LLM inference with PagedAttention
- **TensorRT-LLM** - Optimized LLM inference with NVIDIA TensorRT
- **TorchServe** - Production model serving with PyTorch

### Reinforcement Learning
- **TRL (Transformer Reinforcement Learning)** - PPO, DPO, RLOO for LLM training
- **Stable-Baselines3** - RL algorithms for continuous control
- **RLlib** - Scalable reinforcement learning with Ray

### Research Infrastructure
- **Modal ML Training** - Serverless GPU infrastructure for experiments
- **Weights & Biases Integration** - Experiment tracking and visualization
- **Ray Distributed Computing** - Distributed compute for hyperparameter tuning

## 📦 Skill Structure

Each skill follows this standardized structure:

```
skill-name/
├── SKILL.md              # Comprehensive documentation
├── README.md             # Quick start guide
├── templates/            # Production-ready code templates
│   ├── basic_example.py
│   └── advanced_example.py
└── examples/             # Real-world use cases
    ├── example_1.py
    └── example_2.py
```

## 🚀 Getting Started

### Installation

1. Clone this repository:
```bash
git clone https://github.com/YOUR_USERNAME/claude-ai-research-skills.git
```

2. Browse available skills in the repository

3. Use skills with Claude Code or import into your Orchestra Research platform

### Using a Skill

Each skill can be used in two ways:

**Option 1: Direct Reference**
Point Claude Code to the skill directory and it will automatically load the SKILL.md documentation.

**Option 2: ZIP Package**
Package the skill as a ZIP file for uploading to skill marketplaces:
```bash
cd skill-name
zip -r skill-name.zip . -x ".*" -x "__pycache__/*"
```

## 📖 Skill Format

Each `SKILL.md` follows this structure:

```markdown
---
name: skill-name
version: 1.0.0
tags: [tag1, tag2, tag3]
author: Your Name
---

# Skill Name

## Overview
Brief description of what this skill does

## Prerequisites
- Required dependencies
- Environment setup

## Quick Start
Minimal working example

## Core Concepts
Key ideas and terminology

## API Reference
Detailed API documentation

## Examples
Real-world use cases

## Best Practices
Production tips and gotchas

## Troubleshooting
Common issues and solutions

## References
External resources
```

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Adding a New Skill

1. Fork this repository
2. Create a new directory under the appropriate category
3. Follow the skill structure template
4. Ensure SKILL.md is comprehensive (500+ lines recommended)
5. Test all code examples
6. Submit a pull request

### Quality Standards

- **Documentation**: Comprehensive SKILL.md with examples
- **Code Quality**: Production-ready, well-commented templates
- **Testing**: All examples must run successfully
- **Dependencies**: Clear dependency specifications
- **Versioning**: Semantic versioning (MAJOR.MINOR.PATCH)

## 📋 Roadmap

- [ ] Megatron-LM distributed training skill
- [ ] TensorRT-LLM inference optimization skill
- [ ] DeepSpeed ZeRO training skill
- [ ] Ray Tune hyperparameter optimization skill
- [ ] Weights & Biases tracking integration skill
- [ ] OpenAI Gym environment setup skill
- [ ] Stable Diffusion fine-tuning skill
- [ ] LangChain research assistant skill

## 🏗️ Repository Structure

```
claude-ai-research-skills/
├── README.md
├── CONTRIBUTING.md
├── LICENSE
├── .gitignore
├── deep-learning/
│   ├── grpo-rl-training/
│   ├── megatron-training/
│   ├── deepspeed-training/
│   └── huggingface-finetune/
├── inference/
│   ├── vllm-inference/
│   ├── tensorrt-llm/
│   └── torchserve/
├── reinforcement-learning/
│   ├── trl-training/
│   ├── stable-baselines3/
│   └── rllib/
└── infrastructure/
    ├── modal-ml-training/
    ├── wandb-integration/
    └── ray-distributed/
```

## 📄 License

MIT License - See [LICENSE](LICENSE) for details

## 🙏 Acknowledgments

Built for the AI research community, powered by [Claude Code](https://claude.ai/claude-code) and [Orchestra Research](https://orchestra.ai).

## 📞 Contact

- Issues: [GitHub Issues](https://github.com/YOUR_USERNAME/claude-ai-research-skills/issues)
- Discussions: [GitHub Discussions](https://github.com/YOUR_USERNAME/claude-ai-research-skills/discussions)

---

**Note**: This is an open-source community project. Skills are provided as-is for educational and research purposes.

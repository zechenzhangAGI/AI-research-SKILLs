# Contributing to Claude AI Research Skills

Thank you for your interest in contributing! This guide will help you create high-quality skills for the AI research community.

## 🎯 Contribution Process

1. **Fork** the repository
2. **Create** a new branch for your skill (`git checkout -b skill/your-skill-name`)
3. **Develop** your skill following our guidelines
4. **Test** all code examples thoroughly
5. **Submit** a pull request with a clear description

## 📋 Skill Checklist

Before submitting, ensure your skill includes:

- [ ] Comprehensive `SKILL.md` (500+ lines recommended)
- [ ] Quick start `README.md`
- [ ] At least 2 working code templates in `templates/`
- [ ] At least 2 real-world examples in `examples/`
- [ ] YAML frontmatter with metadata
- [ ] Clear prerequisite documentation
- [ ] Installation instructions
- [ ] Troubleshooting section
- [ ] References to external resources

## 📝 Skill Structure

### Directory Layout

```
your-skill-name/
├── SKILL.md              # Main documentation (REQUIRED)
├── README.md             # Quick start guide (REQUIRED)
├── templates/            # Code templates (REQUIRED)
│   ├── basic_template.py
│   └── advanced_template.py
├── examples/             # Real examples (REQUIRED)
│   ├── example_1.py
│   ├── example_2.py
│   └── data/            # Example data if needed
└── tests/               # Optional test files
    └── test_templates.py
```

### SKILL.md Format

The main documentation file must include:

```markdown
---
name: your-skill-name
display_name: Your Skill Display Name
version: 1.0.0
tags: [tag1, tag2, tag3, tag4]
author: Your Name or Organization
license: MIT
---

# Your Skill Name

## Overview
2-3 paragraph description of what this skill does and why it's useful

## Prerequisites
- Python version
- Required packages
- Hardware requirements (GPU, etc.)
- API keys or accounts needed

## Installation
Step-by-step installation guide with code blocks

## Quick Start
Minimal working example (10-20 lines)

## Core Concepts
Explain key ideas users need to understand

## Detailed Usage

### Basic Example
Simple use case with explanation

### Advanced Example
More complex scenario

### Configuration Options
Document all configurable parameters

## API Reference
Document key functions, classes, and methods

## Examples

### Example 1: [Use Case Name]
Real-world scenario with code

### Example 2: [Another Use Case]
Another real-world scenario

## Best Practices
- Production tips
- Performance optimization
- Common pitfalls to avoid

## Troubleshooting

### Common Issue 1
Problem description and solution

### Common Issue 2
Problem description and solution

## Performance Tips
Optimization strategies

## References
- Official documentation links
- Research papers
- Tutorial videos
- Related resources
```

## ✅ Quality Standards

### Documentation

- **Comprehensive**: Cover all major features and use cases
- **Clear**: Use simple language, avoid jargon where possible
- **Practical**: Include real-world examples, not toy examples
- **Accurate**: Test all code snippets before submission
- **Up-to-date**: Use latest stable versions of dependencies

### Code Quality

- **Working**: All code must run without errors
- **Commented**: Explain non-obvious logic
- **Formatted**: Follow PEP 8 for Python (or language standards)
- **Dependencies**: Pin versions in requirements or comments
- **Error Handling**: Include proper error handling

### Examples

- **Realistic**: Based on actual research tasks
- **Complete**: Include all necessary imports and setup
- **Documented**: Comment key steps
- **Tested**: Verify examples work end-to-end

## 🏷️ Tagging Guidelines

Use descriptive tags to help users discover your skill:

**Framework Tags**: `pytorch`, `tensorflow`, `jax`, `huggingface`, `trl`, `megatron`

**Task Tags**: `training`, `inference`, `fine-tuning`, `evaluation`, `deployment`

**Domain Tags**: `nlp`, `computer-vision`, `reinforcement-learning`, `ml-ops`

**Infrastructure Tags**: `gpu`, `distributed`, `cloud`, `modal`, `ray`

Example:
```yaml
tags: [pytorch, reinforcement-learning, trl, grpo, training, gpu]
```

## 📂 Category Guidelines

Place your skill in the appropriate category:

### `deep-learning/`
Training and fine-tuning neural networks (Megatron, DeepSpeed, HuggingFace, etc.)

### `inference/`
Model serving and optimization (vLLM, TensorRT-LLM, TorchServe, etc.)

### `reinforcement-learning/`
RL algorithms and frameworks (TRL, Stable-Baselines3, RLlib, etc.)

### `infrastructure/`
Compute platforms and tools (Modal, Ray, Weights & Biases, etc.)

### `data-processing/`
Data preparation and augmentation (coming soon)

### `evaluation/`
Benchmarking and metrics (coming soon)

## 🔄 Versioning

Follow semantic versioning:

- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes

Example: `1.2.3`

## 🧪 Testing Your Skill

Before submission:

1. **Clean Environment Test**
   ```bash
   # Create fresh environment
   conda create -n test-skill python=3.10
   conda activate test-skill

   # Install from your instructions
   # Run all examples
   ```

2. **Documentation Review**
   - Read through SKILL.md as a first-time user
   - Check for broken links
   - Verify code blocks have syntax highlighting

3. **Code Verification**
   - Run all templates
   - Execute all examples
   - Check for warnings or deprecation notices

## 💡 Skill Ideas

We especially welcome skills for:

- **Distributed Training**: Megatron, DeepSpeed, FSDP
- **Model Compression**: Quantization, pruning, distillation
- **Advanced RL**: Multi-agent, offline RL, model-based RL
- **MLOps**: Experiment tracking, model monitoring, A/B testing
- **Scientific Computing**: Molecular dynamics, protein folding, climate modeling
- **Multimodal Models**: Vision-language models, audio processing

## 📞 Getting Help

- **Questions**: Open a [Discussion](https://github.com/YOUR_USERNAME/claude-ai-research-skills/discussions)
- **Bug Reports**: File an [Issue](https://github.com/YOUR_USERNAME/claude-ai-research-skills/issues)
- **Feedback**: Tag maintainers in your PR

## 📄 License

By contributing, you agree to license your contribution under the MIT License.

## 🙏 Code of Conduct

- Be respectful and inclusive
- Provide constructive feedback
- Help others learn
- Give credit where due

---

Thank you for helping build the best collection of AI research skills! 🚀

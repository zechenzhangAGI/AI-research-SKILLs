# Session Report Templates for AI Research Skills

Ready-to-use session report templates for common AI research workflows. Each template follows the [SkillEvolve session schema](https://github.com/Skill-Evolve/meta-skill/blob/main/meta-skill/references/session-schema.md).

---

## Required Fields

Every session report must include:

| Field | Format | Example |
|-------|--------|---------|
| `session_id` | `sess-YYYY-MM-DD-{random}` | `sess-2026-02-15-x7k9m` |
| `agent_id` | Your registered ID | `agent-abc123` |
| `timestamp` | ISO 8601 | `2026-02-15T14:30:00Z` |
| `skill.name` | Skill name from YAML frontmatter | `grpo-rl-training` |
| `task.description` | Sanitized description (no secrets) | `Fine-tune Qwen2.5-7B on domain QA` |
| `outcome.success` | Boolean | `true` |

---

## Template 1: Fine-Tuning Session

```json
{
  "session_id": "sess-YYYY-MM-DD-XXXXX",
  "agent_id": "YOUR_AGENT_ID",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "skill": {
    "name": "axolotl",
    "source_repo": "https://github.com/Orchestra-Research/AI-Research-SKILLs",
    "source_path": "03-fine-tuning/axolotl",
    "source_version": "1.0.0"
  },
  "task": {
    "description": "Fine-tune [MODEL] on [DATASET] for [TASK]",
    "category": "training",
    "tags": ["fine-tuning", "LoRA"]
  },
  "outcome": {
    "success": true,
    "notes": "Achieved [METRIC] after [N] steps/epochs"
  },
  "issues_encountered": [
    {
      "type": "error",
      "description": "Describe error or issue",
      "resolved": true,
      "resolution": "How you fixed it"
    }
  ],
  "learnings": [
    "Key takeaway 1",
    "Key takeaway 2"
  ],
  "environment": {
    "os": "Ubuntu 22.04",
    "python": "3.11",
    "cuda": "12.4",
    "gpus": "1x A100 80GB",
    "memory": "64GB"
  },
  "duration_minutes": 0
}
```

---

## Template 2: GRPO/RL Training Session

```json
{
  "session_id": "sess-YYYY-MM-DD-XXXXX",
  "agent_id": "YOUR_AGENT_ID",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "skill": {
    "name": "grpo-rl-training",
    "source_repo": "https://github.com/Orchestra-Research/AI-Research-SKILLs",
    "source_path": "06-post-training/grpo-rl-training",
    "source_version": "1.0.0"
  },
  "task": {
    "description": "Train [MODEL] with GRPO for [OBJECTIVE]",
    "category": "training",
    "tags": ["GRPO", "reward-design", "structured-output"]
  },
  "outcome": {
    "success": true,
    "partial": false,
    "notes": "Format compliance: [X]%, Correctness: [Y]% after [N] steps"
  },
  "issues_encountered": [
    {
      "type": "performance",
      "description": "Training speed issue or reward signal problem",
      "resolved": true,
      "resolution": "Configuration change or technique that fixed it"
    }
  ],
  "learnings": [
    "Reward function design insight",
    "Hyperparameter finding",
    "Training dynamics observation"
  ],
  "environment": {
    "os": "Ubuntu 22.04",
    "python": "3.11",
    "cuda": "12.4",
    "gpus": "1x A100 80GB",
    "memory": "64GB"
  },
  "duration_minutes": 0
}
```

---

## Template 3: Inference Deployment Session

```json
{
  "session_id": "sess-YYYY-MM-DD-XXXXX",
  "agent_id": "YOUR_AGENT_ID",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "skill": {
    "name": "serving-llms-vllm",
    "source_repo": "https://github.com/Orchestra-Research/AI-Research-SKILLs",
    "source_path": "12-inference-serving/vllm",
    "source_version": "1.0.0"
  },
  "task": {
    "description": "Deploy [MODEL] for inference with [FRAMEWORK]",
    "category": "deployment",
    "tags": ["inference", "serving", "production"]
  },
  "outcome": {
    "success": true,
    "notes": "Throughput: [X] tok/s, Latency p50: [Y]ms, p99: [Z]ms"
  },
  "issues_encountered": [
    {
      "type": "error",
      "description": "Deployment error or configuration issue",
      "resolved": true,
      "resolution": "Fix applied"
    }
  ],
  "learnings": [
    "Configuration optimization",
    "Throughput tuning insight"
  ],
  "environment": {
    "os": "Ubuntu 22.04",
    "python": "3.11",
    "cuda": "12.4",
    "gpus": "2x A100 80GB",
    "memory": "128GB"
  },
  "duration_minutes": 0
}
```

---

## Template 4: Distributed Training Session

```json
{
  "session_id": "sess-YYYY-MM-DD-XXXXX",
  "agent_id": "YOUR_AGENT_ID",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "skill": {
    "name": "deepspeed",
    "source_repo": "https://github.com/Orchestra-Research/AI-Research-SKILLs",
    "source_path": "08-distributed-training/deepspeed",
    "source_version": "1.0.0"
  },
  "task": {
    "description": "Scale [MODEL] training to [N] GPUs with [FRAMEWORK]",
    "category": "training",
    "tags": ["distributed", "multi-gpu", "scaling"]
  },
  "outcome": {
    "success": true,
    "notes": "Scaled to [N] GPUs, throughput: [X] samples/s, scaling efficiency: [Y]%"
  },
  "issues_encountered": [
    {
      "type": "error",
      "description": "Multi-GPU synchronization or communication issue",
      "resolved": true,
      "resolution": "NCCL config change, wrapping strategy, etc."
    }
  ],
  "learnings": [
    "Scaling insight",
    "Configuration finding"
  ],
  "environment": {
    "os": "Ubuntu 22.04",
    "python": "3.11",
    "cuda": "12.4",
    "gpus": "8x A100 80GB",
    "memory": "512GB"
  },
  "duration_minutes": 0
}
```

---

## Template 5: Evaluation Session

```json
{
  "session_id": "sess-YYYY-MM-DD-XXXXX",
  "agent_id": "YOUR_AGENT_ID",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "skill": {
    "name": "lm-evaluation-harness",
    "source_repo": "https://github.com/Orchestra-Research/AI-Research-SKILLs",
    "source_path": "11-evaluation/lm-evaluation-harness",
    "source_version": "1.0.0"
  },
  "task": {
    "description": "Evaluate [MODEL] on [BENCHMARKS]",
    "category": "evaluation",
    "tags": ["benchmarking", "evaluation"]
  },
  "outcome": {
    "success": true,
    "notes": "Results: [BENCHMARK_1]: [SCORE], [BENCHMARK_2]: [SCORE]"
  },
  "issues_encountered": [],
  "learnings": [
    "Evaluation configuration insight",
    "Benchmark selection rationale"
  ],
  "environment": {
    "os": "Ubuntu 22.04",
    "python": "3.11",
    "cuda": "12.4",
    "gpus": "1x A100 80GB",
    "memory": "64GB"
  },
  "duration_minutes": 0
}
```

---

## Template 6: RAG Pipeline Session

```json
{
  "session_id": "sess-YYYY-MM-DD-XXXXX",
  "agent_id": "YOUR_AGENT_ID",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "skill": {
    "name": "faiss",
    "source_repo": "https://github.com/Orchestra-Research/AI-Research-SKILLs",
    "source_path": "15-rag/faiss",
    "source_version": "1.0.0"
  },
  "task": {
    "description": "Build RAG pipeline with [VECTOR_DB] for [USE_CASE]",
    "category": "integration",
    "tags": ["RAG", "retrieval", "embeddings"]
  },
  "outcome": {
    "success": true,
    "notes": "Retrieval accuracy: [X]% at top-5, latency: [Y]ms per query"
  },
  "issues_encountered": [
    {
      "type": "performance",
      "description": "Retrieval quality or latency issue",
      "resolved": true,
      "resolution": "Index tuning, chunk size adjustment, etc."
    }
  ],
  "learnings": [
    "Chunking strategy insight",
    "Index configuration finding"
  ],
  "environment": {
    "os": "Ubuntu 22.04",
    "python": "3.11",
    "cuda": "12.4",
    "gpus": "1x A100 80GB",
    "memory": "64GB"
  },
  "duration_minutes": 0
}
```

---

## Template 7: Quantization/Optimization Session

```json
{
  "session_id": "sess-YYYY-MM-DD-XXXXX",
  "agent_id": "YOUR_AGENT_ID",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "skill": {
    "name": "gptq-quantization",
    "source_repo": "https://github.com/Orchestra-Research/AI-Research-SKILLs",
    "source_path": "10-optimization/gptq",
    "source_version": "1.0.0"
  },
  "task": {
    "description": "Quantize [MODEL] to [BITS]-bit with [METHOD]",
    "category": "optimization",
    "tags": ["quantization", "compression"]
  },
  "outcome": {
    "success": true,
    "notes": "Size: [X]GB → [Y]GB, Perplexity: [BASE] → [QUANTIZED], Speed: [Z]x"
  },
  "issues_encountered": [],
  "learnings": [
    "Calibration dataset insight",
    "Quality-speed tradeoff finding"
  ],
  "environment": {
    "os": "Ubuntu 22.04",
    "python": "3.11",
    "cuda": "12.4",
    "gpus": "1x A100 80GB",
    "memory": "64GB"
  },
  "duration_minutes": 0
}
```

---

## Task Categories Reference

Use these standardized categories in `task.category`:

| Category | When to Use |
|----------|-------------|
| `training` | Fine-tuning, pretraining, RL training |
| `inference` | Running model predictions, batch inference |
| `deployment` | Setting up serving infrastructure |
| `evaluation` | Benchmarking, testing model quality |
| `optimization` | Quantization, pruning, speed improvements |
| `configuration` | Setting up tools, environments, configs |
| `integration` | Connecting multiple tools/services |
| `debugging` | Fixing errors, investigating issues |
| `testing` | Validating behavior, running test suites |
| `documentation` | Writing docs, papers, reports |
| `other` | Anything that doesn't fit above |

---

## Issue Types Reference

Use these standardized types in `issues_encountered[].type`:

| Type | When to Use | Example |
|------|-------------|---------|
| `error` | Something crashed or failed | OOM, CUDA error, import failure |
| `warning` | Unexpected but not fatal | Deprecation warning, fallback behavior |
| `confusion` | Documentation unclear or misleading | Config option does opposite of what docs say |
| `performance` | Slower than expected | Training throughput below baseline |

# AI-Research-SKILLs → SkillEvolve Discovery Map

Detailed mapping of each skill category to the types of discoveries, gotchas, and techniques that are most valuable to share with the SkillEvolve community.

---

## 01 — Model Architecture

**Skills**: LitGPT, Mamba, RWKV, NanoGPT, TorchTitan

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "LitGPT checkpoint format changed in v2.x — old configs silently produce wrong results" |
| Technique | "Custom attention mask pattern for Mamba hybrid models to handle variable-length sequences" |
| Recipe | "Pretraining NanoGPT on custom corpus: tokenizer → data prep → config → training loop" |
| Benchmark | "Mamba-2 vs Transformer inference latency at sequence lengths 1K, 4K, 16K, 64K" |

**Session categories**: `training`, `configuration`, `debugging`

---

## 02 — Tokenization

**Skills**: HuggingFace Tokenizers, SentencePiece

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "SentencePiece BPE and Unigram produce different token boundaries for CJK text" |
| Technique | "Train domain-specific tokenizer, then merge with base model tokenizer to preserve special tokens" |
| Recipe | "Extend Llama tokenizer with 5K domain tokens without breaking chat template" |

**Session categories**: `configuration`, `optimization`, `integration`

---

## 03 — Fine-Tuning

**Skills**: Axolotl, LLaMA-Factory, Unsloth, PEFT

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "Axolotl `micro_batch_size` > 1 with gradient checkpointing causes silent NaN on some models" |
| Technique | "QLoRA on 24GB: micro_batch=1, grad_accum=8, bf16, gradient_checkpointing" |
| Recipe | "Full Unsloth fine-tuning workflow: data format → config → train → merge → quantize → deploy" |
| Benchmark | "Unsloth 4-bit vs HF PEFT 4-bit: training speed comparison on Qwen2.5 7B" |
| Taste | "When to use r=8 vs r=32 for LoRA rank — lower rank for style, higher for knowledge" |

**Session categories**: `training`, `configuration`, `optimization`

---

## 04 — Mechanistic Interpretability

**Skills**: TransformerLens, SAELens, NNsight, Pyvene

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "TransformerLens hook points differ between GPT-2 and Llama — check model.hook_dict" |
| Technique | "Sparse autoencoder feature steering: find feature → scale activation → measure downstream effect" |
| Recipe | "End-to-end interpretability workflow: identify circuit → ablate → validate → visualize" |
| Insight | "SAE features for 'code' vs 'math' activate in different MLP layers for Llama models" |

**Session categories**: `debugging`, `testing`, `documentation`

---

## 05 — Data Processing

**Skills**: Ray Data, NeMo Curator

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "Ray Data `.map_batches()` OOMs with large batch sizes — use `batch_size=1000` as default" |
| Technique | "NeMo Curator dedup pipeline: exact hash → fuzzy MinHash → semantic embedding dedup" |
| Recipe | "Process 1TB dataset on 8-node cluster: partition → filter → dedup → tokenize" |
| Benchmark | "Ray Data vs Spark for text preprocessing: throughput comparison at 100GB, 1TB, 10TB" |

**Session categories**: `optimization`, `configuration`, `deployment`

---

## 06 — Post-Training (RLHF/DPO/GRPO)

**Skills**: TRL, GRPO, OpenRLHF, SimPO

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "GRPO loss increases during training — this is EXPECTED (KL divergence from reference policy)" |
| Technique | "Incremental format rewards (partial credit per tag) converge 2x faster than binary rewards" |
| Recipe | "Multi-stage GRPO: Stage 1 format compliance → Stage 2 correctness → Stage 3 style" |
| Taste | "3-5 reward functions is the sweet spot — fewer gives weak signal, more creates conflicting gradients" |
| Benchmark | "DPO vs GRPO vs SimPO for structured output compliance on Qwen2.5 models" |

**Session categories**: `training`, `optimization`, `debugging`

---

## 07 — Safety & Alignment

**Skills**: Constitutional AI, LlamaGuard, NeMo Guardrails, Prompt Guard

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "LlamaGuard 3 false-positives on medical terminology — add domain allow-list" |
| Technique | "Layer NeMo Guardrails: input filter → LlamaGuard → output filter for defense in depth" |
| Recipe | "Production safety pipeline: prompt injection detection → content classification → output filtering" |
| Fix | "Prompt Guard regex bypass for unicode homoglyphs — normalize input before classification" |

**Session categories**: `configuration`, `testing`, `integration`

---

## 08 — Distributed Training

**Skills**: Megatron-Core, DeepSpeed, FSDP2, Accelerate, Lightning, Ray Train

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "DeepSpeed ZeRO-3 model.generate() hangs after training — gather params first" |
| Technique | "FSDP2 + activation checkpointing: wrap every transformer layer, not every 2nd" |
| Recipe | "Scale training from 1 GPU to 64 GPUs: Accelerate config → FSDP wrapping → gradient sync" |
| Benchmark | "DeepSpeed ZeRO-2 vs ZeRO-3 vs FSDP2: throughput at 7B, 13B, 70B model sizes" |
| Fix | "NCCL timeout on multi-node: set NCCL_SOCKET_IFNAME to correct network interface" |

**Session categories**: `training`, `configuration`, `debugging`, `optimization`

---

## 09 — Infrastructure

**Skills**: Modal, SkyPilot, Lambda Labs

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "Modal cold start adds 30-60s for GPU functions — use `keep_warm=1` for interactive use" |
| Technique | "SkyPilot spot instance strategy: set `use_spot: true` with `spot_recovery: FAILOVER`" |
| Recipe | "Multi-cloud training: SkyPilot provisions cheapest GPUs → Modal handles serving" |
| Benchmark | "A100 80GB cost comparison: Modal vs Lambda vs AWS spot (per-hour and per-training-run)" |

**Session categories**: `deployment`, `configuration`, `optimization`

---

## 10 — Optimization

**Skills**: Flash Attention, bitsandbytes, GPTQ, AWQ, HQQ, GGUF

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "Flash Attention 2 silently falls back to slow path when sequence length is not divisible by 8" |
| Technique | "Chain quantization: AWQ 4-bit → GGUF conversion for llama.cpp deployment" |
| Recipe | "Full quantization pipeline: calibration dataset → GPTQ quantize → evaluate perplexity → deploy" |
| Benchmark | "AWQ vs GPTQ vs bitsandbytes: quality/speed/memory tradeoff at 4-bit for Llama 3 70B" |
| Taste | "Use AWQ for serving (faster inference), GPTQ for research (better perplexity)" |

**Session categories**: `optimization`, `deployment`, `testing`

---

## 11 — Evaluation

**Skills**: lm-evaluation-harness, BigCode, NeMo Evaluator

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "lm-eval-harness default `num_fewshot` varies per task — always set explicitly for reproducibility" |
| Technique | "Custom eval task: define YAML config → implement metric → register → run" |
| Recipe | "Full model evaluation pipeline: standard benchmarks → domain-specific tasks → human eval" |
| Benchmark | "Evaluation cost comparison: full MMLU vs MMLU-Pro subset correlation (r=0.94 at 20% samples)" |

**Session categories**: `evaluation`, `testing`, `configuration`

---

## 12 — Inference & Serving

**Skills**: vLLM, TensorRT-LLM, llama.cpp, SGLang

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "vLLM tensor_parallel must match GPU count exactly — partial tensor parallel not supported" |
| Technique | "SGLang RadixAttention prefix caching: 3x throughput for shared-prefix workloads" |
| Recipe | "Production vLLM deployment: model loading → engine config → API server → load balancer" |
| Benchmark | "vLLM vs TensorRT-LLM vs SGLang: latency p50/p95/p99 for Llama 3 8B at 100 QPS" |
| Fix | "llama.cpp GGUF conversion fails for GQA models — pass `--gqa` flag with correct group count" |

**Session categories**: `deployment`, `inference`, `optimization`

---

## 13 — MLOps

**Skills**: Weights & Biases, MLflow, TensorBoard

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "W&B `log()` in training loop adds 5-10ms overhead — use `commit=False` and batch" |
| Technique | "MLflow model registry with auto-staging: dev → staging → production promotion pipeline" |
| Recipe | "Experiment tracking setup: W&B for training metrics → MLflow for model registry → TensorBoard for debugging" |

**Session categories**: `configuration`, `integration`, `deployment`

---

## 14 — Agents

**Skills**: LangChain, LlamaIndex, CrewAI, AutoGPT

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "LangChain LCEL chains silently swallow exceptions in `.batch()` — wrap with error handling" |
| Technique | "LlamaIndex sub-question query engine for multi-document reasoning across heterogeneous sources" |
| Recipe | "Production agent pipeline: tool definition → memory setup → guardrails → evaluation loop" |

**Session categories**: `integration`, `deployment`, `debugging`

---

## 15 — RAG

**Skills**: Chroma, FAISS, Sentence Transformers, Pinecone, Qdrant

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "FAISS IVF index requires `nprobe` tuning — default (1) misses 40% of relevant results" |
| Technique | "Hybrid retrieval: BM25 keyword search + dense embedding search with reciprocal rank fusion" |
| Recipe | "Production RAG pipeline: chunk → embed → index → retrieve → rerank → generate" |
| Benchmark | "Chroma vs FAISS vs Qdrant: latency at 1M, 10M, 100M vectors with filtered search" |
| Taste | "Chunk size 512 tokens for factoid QA, 1024 for summarization, 256 for code search" |

**Session categories**: `configuration`, `optimization`, `integration`

---

## 16 — Prompt Engineering

**Skills**: DSPy, Instructor, Guidance, Outlines

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "Outlines JSON schema generation fails with recursive schemas — flatten first" |
| Technique | "DSPy optimizer: compile few-shot examples from labeled data → auto-generate optimal prompts" |
| Recipe | "Structured output pipeline: define Pydantic model → Instructor extraction → validation → retry" |
| Taste | "Use Instructor for single-object extraction, Outlines for constrained generation, DSPy for prompt optimization" |

**Session categories**: `configuration`, `optimization`, `integration`

---

## 17 — Observability

**Skills**: LangSmith, Phoenix

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "LangSmith tracing adds 15-20ms per LLM call — disable in latency-critical production paths" |
| Technique | "Phoenix embedding drift detection: monitor retrieval quality degradation over time" |
| Recipe | "Full observability stack: LangSmith for tracing → Phoenix for embedding analysis → custom dashboards" |

**Session categories**: `configuration`, `integration`, `debugging`

---

## 18 — Multimodal

**Skills**: CLIP, Whisper, LLaVA, Stable Diffusion, SAM, BLIP-2, AudioCraft

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "Whisper large-v3 hallucinates on silence — add VAD preprocessing to skip empty segments" |
| Technique | "CLIP zero-shot classification: craft prompt templates per domain for 10-15% accuracy boost" |
| Recipe | "Multimodal pipeline: Whisper transcription → LLaVA visual grounding → CLIP similarity ranking" |
| Benchmark | "Whisper large-v3 vs medium: WER comparison across 10 languages and accent varieties" |

**Session categories**: `inference`, `integration`, `optimization`

---

## 19 — Emerging Techniques

**Skills**: MoE Training, Model Merging, Long Context, Speculative Decoding, Distillation, Pruning

| Discovery Type | Example |
|----------------|---------|
| Gotcha | "Model merging with SLERP fails when models have different tokenizers — align first" |
| Technique | "Speculative decoding: use quantized draft model (same architecture) for 2-3x inference speedup" |
| Recipe | "Knowledge distillation: teacher inference → soft label generation → student training → evaluation" |
| Benchmark | "Structured pruning at 50% sparsity: quality retention across model families (Llama, Qwen, Mistral)" |

**Session categories**: `training`, `optimization`, `testing`

---

## 20 — ML Paper Writing

**Skills**: ML Paper Writing

| Discovery Type | Example |
|----------------|---------|
| Technique | "LaTeX table formatting recipe for benchmark results with confidence intervals" |
| Recipe | "Paper writing workflow: outline → related work search → experiments → writing → revision" |
| Taste | "Ablation study table design: one variable per row, bold best result, include std dev" |

**Session categories**: `documentation`, `other`

# Skill Creation Guide

**Based on**: [Anthropic Official Best Practices](anthropic_official_docs/best_practices.md)
**Last Updated**: November 6, 2025

---

## Core Principles (from Anthropic)

### 1. Concise is Key

**The context window is a public good.** Your skill shares it with system prompts, conversation history, and other skills.

**Default assumption: Claude is already smart**

Only add context Claude doesn't already have. Challenge each piece of information:
- "Does Claude really need this explanation?"
- "Can I assume Claude knows this?"
- "Does this paragraph justify its token cost?"

**Good** (50 tokens):
```markdown
## Extract PDF text

Use pdfplumber for text extraction:

```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
```

**Bad** (150 tokens):
```markdown
## Extract PDF text

PDF (Portable Document Format) files are a common file format that contains
text, images, and other content. To extract text from a PDF, you'll need to
use a library. There are many libraries available for PDF processing, but we
recommend pdfplumber because it's easy to use and handles most cases well.
First, you'll need to install it using pip. Then you can use the code below...
```

### 2. Progressive Disclosure

**SKILL.md serves as an overview** that points Claude to detailed materials as needed.

- Keep SKILL.md body **under 500 lines** for optimal performance
- Aim for **200-300 lines** in practice
- Split content into separate reference files
- Keep references **ONE LEVEL DEEP** from SKILL.md (no nested references)

**Structure**:
```
skill-name/
├── SKILL.md              # Main overview (200-300 lines)
├── server-deployment.md  # Specific topic (loaded as needed)
├── offline-inference.md  # Another topic (loaded as needed)
├── optimization.md       # Advanced topic (loaded as needed)
└── scripts/
    ├── validate.py       # Utility script (executed, not loaded)
    └── helper.py         # Another script
```

### 3. Use Workflows with Checklists

For multi-step tasks, provide copy-paste checklists:

```markdown
## Deployment workflow

Copy this checklist and track progress:

```
Task Progress:
- [ ] Step 1: Configure server settings
- [ ] Step 2: Validate configuration
- [ ] Step 3: Deploy to production
- [ ] Step 4: Verify deployment
```

**Step 1: Configure server settings**

Edit `config.yaml` with production values.

**Step 2: Validate configuration**

Run validator and fix errors:
```bash
python validate.py config.yaml
# If errors: fix → validate again → continue
```

**Step 3: Deploy to production**

[Specific deployment command]

**Step 4: Verify deployment**

[Verification steps]
```

### 4. Feedback Loops for Quality

**Common pattern**: Run validator → fix errors → repeat

```markdown
## Document editing process

1. Make your edits to `document.xml`
2. **Validate immediately**: `python validate.py document.xml`
3. If validation fails:
   - Review the error message carefully
   - Fix the issues
   - Run validation again
4. **Only proceed when validation passes**
5. Export final document
```

---

## YAML Frontmatter Requirements

```yaml
---
name: "skill-name-here"
description: "Third-person description of what this does and when to use it. Include key terms and triggers. Maximum 1024 characters."
---
```

**name** field:
- Maximum 64 characters
- Lowercase letters, numbers, hyphens only
- No XML tags
- No reserved words: "anthropic", "claude"
- **Recommended**: Use gerund form (e.g., `serving-llms`, `processing-pdfs`, `analyzing-data`)

**description** field:
- Maximum 1024 characters
- Non-empty
- No XML tags
- **MUST be third person**: "Processes files..." not "I can help you..."
- Include **what** it does AND **when** to use it
- Include key terms for discovery

**Examples**:

✅ **Good**:
```yaml
description: "Serves LLMs with high throughput using vLLM's PagedAttention and continuous batching. Use when deploying production LLM APIs, optimizing inference latency, or serving models with limited GPU memory."
```

✅ **Good**:
```yaml
description: "Extracts text and tables from PDF files, fills forms, merges documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction."
```

❌ **Bad** (first person):
```yaml
description: "I can help you process PDF files and extract text"
```

❌ **Bad** (too vague):
```yaml
description: "Helps with documents"
```

---

## Skill Structure Best Practices

### File Organization

**Simple skill** (just SKILL.md):
```
skill-name/
└── SKILL.md
```

**Complex skill** (with references):
```
skill-name/
├── SKILL.md                  # Overview, points to references
├── server-deployment.md      # Topic-specific guide
├── offline-inference.md      # Another topic
├── optimization.md           # Advanced features
├── troubleshooting.md        # Common issues
└── scripts/
    ├── validate.py           # Utility script
    └── setup.sh              # Setup script
```

**Domain-specific organization** (for Skills with multiple domains):
```
bigquery-skill/
├── SKILL.md                  # Overview and navigation
└── reference/
    ├── finance.md            # Revenue, billing metrics
    ├── sales.md              # Opportunities, pipeline
    ├── product.md            # API usage, features
    └── marketing.md          # Campaigns, attribution
```

### Reference Files

**One level deep**: All reference files should link directly from SKILL.md

✅ **Good**:
```markdown
# SKILL.md

**Server deployment**: See [server-deployment.md](server-deployment.md)
**Offline inference**: See [offline-inference.md](offline-inference.md)
**API reference**: See [api-reference.md](api-reference.md)
```

❌ **Bad** (nested references):
```markdown
# SKILL.md
See [advanced.md](advanced.md)...

# advanced.md
See [details.md](details.md)...

# details.md
Here's the actual information...
```

**Table of contents**: For reference files >100 lines, include table of contents at top

```markdown
# API Reference

## Contents
- Authentication and setup
- Core methods (create, read, update, delete)
- Advanced features (batch operations, webhooks)
- Error handling patterns
- Code examples

## Authentication and setup
...
```

---

## Content Guidelines

### Assume Claude is Smart

Don't explain basics. Assume Claude knows:
- What PDFs are
- How libraries work
- What APIs are
- Common programming concepts
- Standard ML/AI terminology

Only explain:
- Domain-specific concepts unique to this tool
- Non-obvious gotchas
- Best practices from community experience

### Consistent Terminology

Choose one term and use it throughout:

✅ **Good**:
- Always "API endpoint"
- Always "field"
- Always "extract"

❌ **Bad**:
- Mix "API endpoint", "URL", "API route", "path"
- Mix "field", "box", "element", "control"
- Mix "extract", "pull", "get", "retrieve"

### Avoid Time-Sensitive Information

❌ **Bad**:
```markdown
If you're doing this before August 2025, use the old API.
After August 2025, use the new API.
```

✅ **Good**:
```markdown
## Current method

Use the v2 API endpoint: `api.example.com/v2/messages`

## Old patterns

<details>
<summary>Legacy v1 API (deprecated 2025-08)</summary>

The v1 API used: `api.example.com/v1/messages`

This endpoint is no longer supported.
</details>
```

### Provide Examples (Input/Output Pairs)

For skills where output quality depends on seeing examples:

```markdown
## Commit message format

Generate commit messages following these examples:

**Example 1:**
Input: Added user authentication with JWT tokens
Output:
```
feat(auth): implement JWT-based authentication

Add login endpoint and token validation middleware
```

**Example 2:**
Input: Fixed bug where dates displayed incorrectly in reports
Output:
```
fix(reports): correct date formatting in timezone conversion

Use UTC timestamps consistently across report generation
```

Follow this style: type(scope): brief description, then detailed explanation.
```

---

## Common Patterns

### Template Pattern

Provide templates for output format. Match strictness to needs.

**For strict requirements**:
````markdown
## Report structure

ALWAYS use this exact template structure:

```markdown
# [Analysis Title]

## Executive summary
[One-paragraph overview of key findings]

## Key findings
- Finding 1 with supporting data
- Finding 2 with supporting data
- Finding 3 with supporting data

## Recommendations
1. Specific actionable recommendation
2. Specific actionable recommendation
```
````

**For flexible guidance**:
````markdown
## Report structure

Here is a sensible default format, but use your best judgment:

```markdown
# [Analysis Title]

## Executive summary
[Overview]

## Key findings
[Adapt sections based on what you discover]

## Recommendations
[Tailor to the specific context]
```

Adjust sections as needed for the specific analysis type.
````

### Conditional Workflow Pattern

Guide Claude through decision points:

```markdown
## Document modification workflow

1. Determine the modification type:

   **Creating new content?** → Follow "Creation workflow" below
   **Editing existing content?** → Follow "Editing workflow" below

2. Creation workflow:
   - Use docx-js library
   - Build document from scratch
   - Export to .docx format

3. Editing workflow:
   - Unpack existing document
   - Modify XML directly
   - Validate after each change
   - Repack when complete
```

---

## Anti-Patterns to Avoid

### ❌ Windows-Style Paths

Always use forward slashes:

✅ **Good**: `scripts/helper.py`, `reference/guide.md`
❌ **Bad**: `scripts\helper.py`, `reference\guide.md`

### ❌ Too Many Options

Don't present multiple approaches unless necessary:

❌ **Bad**:
"You can use pypdf, or pdfplumber, or PyMuPDF, or pdf2image, or..."

✅ **Good**:
"Use pdfplumber for text extraction:
```python
import pdfplumber
```

For scanned PDFs requiring OCR, use pdf2image with pytesseract instead."

### ❌ Nested References

❌ **Bad**: SKILL.md → advanced.md → details.md → actual info
✅ **Good**: SKILL.md → [topic].md (all references one level deep)

### ❌ Over-Explaining Basics

❌ **Bad** (150 tokens):
"PDF files are a common format. They contain text and images. To process them, you need a library. Python has many PDF libraries. We recommend pdfplumber because..."

✅ **Good** (30 tokens):
"Use pdfplumber for PDF text extraction:
```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```"

---

## Quality Checklist

Before submitting a skill:

### Core Quality
- [ ] Description is specific and includes key terms
- [ ] Description includes both what it does and when to use it
- [ ] SKILL.md body is under 500 lines (aim for 200-300)
- [ ] Additional details in separate files (if needed)
- [ ] No time-sensitive information (or in "old patterns" section)
- [ ] Consistent terminology throughout
- [ ] Examples are concrete, not abstract
- [ ] File references are one level deep
- [ ] Progressive disclosure used appropriately
- [ ] Workflows have clear steps with checklists

### Code and Scripts
- [ ] Scripts solve problems rather than punt to Claude
- [ ] Error handling is explicit and helpful
- [ ] No "magic numbers" (all values justified)
- [ ] Required packages listed in instructions
- [ ] No Windows-style paths (all forward slashes)
- [ ] Validation/verification steps for critical operations
- [ ] Feedback loops included for quality-critical tasks

### Content Quality
- [ ] Assumes Claude is smart (no over-explaining basics)
- [ ] Third person description
- [ ] Gerund naming (e.g., "serving-llms" not "llm-server")
- [ ] Clear when to use vs alternatives
- [ ] Concrete examples with input/output pairs
- [ ] Troubleshooting section with common issues

---

## Recommended Process

### 1. Research Phase

- Read official documentation thoroughly
- Analyze real-world usage (blog posts, Stack Overflow, GitHub issues)
- Identify key concepts and common gotchas
- Find production code examples

### 2. Outline Phase

Create structure outline:
1. Quick start (20-30 lines)
2. Common workflows with checklists (80-120 lines)
3. When to use vs alternatives (20-30 lines)
4. Common issues (30-50 lines)
5. Advanced topics with links to reference files (10-20 lines)

**Target**: 200-300 lines for SKILL.md

### 3. Writing Phase

Use SKILL_TEMPLATE.md as starting point:
- Fill in YAML frontmatter (name, description)
- Write concise quick start
- Create 2-3 workflows with copy-paste checklists
- Add common issues section
- Link to reference files for advanced topics

### 4. Reference Files Phase

Create separate markdown files for:
- Detailed API documentation
- Advanced features
- Troubleshooting guides
- Configuration references
- Domain-specific content

Each file:
- Has clear purpose
- Links directly from SKILL.md
- Includes table of contents if >100 lines
- Focuses on one topic

### 5. Testing Phase

Test with Claude:
- Activate the skill
- Try common workflows
- Verify checklist format works
- Test progressive disclosure (does Claude load right files?)
- Check cross-references work

### 6. Iteration Phase

Based on testing:
- Simplify over-explained sections
- Add missing common issues
- Improve workflow clarity
- Reorganize reference files if needed

---

## Examples of Good Skills

**For structure reference**, see official Anthropic examples in `anthropic_official_docs/best_practices.md`:
- PDF Processing skill (lines 286-307)
- BigQuery skill (lines 316-344)
- Git Commit Helper (lines 229-233)

**From this project**:
- Reference GRPO-RL-Training skill for comprehensive workflows
- But make it MORE CONCISE following Anthropic guidelines

---

## Common Mistakes to Avoid

1. **Making SKILL.md too long** (>500 lines is RED FLAG)
2. **Over-explaining basics** (assume Claude knows ML/programming)
3. **No workflows with checklists** (makes complex tasks hard)
4. **Nested references** (keep one level deep)
5. **First-person descriptions** (use third person!)
6. **Vague skill names** (use gerund form with specific terms)
7. **No "when to use vs alternatives"** (critical for skill selection)
8. **Missing validation steps** (add feedback loops)
9. **Too many options** (provide default with escape hatch)
10. **Time-sensitive info** (use "old patterns" section instead)

---

## Resources

- **Anthropic Official Best Practices**: [anthropic_official_docs/best_practices.md](anthropic_official_docs/best_practices.md)
- **Skill Template**: [SKILL_TEMPLATE.md](SKILL_TEMPLATE.md)
- **Contributing Guide**: [CONTRIBUTING.md](CONTRIBUTING.md)

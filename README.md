# Personal-Prompts-by-anorak999

A curated collection of production-grade, multi-LLM system prompts optimized for code intelligence, security auditing, prompt engineering, GNOME extension development, and academic writing. Each prompt is token-optimized and reformatted to match how different AI models were trained to parse instructions.

---

## Prompt Index

| # | Prompt | File | Purpose |
|---|--------|------|---------|
| 1 | **VECNA** | [`VECNA/VECNA_Finalized_Multi_LLM.md`](VECNA/VECNA_Finalized_Multi_LLM.md) | Code Efficiency Auditor -- performance bottleneck analysis |
| 2 | **BLOB** | [`BLOB/BLOB_Finalized_Multi_LLM.md`](BLOB/BLOB_Finalized_Multi_LLM.md) | Production Code Security Auditor -- deep-dive security review |
| 3 | **ZETA** | [`ZETA/ZETA_Finalized_Multi_LLM.md`](ZETA/ZETA_Finalized_Multi_LLM.md) | AI Prompt Optimizer (Lyra v2) -- vague-to-precise prompt engineering |
| 4 | **GENIE** | [`GENIE/GENIE_Finalized_Multi_LLM.md`](GENIE/GENIE_Finalized_Multi_LLM.md) | Project Foundation Architect -- full project scaffolding + multi-agent orchestration |
| 5 | **GXT** | [`GXT/GXT_Finalized_Multi_LLM.md`](GXT/GXT_Finalized_Multi_LLM.md) | GNOME eXtension Toolkit -- recursive extension development checklist |
| 6 | **WRYT** | [`WRYT/WRYT_Finalized.md`](WRYT/WRYT_Finalized.md) | Academic Writing Assistant -- undergraduate-level assignment writer |
| -- | **X99 (GitCore)** | [`version-control.md`](version-control.md) | Git architecture enforcer persona -- Four-Tier Branching Strategy |

---

## What's Inside

Seven specialized prompts, each available in **5 native formats** (Claude, GPT, Gemini, Llama, Mistral):

---

### VECNA -- Code Efficiency Auditor

Analyze codebases for performance bottlenecks and deliver quantified optimizations.

**Finds:**

- Time complexity issues (O(n2) patterns, unoptimized search, redundant operations)
- Memory leaks, excess allocations, inefficient caching
- Blocking I/O, N+1 queries, sync bottlenecks
- Concurrency bugs (race conditions, deadlocks, poor synchronization)
- Resource exhaustion risks (unclosed connections, pooling failures)

**Delivers:**

- 5-7 prioritized findings with quantified impact ("500ms -> 50ms", "O(n2) -> O(n log n)")
- Up to 2 solution options per issue with pros/cons analysis
- Best-fit recommendation with efficiency-to-effort justification
- Annotated code changes with inline rationale and test/deployment instructions

[Open VECNA](VECNA/VECNA_Finalized_Multi_LLM.md)

---

### BLOB -- Production Code Security Auditor

Deep-dive code reviews to surface every issue degrading stability, security, performance, or correctness.

**Audits:**

- **Security**: injection (SQL/OS/LDAP/XSS/SSTI), authN/authZ bypass, insecure deserialization, unsafe eval/exec, hardcoded secrets, TOCTOU
- **Logic**: off-by-one errors, boundary cases, null-safety, inverted conditions, silent failures
- **Concurrency**: unsynchronized state, race conditions, non-atomic operations, deadlocks, async sequencing bugs
- **Performance**: N+1 queries, hot-path bottlenecks, memory bloat, missing indexes, blocking I/O
- **Integration**: weak error handling, brittle response assumptions, missing retries/circuit breakers, credential handling
- **Change impact** (when diff provided): correctness, regressions, missed edge cases, removed critical code

**Delivers:**

- Severity-ranked findings (Critical -> High -> Medium -> Low)
- Exact file locations with line numbers and function names
- Exploitation paths or failure modes for each issue
- Code-ready fix suggestions
- Confidence levels (High/Medium/Low) for each finding

[Open BLOB](BLOB/BLOB_Finalized_Multi_LLM.md)

---

### ZETA -- AI Prompt Optimizer (Lyra v2)

Transform vague requests into precise, high-performing prompts tuned for any LLM.

**Features:**

- **4D Methodology**: Deconstruct -> Diagnose -> Develop -> Deliver
- **Intelligent Mode Selection**: Auto-detects complexity (BASIC for simple requests, DETAIL for complex projects)
- **Technique Library**:
  - Foundation techniques (goal alignment, context layering, output specs, task decomposition)
  - Advanced techniques (chain-of-thought, few-shot examples, multi-perspective analysis)
- **Platform-Specific Optimization**: Tailored guidance for ChatGPT, Claude, Gemini, and others
- **Actionable Output**: Optimized prompt + change summary + techniques applied + pro tips

[Open ZETA](ZETA/ZETA_Finalized_Multi_LLM.md)

---

### GENIE -- Project Foundation Architect & Multi-Agent Orchestration

Instantly bootstrap any software project with professional structure, intelligent agents, ironclad version control, and comprehensive logging systems.

**Features:**

- **4-Step Workflow**: Initialize -> Architecture Selection -> Agent Setup -> Foundation Files
- **Two Orchestration Modes**: One-Man Show (single agent) or Parallel Multi-Agent (multiple specialized agents)
- **Three Mandatory Foundation Files**: PROJECT\_LOG.md, TEST\_LOG.md, VERSION\_CONTROL.md
- **Agent Definition Generation**: Creates `agents/[agent-name].md` with operational rules, boundaries, protocols
- **GitCore Integration**: Enforces X99 Four-Tier Branching Strategy (Feature -> Dev -> Staging -> Main)
- **Professional Directory Blueprint**: `.git/`, `agents/`, `docs/`, `src/`, `tests/`, plus all foundation files

**Delivers:**

- Complete project directory structure with all scaffolding files
- Agent definition files with persona parameters, capabilities, limitations, and decision frameworks
- PROJECT\_LOG.md with ISO 8601 timestamped entries (Author, Action Type, Description, Trigger, Impact)
- TEST\_LOG.md tracking manual, automated, edge-case, and regression test suites
- VERSION\_CONTROL.md with X99-enforced branching strategy, protection rules, and migration plan
- Execution summary with ready-to-run terminal commands

[Open GENIE](GENIE/GENIE_Finalized_Multi_LLM.md)

---

### GXT -- GNOME eXtension Toolkit

A recursive checklist for GNOME Shell extension development. Catches the small, repeating set of category mismatches that cause most extension bugs before they become 2-hour debugging sessions.

**Covers:**

- **CHECK 0**: Identity — target GNOME version, surface type, component lifespan, external integrations
- **CHECK 1**: Import & module hygiene — ESM vs legacy, metadata.json correctness, enable/disable signatures
- **CHECK 2**: St vs Gtk4 boundary — the #1 recursive bug generator; shell UI vs preferences window widget sets
- **CHECK 3**: Lifecycle symmetry — enable/disable contracts, signal tracking, timeout cleanup, subprocess teardown
- **CHECK 4**: Settings & schema — glib-compile-schemas, key name/type matching, bind flags
- **CHECK 5**: External processes — async subprocess calls, error handling, sane polling intervals
- **CHECK 6**: Debugging loop breakers — journalctl, nested sessions, Looking Glass, session restart methods
- **CHECK 7**: Pre-release / packaging — clean install testing, extensions.gnome.org requirements

**Delivers:**

- 8 named check categories you can reference by number ("oh, this is a Check 3 leak")
- Usage guide mapping symptoms to the right check (new project, intermittent bug, settings issue, subprocess, pre-release)
- Recursive design — re-run the relevant checks at any point, not just linear stages

[Open GXT](GXT/GXT_Finalized_Multi_LLM.md)

---

### WRYT -- Academic Writing Assistant

A master academic writer specialized exclusively in undergraduate-level assignments. Produces original, error-free academic content with strict adherence to formal tone, Harvard referencing, and plagiarism-free standards.

**Covers:**

- **Tone Protocols**: Complete objectivity, evidence-based phrasing, precision with data/metrics/sources, formal third-person voice
- **Style Protocols**: Clarity, concision, varied sentence structure, consistent spelling, defined terminology, neutral language
- **Inclusive Language**: Singular they/them, gender-neutral terms throughout
- **Structural Requirements**: Harvard in-text citations, complete reference lists, 100% paraphrased originality, professional grammar
- **Research Report Tone**: Formal, objective, evidence-based scholarly communication
- **Strict Prohibitions**: Banned words/phrases list, no metaphors/cliches/em dashes/empty structures

**Delivers:**

- Polished undergraduate-level academic content on any topic or assignment
- Harvard-referenced citations with complete reference list
- Plagiarism-free, original prose with professional academic tone
- Compliance with 30+ banned words/phrases and structural prohibitions

[Open WRYT](WRYT/WRYT_Finalized.md)

---

### X99 (GitCore) -- Git Architecture Enforcer Persona

A persona-based prompt (like VECNA/BLOB/ZETA) that acts as X99, an uncompromising Git enforcer. Imposes the GitCore Four-Tier Branching Strategy (`feature` -> `dev` -> `staging` -> `main`) as the permanent, non-negotiable standard, with ironclad rules, migration plan, branch protection, and ready-to-run git commands.

[Open X99 (GitCore)](version-control.md)

---

## Token Efficiency

All prompts are **optimized 32-62% smaller** than their originals with **zero loss of functionality**:

| Prompt | VECNA | BLOB | ZETA | GENIE |
|--------|-------|------|------|-------|
| **Optimized** | 850-1,250 tokens | 460-600 tokens | 400-450 tokens | 850-1,400 tokens |
| **Savings** | 37-52% | 37-52% | 32-38% | optimized for 5 LLM-native formats |

---

## Quick Start

### 1. Choose Your LLM

| LLM | Prompt Format | File Pattern |
|-----|---------------|--------------|
| **Claude** (Opus/Sonnet/Haiku) | XML | `*_Multi_LLM.md` -> Claude block |
| **ChatGPT/GPT-5.5/5.4** | Markdown + JSON | `*_Multi_LLM.md` -> GPT block |
| **Google Gemini 3.x** | Markdown + JSON | `*_Multi_LLM.md` -> Gemini block |
| **Meta Llama 4** | Control tokens | `*_Multi_LLM.md` -> Llama block |
| **Mistral/Mixtral** | [INST] wrapper | `*_Multi_LLM.md` -> Mistral block |

### 2. Copy the Right Block

Each file (`VECNA_Finalized_Multi_LLM.md`, `BLOB_Finalized_Multi_LLM.md`, `ZETA_Finalized_Multi_LLM.md`, `GENIE_Finalized_Multi_LLM.md`) contains all 5 variants. Copy the block matching your LLM.

### 3. Paste & Go

Paste the prompt into your chat or API call. Reference the placeholder (e.g., `{{codebase}}`, `{{raw_prompt}}`) and provide your input.

---

## Usage Examples

### VECNA: Review a Python Service for Performance Issues

    # Copy the Claude XML variant from VECNA/VECNA_Finalized_Multi_LLM.md
    # Paste it, then follow with:

    Here's my codebase:

    [paste your Python code or directory structure]

**Expected output:**

- 5-7 issues ranked by impact
- Each with location, category, quantified gain, and implementation effort
- Side-by-side code comparisons
- Test/benchmark instructions

---

### BLOB: Security Audit a Node.js API

    # Copy the GPT Markdown variant from BLOB/BLOB_Finalized_Multi_LLM.md
    # Paste it, then follow with:

    Review this API codebase (in current directory):
    /src
      /routes
      /middleware
      /models
      /services

**Expected output:**

- Sorted table: Critical findings first
- For each: category, file:line, description, exploitation path, fix snippet
- Confidence level for each finding

---

### ZETA: Optimize a Vague User Prompt

    # Copy the Claude XML variant from ZETA/ZETA_Finalized_Multi_LLM.md
    # Paste it, then follow with:

    DETAIL using Claude -- I need a script that takes CSV data and does something useful with it.

**Expected output:**

- Deconstruction of the vague intent
- Optimized, specific prompt
- Explanation of what changed
- Techniques applied (e.g., "task decomposition + context layering")
- Pro tips for using the optimized prompt

---

## Design Philosophy

Each prompt balances **depth** (comprehensive coverage of risk categories) with **conciseness** (native LLM syntax, minimal tokens).

### Key Principles

1. **Model-native syntax** -- Claude gets XML, GPT gets Markdown + JSON, Llama gets control tokens -- not one-size-fits-all
2. **Elimination of redundancy** -- nested bullets, repeated context, verbose scoping removed
3. **Actionable output** -- every finding includes location, severity, confidence, and fix guidance
4. **Production-ready** -- designed for real codebases, real security reviews, real optimization decisions

---

## When to Use Each Prompt

### VECNA -- Use When:

- You need **quantified performance improvements** (ms, memory, algorithmic complexity)
- You want **ranked recommendations** with pros/cons tradeoffs
- You're optimizing **hot paths** in existing code
- You need **before/after benchmarks** and test instructions

### BLOB -- Use When:

- You require a **comprehensive security review** (includes logic, concurrency, integration)
- You need **exact file:line locations** for every issue
- You want **severity ranking** and confidence levels
- You're reviewing **production-critical code** or **prior to deployment**

### ZETA -- Use When:

- Your prompt is **vague or incomplete**
- You want to **optimize for a specific LLM** (ChatGPT, Claude, Gemini, etc.)
- You need **structured step-by-step reasoning** for complex tasks
- You want **fewer iterations** with your AI assistant

### GENIE -- Use When:

- Starting a **new project** and need instant professional scaffolding
- You want **agent-based orchestration** with defined roles and boundaries
- Setting up **logging, testing, and version control** systems from day one
- Need a **complete directory blueprint** with foundation files pre-generated

### GXT -- Use When:

- Building or maintaining a **GNOME Shell extension** and want to catch common bugs early
- Debugging **intermittent crashes** or "works then breaks" lifecycle issues (Check 3)
- Getting **silent failures** from settings/schema mismatches (Check 4)
- Mixing up **St vs Gtk4** widgets (Check 2 — the #1 recursive bug generator)
- About to **publish to extensions.gnome.org** and need a packaging sanity pass (Check 7)

### WRYT -- Use When:

- Writing an **undergraduate essay**, report, or assignment and need formal academic tone
- Requiring **Harvard-referenced citations** with a complete reference list
- Need **plagiarism-free content** that paraphrases thoroughly and uses evidence-based phrasing
- Producing a **research report** that demands objective, third-person scholarly communication
- Want to **avoid banned words/phrases** and maintain strict academic prose quality

### X99 (GitCore) -- Use When:

- Setting up or migrating a repository to a professional branching model
- Enforcing branch protection rules and CI/CD gates
- Auditing branch hygiene and compliance
- You want an LLM to act as a strict Git/DevOps enforcer persona

---

## Advanced: Custom Adaptations

Each prompt is modular. You can:

- **Adjust focus areas** -- VECNA can prioritize memory over I/O by reordering the `<focus_areas>` section
- **Combine prompts** -- Use GENIE to scaffold, BLOB first for security, then VECNA for performance of the cleaned code
- **Layer ZETA** -- Optimize a vague requirement with ZETA first, then feed the result to VECNA or BLOB
- **GXT for GNOME** -- Run GXT checks alongside BLOB (security) when building extensions that touch D-Bus or external processes
- **WRYT for academic work** -- Use WRYT to produce undergraduate-level assignments with Harvard citations and formal tone
- **Switch LLM variants mid-stream** -- Start with Claude's XML, switch to GPT's Markdown if needed

---

## File Structure

    Personal-Prompts-by-anorak999/
    |-- VECNA/
    |   |-- VECNA_Finalized_Multi_LLM.md    # Efficiency auditor (5 variants)
    |-- BLOB/
    |   |-- BLOB_Finalized_Multi_LLM.md     # Security auditor (5 variants)
    |-- ZETA/
    |   |-- ZETA_Finalized_Multi_LLM.md     # Prompt optimizer (5 variants)
    |-- GENIE/
    |   |-- GENIE_Finalized_Multi_LLM.md    # Project foundation architect (5 variants)
    |-- GXT/
    |   |-- GXT_Finalized_Multi_LLM.md      # GNOME eXtension Toolkit (recursive checklist)
    |-- WRYT/
    |   |-- WRYT_Finalized.md               # Academic Writing Assistant (undergraduate)
    |-- version-control.md                    # GitCore branching strategy
    |-- README.md                             # This file
    |-- LICENSE                               # MIT

---

## Token Cost Comparison

**Typical workflow without optimization:**

- Original VECNA: ~2,000 tokens per run
- Original BLOB: ~950 tokens per run
- **Total per code review**: ~2,950 tokens

**With these optimized variants:**

- VECNA (optimized): ~900 tokens per run
- BLOB (optimized): ~500 tokens per run
- **Total per code review**: ~1,400 tokens
- **Savings**: **52% fewer tokens**, same results

---

## Pro Tips

1. **Combine for max insight**: Use GENIE to scaffold the project, BLOB for security, then VECNA on the cleaned code (performance)
2. **Context matters**: Include architecture diagrams, framework info, and deployment constraints when using VECNA or BLOB
3. **Auto-mode selection**: ZETA detects complexity; trust its BASIC vs DETAIL choice or override explicitly
4. **Diff support**: BLOB is especially powerful when you provide a diff (change\_impact category activates)
5. **Reuse optimized prompts**: Save the ZETA-optimized results and reuse them across your team
6. **GitCore first**: Apply the branching strategy before starting collaborative work -- retrofitting is harder than starting clean

---

## License

MIT License -- free to use, modify, and distribute. See [LICENSE](LICENSE) file.

---

## Feedback & Contributions

Have ideas for new focus areas, additional LLM variants, or improvements? Open an issue or PR!

---

## What's Next?

Future iterations may include:

- Variants for additional models (Claude 3 Opus, Grok, etc.)
- Domain-specific prompts (ML/AI audit, API design review, infrastructure)
- Integration templates (GitHub Actions, CI/CD pipelines)
- Automated prompt testing/evaluation framework
- GENIE extensions (cloud provider scaffolding, Docker/K8s templates)
- GXT multi-LLM variants (Claude XML, GPT Markdown, Gemini, Llama, Mistral)
- GXT coverage for GNOME 46+ API changes and new extension surfaces
- WRYT multi-LLM variants (Claude XML, GPT Markdown, Gemini, Llama, Mistral)
- WRYT discipline-specific variants (nursing, law, business, psychology, STEM)

---

**Made by anorak999** | Optimized for production code intelligence, security, and academic writing

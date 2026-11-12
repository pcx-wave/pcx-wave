# Paul Cavalier

[![Cavalier Studios](https://img.shields.io/badge/Cavalier_Studios-portfolio-111827?style=flat-square)](https://cavalier-studios.fr)
[![Plume Africa](https://img.shields.io/badge/Plume_Africa-plume.africa-c2410c?style=flat-square)](https://plume.africa)
[![CV Scope](https://img.shields.io/badge/CV_Scope-cv--scope.com-2563eb?style=flat-square)](https://cv-scope.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-paulcavalier-0a66c2?style=flat-square&logo=linkedin&logoColor=white)](https://linkedin.com/in/paulcavalier)

AI engineer and fullstack developer. I build tooling that makes LLM coding agents cheaper and more reliable, and ship web products end to end (Python, Flask, Node.js).

## AI engineering

I work on the layer around the model: the harness that runs and checks an agent, the context it is given, and the prompts and skills that steer it. The work is measured, including where it does not pay off.

**Harnessing.** Claude Code orchestrates, a cheaper model (Mistral Vibe, Gemini CLI) does the heavy work, and nothing is accepted until it is reviewed as a `git diff`. The harness checks before and after each run: a pre-launch gate aborts when an edit anchor cannot be matched byte for byte, a post-run gate catches invalid code, and failures are classified (non-zero exit, nothing written, search/replace miss, timeout) instead of being lumped together. After a CLI upgrade broke 12 of 31 delegations and the harness misread most of them, I audited it against a 24-run benchmark with hidden acceptance tests, and the verdict was correct on 23 of 24.

**Context engineering.** The orchestrator's context is the scarce resource, so I move work and knowledge out of it. Claude reads a summary of about 700 to 1,100 tokens per run while the delegate processes a median of 215k. The skill itself was cut from about 12,900 to 5,300 tokens once I measured what loading it costs, and prompt-cache behaviour (around 90% hit rate) is part of the cost model. For large codebases I designed a three-layer context skill that replaces reading source with reading the filesystem: a deterministic skeleton (file tree, symbols, imports, dependencies, zero LLM cost), one LLM digest per module (intent, invariants, conventions, design decisions), and pre-generated context slices per build phase (domain, engine, surface, hardening) that the orchestrator loads as it advances. Questions are answered from those slices first, and only novel ones fall back to a short LLM call. Across sessions, a knowledge graph built from past conversations (21,000+ triplets) is served over MCP, including an index of approaches that were tried and rejected, so an agent does not repeat them.

**Prompt and skill design.** Skills are prompts loaded on demand, so their description decides when they fire and their length decides what they cost. A router skill picks the most relevant installed skill for a request. Prompts are written as contracts with checkable output: the module digest prompt fixes the exact structure and a token budget and requires a phase tag, and the codebase-answer prompt requires a `file:line` pointer for every claim, caps the answer at 300 tokens and forces the exact reply "not found in corpus" when the context does not contain the answer, rather than letting the model guess. Delegation prompts are self-contained because the delegate sees none of the conversation. The documented finding is that delegation costs Claude about $0.03 per task and only pays when doing the work directly would mean reading more than roughly 7k tokens of code or iterating.

| Project | Description |
|---------|-------------|
| [vibe-skill](https://github.com/pcx-wave/vibe-skill) | Supervised delegation to Mistral Vibe, with 2,450 logged runs over 28 days (155M tokens) and per-run cost accounting |
| [gemini-skill](https://github.com/pcx-wave/gemini-skill) | The same delegation pattern for Gemini CLI |
| [skill-router](https://github.com/pcx-wave/skill-router) | Routes a request to the most relevant installed skill |
| [update-skills](https://github.com/pcx-wave/update-skills) | Keeps custom Claude Code skills in sync |
| [askclaude](https://github.com/pcx-wave/askclaude) | CLI to ask Claude to plan, review or reflect from the terminal |

## In production

- [Plume Africa](https://plume.africa): instant safari packaging and booking engine for Namibia and South Africa, with a B2C configurator and a B2B agent mode.
- [CV Scope](https://cv-scope.com): AI-powered job search and application tool. It analyses a CV, runs a dual search (direct continuity and adjacent fields) and ranks offers by fit.

## Stack

Python, Flask, Node.js, SQLite, D3.js, Shell, MCP. Self-hosted deployment on Raspberry Pi with systemd, PM2 and Cloudflare tunnels.

## Contact

[LinkedIn](https://linkedin.com/in/paulcavalier) · [Paul.cavalier@live.fr](mailto:Paul.cavalier@live.fr)

## Research background

PhD in applied physics (CEA / Université de Grenoble), 15 peer-reviewed publications on wavelet analysis and optoelectronics. Postdoc at UCT Cape Town, then Science & Technology Attaché at the French Embassy in South Africa.

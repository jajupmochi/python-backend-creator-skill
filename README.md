# python-backend-creator: Interactive, Human-in-the-Loop Python Backend Scaffolding

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/downloads/)
[![GitHub Release](https://img.shields.io/github/v/release/jajupmochi/python-backend-creator-skill?label=Release)](https://github.com/jajupmochi/python-backend-creator-skill/releases)
[![Skills Hub](https://img.shields.io/badge/Published%20to-Claude%20Skills%20Hub-00d4ff)](https://skills.sh/)
[![Status: Active](https://img.shields.io/badge/Status-Active-brightgreen)](https://github.com/jajupmochi/python-backend-creator-skill)

**python-backend-creator** guides you through creating industrial-grade Python backend repositories using a structured, decision-driven workflow that produces minimal, testable, well-documented code atomically rather than through fragile end-to-end generation.

> **⚠️ Important Notice**: This skill was **auto-generated** using the [skill-creator](https://github.com/github/copilot-extensions) framework. The generation prompt is available in [`parent_prompt.md`](parent_prompt.md) and was created on **February 6, 2026** through extensive collaboration between the human author and AI assistants (ChatGPT and Claude). Both the prompt and the generated skill itself have undergone **limited human verification**. **Use with caution** and validate outputs carefully in production environments.

## Problem

Traditional code generation tools produce complete projects in one operation, often making assumptions about architecture, dependencies, and structure without user input. This leads to bloated repositories with unnecessary complexity, poor documentation, and code that does not match the user's actual requirements. When generation fails partway through, users are left with partial, broken repositories that are difficult to salvage.

## Solution

python-backend-creator uses a human-in-the-loop methodology where every major decision is explicitly confirmed before any code is generated. The workflow breaks repository creation into small atomic tasks that are independently generated, validated, and approved. A built-in Design Review Critic evaluates architecture choices before implementation begins, catching issues early when they are easier to fix.

## Key Features

- **Stage-based workflow** with explicit user confirmation at each stage
- **Atomic file-by-file generation** with validation after each file
- **Built-in Design Review Critic** providing engineering maturity scoring (0–25 scale)
- **Multi-agent support** through standardized sub-prompts — works with GitHub Copilot, Claude Code, Cursor, and more
- **Comprehensive quality assurance** including syntax checking, contract verification, security scanning, and test execution
- **Flexible feature selection** for API layers, documentation sites, CI/CD, DevContainers, and more
- **Production-ready defaults** including GitHub Actions, containerization, and structured logging

## Supported Environments

python-backend-creator works with any code generation agent that can interpret structured prompts and generate Python code:

- **GitHub Copilot** in Visual Studio Code
- **Claude Code** command-line tool
- **Cursor IDE**
- **ChatGPT Code Interpreter**
- Other LLM-based coding assistants

Agent-specific integration guidance is included in the skill.

## Installation

### GitHub Copilot

1. Place the `python-backend-creator/` skill folder under your `.github/skills/` directory
2. The skill will be available in Copilot Chat automatically

### Claude Code

```bash
# Add the skill
claude skill add /path/to/python-backend-creator

# Or use directly
claude --skill /path/to/python-backend-creator/SKILL.md
```

### General Use

Load `SKILL.md` into your agent's context and begin by describing your project.

## Workflow Overview

The workflow consists of five mandatory stages:

| Stage | Name | Purpose |
|-------|------|---------|
| **0** | Design Intake | 11-question questionnaire capturing requirements, goals, constraints |
| **1** | Core Architecture | Decisions on dependency management, configuration, error handling, logging, testing |
| **Critic 1** | Design Review | Automated scoring across 5 dimensions (0–25 scale) |
| **2** | Repository Structure | Directory layout definition and approval |
| **3** | Optional Features | API layers, UIs, docs, examples, translated READMEs |
| **Critic 2** | Design Review | Structure and feature consistency check |
| **4** | Atomic Generation | File-by-file generation with sub-prompts, validation, and approval |

Each stage requires explicit user confirmation. No stage can be skipped.

## Quick Start

To begin, invoke the skill with your project description:

```
I want to create a Python backend for a data validation library.
It should validate structured data against schemas and return error reports.
```

The skill will guide you through:

1. **Questionnaire** — answer 11 questions about your project
2. **Architecture** — choose dependency manager, error handling, testing strategy, etc.
3. **Design review** — automated critique scores your design
4. **Structure** — define the directory layout
5. **Features** — select optional components
6. **Generation** — receive sub-prompts, generate files one by one, validate each

See the full [example invocation](references/example-invocation.md) for a complete walkthrough.

## Table of Contents

- [Problem & Solution](#problem-and-solution)
- [Key Features](#key-features)
- [Supported Environments](#supported-environments)
- [Installation](#installation)
- [Workflow Overview](#workflow-overview)
- [Architecture Choices](#architecture-choices-supported)
- [Design Philosophy](#design-philosophy)
- [Skill Contents](#skill-contents)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

| Category | Options |
|----------|---------|
| **Dependency Management** | uv, Poetry, PDM, pip+requirements.txt, pip+pyproject.toml |
| **Configuration** | Environment variables, YAML/TOML files, Pydantic Settings, Combination |
| **Error Handling** | Exception-driven, Result objects, Mixed |
| **Logging** | Standard library, structlog, Minimal |
| **Testing** | None, Unit only, Unit+integration, Comprehensive |
| **API Frameworks** | FastAPI, Flask, GraphQL (Strawberry/Graphene) |
| **Documentation** | Sphinx, MkDocs, Markdown-only |

## Design Philosophy

python-backend-creator is built on the belief that good architecture emerges from explicit, informed decisions rather than automated defaults. By forcing users to think through design choices early and validating those choices before implementation, the skill helps create backends that truly match requirements and are easier to maintain long-term.

### Core Principles

1. **Design before code** — all decisions finalized before any file is generated
2. **Human-in-the-loop** — no silent assumptions, every choice requires confirmation
3. **Atomic generation** — one file at a time, validated, approved, then next
4. **Built-in quality** — syntax, contract, security, and test checks on every file
5. **Agent-agnostic** — works with any coding assistant through standardized prompts

## Skill Contents

```
python-backend-creator/
├── SKILL.md                              # Core workflow (loaded by agents)
└── references/
    ├── stage0-intake.md                  # Design questionnaire
    ├── stage1-architecture.md            # Architecture decision options
    ├── stage2-structure.md               # Repository structure definition
    ├── stage3-features.md                # Optional feature selection
    ├── stage4-generation.md              # Atomic generation workflow
    ├── design-critic.md                  # Scoring framework (0–25)
    ├── sub-prompt-template.md            # Code generation prompt template
    ├── example-sub-prompts.md            # 5 complete example sub-prompts
    ├── validation.md                     # QA and validation checks
    ├── security.md                       # Security requirements
    ├── readme-spec.md                    # README generation standards
    ├── ci-devcontainer.md                # CI/CD and DevContainer config
    ├── dependency-configs.md             # Per-manager configuration details
    ├── final-report.md                   # Report structure
    ├── sample-final-report.md            # Complete report example
    ├── integration-guides.md             # Copilot, Claude Code, Cursor guides
    ├── troubleshooting.md                # Common issues and solutions
    └── example-invocation.md             # Full workflow walkthrough
```

## Contributing

Contributions are welcome! To improve the skill:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Edit the SKILL.md or reference files
4. Test with your preferred agent (Copilot, Claude Code, Cursor, etc.)
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request with:
   - Description of changes and why they matter
   - Example demonstrating the improvement
   - Results from testing with multiple agents

### Areas for Contribution

- **New agent integrations** — tested workflows for additional AI coding assistants
- **Template improvements** — better scaffolding templates or examples
- **Validation enhancements** — new automated checks or QA improvements
- **Documentation** — clearer guides, translations, or examples
- **Bug reports** — issues with validation, generation, or workflow

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Built with the [skill-creator](https://github.com/github/copilot-extensions) framework
- Inspired by best practices from:
  - [Cookiecutter](https://github.com/cookiecutter/cookiecutter) for project scaffolding
  - [Pydantic](https://github.com/pydantic/pydantic) for validation patterns
  - [pytest](https://github.com/pytest-dev/pytest) for testing standards
- Special thanks to the Claude, GitHub Copilot, and Cursor communities

## Support

- **Issues & Bug Reports**: [GitHub Issues](https://github.com/jajupmochi/python-backend-creator-skill/issues)
- **Discussions & Ideas**: [GitHub Discussions](https://github.com/jajupmochi/python-backend-creator-skill/discussions)
- **Documentation**: See [references/](references/) folder for detailed guides

## Roadmap

### Planned Features

- [ ] Integration with more AI coding assistants
- [ ] Expanded template library (FastAPI specifics, async patterns, etc.)
- [ ] Interactive validation dashboard
- [ ] Multi-language code generation support
- [ ] Real-time collaboration mode
- [ ] Repository sync to GitHub/GitLab

### Known Limitations

- Validation checks require Python 3.11+
- Large projects (100+ files) may require multiple generation sessions
- Some agents may truncate very long sub-prompts

See [GitHub Issues](https://github.com/jajupmochi/python-backend-creator-skill/issues) for detailed tracking.

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=jajupmochi/python-backend-creator-skill&type=Date)](https://star-history.com/#jajupmochi/python-backend-creator-skill&Date)

---

**Made with ❤️ for Python developers worldwide**

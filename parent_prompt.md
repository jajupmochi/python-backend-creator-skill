# Complete Specification for python-backend-creator Skill

## Executive Summary

You are being asked to create **python-backend-creator**, a comprehensive skill that guides code generation agents through an interactive, human-in-the-loop workflow to produce industrial-grade Python backend repositories. This skill emphasizes design validation before code generation, atomic file-by-file creation with explicit user approval at each step, and adherence to production-quality standards including comprehensive testing, continuous integration, documentation, and developer environment setup.

The skill must be agent-agnostic, supporting GitHub Copilot, Claude Code, Cursor, and similar code generation tools through a clearly defined prompt-based interface. All skill documentation, generated code, comments, and user-facing prompts must be in English, with an optional feature allowing users to request additional README files in other languages.

## Skill Identity and Core Purpose

**Skill Name:** python-backend-creator

**Primary Function:** python-backend-creator orchestrates the creation of minimal, testable, industrial-quality Python backend repositories through a structured, multi-stage workflow that requires explicit human confirmation at every major decision point. The skill does not generate complete backends in a single operation. Instead, it breaks the work into small atomic tasks, validates each task independently, and advances only after receiving user approval.

**Design Philosophy:** The skill operates on the principle that robust backend architecture emerges from careful design decisions made explicit and validated early, rather than from automated generation of large codebases. Every assumption is surfaced, every ambiguity is resolved through user interaction, and every generated artifact is verified before proceeding.

## Core Principles (Non-Negotiable Requirements)

### Stage-Based Workflow Enforcement

The interaction must follow a rigid stage-based structure where each stage has a clearly defined objective and output. The skill must present these stages explicitly to the user, making it clear which stage is currently active and what decisions are required. No stage may be skipped, and progression to the next stage requires explicit user confirmation. The stages are not suggestions but mandatory checkpoints that ensure design quality and user awareness.

### Human-in-the-Loop Decision Making

The user retains full decision authority throughout the process. The skill serves as a guide and advisor, proposing options and highlighting risks, but never making silent assumptions or proceeding with default choices without explicit approval. When design information is missing, ambiguous, or underspecified, the skill must halt and request clarification. The skill may suggest reasonable defaults but must clearly label them as suggestions requiring confirmation.

### Atomic Generation Model

Code generation occurs in the smallest practical units, typically individual files or tightly coupled file groups that must be created together to maintain coherence. Each atomic task includes a precisely formatted sub-prompt suitable for sending to a code generation agent, a specification of the expected outputs, validation criteria, and a user confirmation checkpoint. The skill never generates multiple unrelated files in a single operation without separate approval for each.

### Design Precedes Implementation

All architectural decisions, repository structure, dependency management, and feature scope must be finalized and explicitly confirmed before any code generation begins. The skill conducts a comprehensive design review using a dedicated critic sub-agent that evaluates design maturity and surfaces potential issues before implementation starts.

### Built-in Quality Assurance

Every generated artifact undergoes automated validation including syntax checking, contract verification ensuring required symbols are present, and execution of associated unit tests where feasible. The skill records all validation results and presents them to the user as part of the approval process for each atomic task.

## Agent Integration Architecture

### Multi-Agent Compatibility Strategy

The skill must function correctly with GitHub Copilot, Claude Code, Cursor, and other code generation agents through a standardized prompt-based interface. The skill cannot assume access to specific agent APIs or features beyond the ability to provide text prompts and receive text responses. All interactions must be designed to work through natural language prompts that any competent code generation agent can interpret.

### Sub-Prompt Format Specification

When python-backend-creator needs to request code generation from an agent, it must format the request using a precise template that provides complete context, constraints, and success criteria. The template structure is:

```
TASK: [Atomic task identifier, e.g., "Generate models/base.py"]

FILE PATH: [Exact relative path within repository, e.g., "src/mybackend/models/base.py"]

PURPOSE: [Single-sentence description of what this file accomplishes]

LANGUAGE: Python [version, e.g., "3.10+"]

CONSTRAINTS:
- [List of hard requirements, e.g., "Must use type hints for all function signatures"]
- [Dependency restrictions, e.g., "No imports outside standard library except numpy"]
- [Length guidelines, e.g., "Target length under 200 lines"]
- [Style requirements, e.g., "Format with black, use Google-style docstrings"]

REQUIRED PUBLIC INTERFACE:
- [List of classes, functions, constants that must be exposed]
- [Include type signatures for all public APIs]
- [Specify expected behavior for key methods]

IMPLEMENTATION GUIDANCE:
- [Specific algorithmic or design patterns to follow]
- [Edge cases to handle]
- [Performance considerations if relevant]

TESTING SPECIFICATION:
- [Path to associated test file]
- [Key test cases that must pass]
- [Expected behavior to verify]

DEPENDENCIES:
- [Allowed imports from standard library]
- [Allowed third-party dependencies]
- [Modules from the same package that can be imported]

VALIDATION CRITERIA:
- [How success will be measured]
- [Automated checks that will be performed]

OUTPUT FORMAT:
- Provide complete file content
- Include module docstring
- Include inline comments only where behavior is non-obvious
- Follow black formatting conventions
```

This template provides sufficient context for any capable code generation agent to produce appropriate code without requiring agent-specific features or APIs.

### GitHub Copilot Integration Pattern

When operating with GitHub Copilot, the skill should present sub-prompts in a format that can be used directly in Copilot Chat or as inline comments that trigger Copilot suggestions. The skill should instruct users to:

1. Open the target file path in their editor
2. Paste the sub-prompt as a comment block at the top of the file
3. Invoke GitHub Copilot to generate implementation
4. Review the generated code against the validation criteria
5. Confirm acceptance or request modifications

For Copilot Chat, the skill should format sub-prompts as direct questions that can be pasted into the chat interface, such as "Generate a Python module at src/mybackend/models/base.py that implements an ExecutableModel abstract base class with the following requirements..."

### Claude Code and Cursor Integration Pattern

For agents that operate more autonomously, the skill should format sub-prompts as complete task specifications that the agent can execute independently. The sub-prompt should include all necessary context in a self-contained block that the agent can interpret without additional interaction.

### Confirmation and Feedback Collection

After each atomic code generation task, the skill must provide a structured format for collecting user feedback:

```
REVIEW CHECKPOINT: [Task identifier]

Generated artifact: [File path]

Validation results:
- Syntax check: [PASS/FAIL with details]
- Contract verification: [PASS/FAIL with details]
- Unit tests: [PASS/FAIL/SKIPPED with details]

Please review the generated code and select one of the following options:

A. ACCEPT - Proceed to next task
B. REQUEST_MODIFICATION - Specify changes needed
C. REGENERATE - Retry generation with same specification
D. DEFER - Skip this task for now, proceed to next
E. ABORT - Stop the workflow

Your choice:
```

This format works in CLI environments, chat interfaces, and programmatic APIs.

## Stage-Based Workflow Specification

### Stage 0: Design Intake Questionnaire

The skill must begin every interaction by collecting comprehensive information about the project through a structured questionnaire. This stage cannot be skipped or abbreviated. The questionnaire must be presented as a single structured block containing all questions, allowing the user to answer them in one interaction rather than requiring multiple back-and-forth exchanges.

**Required Questions (Present All Together):**

**Project Identity and Purpose**

1. What is the name of your Python package? This will be the importable package name and should follow Python naming conventions (lowercase, underscores permitted).

2. What is the repository name? This may differ from the package name and will be used for the repository directory and in documentation.

3. Provide a single-sentence description of what this backend accomplishes. Focus on the core value proposition.

4. What is the primary purpose of this backend? Select one: (A) REST or GraphQL API service exposed to external clients, (B) Internal service for use by other systems within your organization, (C) Reusable library or framework for other developers, (D) Research prototype or experimental codebase, (E) Command-line tool or batch processing system.

5. Who are the intended users? Select all that apply: (A) External users or customers, (B) Internal team members, (C) Other developers who will import your package, (D) Automated systems, (E) Personal use only.

**Lifecycle and Maturity Expectations**

6. What maturity level are you targeting? (A) Prototype - quick validation of concepts, minimal tests, informal documentation, (B) Production-ready - comprehensive tests, CI/CD, documentation, error handling, (C) Long-term maintained - all production features plus deprecation policies, semantic versioning, comprehensive examples.

7. What is the expected lifespan of this project? (A) Short-term - weeks to months, (B) Medium-term - months to one year, (C) Long-term - multiple years with ongoing maintenance.

**Technical Environment and Constraints**

8. What Python version will you target? Options: Python 3.10, Python 3.11, Python 3.12, or specify a range.

9. Describe your deployment context. Select one: (A) Local development only, (B) Containerized deployment via Docker, (C) Cloud platform such as AWS, GCP, or Azure, (D) Multiple environments, (E) Not yet determined.

10. What is your concurrency model preference? (A) Synchronous only - simpler, easier to debug, (B) Asynchronous using asyncio - higher concurrency for I/O-bound work, (C) Mixed - some components sync, some async, (D) No preference - recommend based on use case.

**Critical Non-Goals**

11. List explicitly what this backend is NOT intended to do. This is critically important for scoping. Examples: not a web UI, not a machine learning training pipeline, not a real-time streaming system, not a database management system. Be specific about functionality you want to exclude.

After the user provides answers, the skill must summarize all responses in a structured format and ask the user to confirm that the summary is accurate and complete. If the user identifies any errors or ambiguities, the skill must repeat this confirmation step with corrections until the user provides explicit approval.

### Stage 1: Core Architecture Decisions

Following confirmation of the design intake, the skill must request explicit decisions on fundamental architectural patterns that will shape the entire codebase. These decisions cannot be deferred and must be resolved before proceeding.

**Mandatory Architecture Decision Points:**

**Dependency Management Strategy**

The skill must ask: "How will you manage Python dependencies and virtual environments?" Provide these options with explanations:

Option A: uv - Modern, extremely fast Python package installer and resolver written in Rust. Provides dependency management, virtual environment handling, and project scaffolding with significantly better performance than traditional tools. Recommended for new projects prioritizing speed and modern Python packaging standards.

Option B: Poetry - Modern dependency management with lock files, virtual environment handling, and publishing tools built in. Recommended for libraries and applications with complex dependency trees.

Option C: PDM - PEP 582 compliant dependency manager with faster resolution and better standards compliance. Good for teams wanting to follow the latest Python packaging standards.

Option D: pip with requirements.txt and venv - Traditional approach using standard library tools. Simpler but requires manual lock file management.

Option E: pip with pyproject.toml only - Minimal approach for simple projects with few dependencies.

The skill must explain that this choice affects how the project is installed, how dependencies are specified, what files will be generated, and the developer experience for dependency operations.

**Configuration Management Approach**

The skill must ask: "How will you handle runtime configuration such as database URLs, API keys, feature flags, and environment-specific settings?" Provide these options:

Option A: Environment variables only - Configuration loaded from environment using os.environ or similar. Simple and cloud-friendly but can become unwieldy with many settings.

Option B: Configuration files in YAML or TOML - Structured configuration in version-controlled files, possibly with environment-specific overrides. Good for complex applications with many settings.

Option C: Pydantic Settings - Type-safe configuration management using Pydantic models that can load from environment variables, files, or both. Recommended for production applications requiring validation.

Option D: Combination approach - Configuration files for structure, environment variables for secrets and environment-specific overrides.

**Error Handling Philosophy**

The skill must ask: "What error handling pattern will you use throughout the codebase?" Provide these options:

Option A: Exception-driven - Use Python's native exception system with custom exception hierarchies. Traditional Python approach, familiar to most developers.

Option B: Result objects - Functions return explicit Result types that wrap success values or error information. More explicit control flow, popular in functional programming styles.

Option C: Mixed - Exceptions for truly exceptional conditions, result objects for expected error cases like validation failures.

**Logging Strategy**

The skill must ask: "How will you implement logging throughout the backend?" Provide these options:

Option A: Standard library logging - Use Python's built-in logging module with formatters and handlers. Simple, no dependencies, familiar to most developers.

Option B: Structured logging - Use structured logging libraries like structlog to emit machine-parseable log records with rich context. Better for centralized log aggregation and analysis.

Option C: Minimal logging - Logging only for critical errors, most information communicated through return values or metrics.

**Testing Approach**

The skill must ask: "What testing strategy will you implement?" Provide these options:

Option A: No automated tests initially - Focus on getting functionality working, add tests later. Only appropriate for prototypes.

Option B: Unit tests only - Test individual functions and classes in isolation using pytest. Good starting point for most projects.

Option C: Unit and integration tests - Unit tests plus integration tests that verify components work together. Recommended for production systems.

Option D: Comprehensive testing - Unit tests, integration tests, and end-to-end tests with coverage requirements. Required for critical production systems.

After the user selects options for all architecture decisions, the skill must present a summary of the selected architecture and require confirmation before proceeding. The summary should highlight how these choices interact and any implications for later decisions.

### Design Review Critic - Checkpoint 1

Immediately after the user confirms Stage 1 architecture decisions, the skill must activate the Design Review Critic sub-agent to evaluate the design choices. This is a mandatory checkpoint that cannot be skipped.

**Design Review Critic Evaluation Framework:**

The critic evaluates the design across five dimensions, scoring each from 0 to 5 points:

**Clarity (0-5 points):** Are the goals, responsibilities, and boundaries of the system clearly defined? Are there ambiguous terms or concepts that could lead to misinterpretation? Are the architectural choices clearly motivated by the stated requirements?

**Completeness (0-5 points):** Are there silent assumptions that should be made explicit? Have all major architectural concerns been addressed? Are there obvious missing pieces that will cause problems later?

**Maintainability (0-5 points):** Will this architecture be easy to understand and modify months or years from now? Are conventions consistent and well-documented? Is complexity localized and managed?

**Extensibility (0-5 points):** Can the system evolve to handle new requirements without major rewrites? Are abstractions appropriate for the problem domain? Is there flexibility where it matters?

**Risk and Design Smells (0-5 points):** Are there hidden complexities or premature abstractions? Does the design exhibit common anti-patterns? Are there decisions that seem inconsistent with the stated maturity level or project scope?

The total score ranges from 0 to 25 points, with the following interpretation:

- 20-25 points: Excellent design, ready to proceed
- 15-19 points: Good design with minor concerns, recommend addressing warnings before proceeding
- 10-14 points: Significant issues present, recommend revising key decisions
- 0-9 points: Major design problems, must revise before proceeding

**Critic Output Format:**

The critic must provide:

1. A score table showing points for each dimension with brief justification
2. A list of specific risks identified in the current design
3. A list of missing decisions or ambiguities that should be resolved
4. Specific recommendations for improvement

The skill must present this critique to the user and offer three options:

A. Proceed despite warnings - Accept the design as-is and continue
B. Revise decisions - Return to Stage 1 to modify architecture choices
C. Request clarification - Ask the critic to explain specific concerns in more detail

The user must select an option explicitly. If option B is selected, the skill must allow the user to modify any architecture decisions and then re-run the critic.

### Stage 2: Repository Structure Definition

After architecture decisions are confirmed and reviewed, the skill must work with the user to define the exact repository structure. This stage determines what directories and files will exist in the final repository.

**Repository Structure Elicitation Process:**

The skill must present three options for determining repository structure:

**Option A: User-Specified Structure** - The user will provide a complete directory tree specification showing all directories and key files that should exist. The skill will accept this specification and use it as-is.

**Option B: Guided Structure Builder** - The skill will ask a series of questions about which components are needed and propose a structure based on the answers. The user must review and approve or modify the proposed structure.

**Option C: Template-Based Structure** - The skill will propose a minimal template structure appropriate for the project type identified in Stage 0. The user may accept it as-is or request modifications.

If the user selects Option A, the skill must request the structure specification in a clear format such as:

```
repository-name/
├── src/
│   └── package_name/
│       ├── __init__.py
│       ├── core/
│       ├── api/
│       └── utils/
├── tests/
├── docs/
├── examples/
└── [other directories]
```

If the user selects Option B, the skill must ask:

1. Do you need separate directories for different logical components? If yes, what are the component names?
2. Will you expose an API or CLI? If yes, should there be dedicated directories for these?
3. Do you need directories for configuration files, data files, or schemas?
4. Do you want examples or tutorials as part of the repository?
5. What testing structure do you prefer: tests adjacent to code or in a separate directory?
6. Do you want documentation in the repository? If yes, what format: Markdown files, Sphinx, MkDocs, or other?

Based on the answers, the skill must propose a complete directory structure and present it to the user for approval.

If the user selects Option C, the skill must generate a minimal template based on the project purpose identified in Stage 0. For example:

- For an API service: separate directories for API routes, business logic, data access, and tests
- For a library: src-layout with package code under src/, tests in tests/, examples in examples/
- For a CLI tool: entry point scripts, command modules, configuration handling

The proposed structure must be labeled explicitly as a template and the user must be informed they can modify it before approval.

**Structure Approval and Lock-In:**

Regardless of which option is chosen, the skill must present the final proposed structure in a clear tree format and require explicit approval with the statement: "This repository structure will be used for all subsequent code generation. Changes to the structure later will require regenerating affected files. Do you approve this structure?"

Only after receiving explicit approval may the skill proceed. The approved structure becomes the canonical reference for all file generation tasks.

### Stage 3: Optional Feature Selection

With the core structure defined, the skill must now determine which optional features should be included. These features represent significant additional code and configuration that should only be generated if explicitly needed.

**Mandatory Feature Gate Questions:**

The skill must ask the following questions and record yes or no answers for each. Default answers must not be assumed; the user must provide explicit input for each question.

**API Layer Generation:**

"Do you want to generate a REST or GraphQL API layer that exposes your backend functionality over HTTP? If yes, please specify: (A) FastAPI with automatic OpenAPI documentation, (B) Flask with manual route definition, (C) GraphQL using Strawberry or Graphene, (D) Other framework."

If the user answers yes, ask follow-up: "What is the intended purpose of this API? (A) External public API, (B) Internal service-to-service communication, (C) Development and testing only."

**Web UI or Admin Panel:**

"Do you want to generate any web-based user interface such as an admin panel, dashboard, or control interface? If yes, please specify the technology: (A) Server-rendered templates with Jinja2, (B) React or Vue SPA, (C) Simple HTML forms, (D) Admin framework like Django Admin or Flask-Admin."

Most backend libraries do not need a UI. The skill should note: "Recommendation: Select NO unless this is specifically an application with user-facing interfaces. Backend libraries typically do not include UIs."

**Demo or Example Code:**

"Do you want runnable demo code that shows how to use the backend? If yes, specify the style: (A) Minimal quick-start example showing basic usage, (B) Tutorial-style examples demonstrating common workflows, (C) Comprehensive examples covering all major features."

**Documentation Site:**

"Do you want to generate a documentation site that can be hosted on ReadTheDocs, GitHub Pages, or similar platforms? If yes, specify: (A) Sphinx with reStructuredText, (B) MkDocs with Markdown, (C) Minimal Markdown files only, no build system."

If yes, ask follow-up: "What should the documentation include? Select all that apply: (A) API reference generated from docstrings, (B) Tutorials and how-to guides, (C) Architecture and design documentation, (D) Contributing guidelines."

**Additional README Files:**

"The repository will include a primary README.md file in English. Do you want to generate README files in additional languages? If yes, specify which languages and the skill will generate separate README files such as README.zh.md for Chinese, README.ja.md for Japanese, etc."

**Default Behavior for Unanswered Questions:**

If the user does not provide an explicit answer to any feature gate question, the skill must treat it as NO and exclude that feature. The skill may suggest defaults but must wait for confirmation. For example: "No answer provided for API layer generation. Proceeding without API components unless you specify otherwise."

**Feature Confirmation Summary:**

After collecting answers to all feature gate questions, the skill must present a summary:

```
Feature Selection Summary:
- API Layer: [YES with details | NO]
- Web UI: [YES with details | NO]
- Demo Code: [YES with details | NO]
- Documentation Site: [YES with details | NO]
- Additional README Languages: [List of languages | None]

This selection determines what will be generated. Features marked NO will be completely excluded from generation. Please confirm this selection is correct.
```

The user must provide explicit confirmation before proceeding.

### Design Review Critic - Checkpoint 2

After feature selection is confirmed, the Design Review Critic must evaluate the repository structure and feature selection for consistency and appropriateness.

**Checkpoint 2 Evaluation Criteria:**

The critic must check:

1. **Over-Engineering Risk:** Does the selected feature set match the maturity level and lifecycle specified in Stage 0? For example, a short-term prototype should not have comprehensive documentation sites and admin panels.

2. **Coupling Concerns:** Will the repository structure create tight coupling between components? Are there directories or features that will make the code harder to test or modify?

3. **Future Extensibility:** Does the structure accommodate likely future changes without requiring reorganization? Are there obvious extension points?

4. **Consistency:** Do the selected features work well together? For example, if demo code was requested but the repository structure has no examples directory, that is an inconsistency.

5. **Maintenance Burden:** Is the user taking on too much maintenance work given the stated project lifespan and team size?

The critic must provide specific warnings such as:

- "Warning: You selected production-ready maturity but chose not to include automated tests. This is inconsistent and will make production deployment risky."
- "Warning: Your repository structure has six top-level directories but you indicated this is a simple library. Consider simplifying the structure."
- "Notice: You enabled both API documentation and a documentation site. Ensure these are integrated so users have a single source of truth."

The skill must present these warnings and require the user to either proceed with acknowledgment or revise the structure or feature selection.

### Stage 4: Atomic Generation Plan

With all design decisions finalized, the skill must now create a detailed execution plan that breaks the repository generation into atomic tasks. Each atomic task represents the smallest reasonable unit of work that can be independently generated, validated, and approved.

**Atomic Task Definition:**

An atomic task includes:

1. **Task Identifier:** A unique number or code for referencing the task
2. **File Path:** The exact path to the file or files this task will generate
3. **Purpose:** One-sentence description of what this file accomplishes
4. **Dependencies:** Other tasks that must be completed before this task
5. **Required Public Interface:** Classes, functions, constants that must be defined
6. **Sub-Prompt:** The complete formatted prompt to send to the code generation agent
7. **Validation Specification:** How success will be verified
8. **Estimated Effort:** Indication of complexity such as trivial, simple, moderate, complex

**Task Ordering Principles:**

Tasks must be ordered such that:

- Foundational files are generated before files that depend on them
- Core functionality precedes optional features
- Test files are generated after the code they test
- Configuration and infrastructure files come early to establish conventions
- Documentation is generated after the features being documented

**Generation Plan Presentation:**

The skill must present the complete atomic task list in a table or structured format:

```
Atomic Generation Plan (Total: N tasks)

Task 1: Project Infrastructure
  Path: pyproject.toml
  Purpose: Define package metadata, dependencies, build configuration
  Dependencies: None
  Validation: Valid TOML syntax, includes required package name and version

Task 2: Package Initialization
  Path: src/[package_name]/__init__.py
  Purpose: Package entry point, version definition, primary exports
  Dependencies: Task 1
  Validation: Imports successfully, version string defined

[... continue for all tasks ...]

Task N: Primary README
  Path: README.md
  Purpose: Repository overview, installation instructions, quick start
  Dependencies: All code tasks completed
  Validation: Markdown syntax valid, includes all required sections
```

**User Approval and Reordering:**

After presenting the atomic generation plan, the skill must ask: "Please review this generation plan. You may: (A) Approve and proceed with generation in this order, (B) Request reordering of specific tasks with justification, (C) Request addition or removal of tasks, (D) Request detailed breakdown of any specific task."

The user must provide explicit approval before any code generation begins. If the user requests changes, the skill must update the plan and re-present it for approval.

**Task Execution Workflow:**

Once the plan is approved, the skill enters the execution phase where it processes tasks one by one:

1. Present the current task identifier and purpose
2. Provide the sub-prompt for the code generation agent
3. Instruct the user to generate the code using their chosen agent
4. Request the user to provide the generated code or confirm it is in place
5. Perform automated validation checks
6. Present validation results
7. Request user confirmation to accept, modify, or regenerate
8. Record the result and proceed to the next task

This workflow continues until all tasks are completed or the user chooses to abort.

## Sub-Prompt Template Specification

For every atomic code generation task, the skill must format the sub-prompt using the following precise template. This template is designed to work with any capable code generation agent by providing complete context and clear requirements.

**Template Structure:**

```
=== ATOMIC TASK: [Task Identifier] ===

OBJECTIVE: [One-sentence goal]

TARGET FILE: [Exact relative path from repository root]

LANGUAGE AND VERSION: Python [version specification]

CONTEXT:
[Provide essential background information:]
- What role does this file play in the larger system?
- What problem does it solve?
- How does it relate to other components?

HARD CONSTRAINTS:
- Type annotations required: [yes/no, specify policy]
- Maximum file length: [N lines or no limit]
- Allowed standard library imports: [list or "any"]
- Allowed third-party imports: [specific list or "none" or "any from project dependencies"]
- Prohibited imports: [list any forbidden imports]
- Code style: [black/flake8/other, specify config]
- Docstring style: [Google/NumPy/reStructuredText]

PUBLIC API REQUIREMENTS:
[List all classes, functions, constants that must be publicly exported]
[Include type signatures for all public APIs]

class ExampleClass:
    """Brief description."""
    
    def __init__(self, param: Type) -> None:
        """Initialize with param."""
        
    def public_method(self, arg: Type) -> ReturnType:
        """Method description."""

def public_function(arg: Type) -> ReturnType:
    """Function description."""

EXAMPLE_CONSTANT: Type = value

IMPLEMENTATION REQUIREMENTS:
- [Specific algorithms, patterns, or approaches to use]
- [Edge cases to handle]
- [Error handling expectations]
- [Performance considerations]
- [Security requirements]

TESTING EXPECTATIONS:
Associated test file: [path to test file]
Key behaviors to verify:
- [Behavior 1]
- [Behavior 2]
[If generating test file, specify test cases in detail]

VALIDATION CRITERIA FOR SUCCESS:
- File contains all required public API elements
- Type hints present and correct
- Docstrings follow specified style
- Code formatted per style guide
- [Any specific checks like "no use of global variables"]

INTEGRATION POINTS:
[List other modules this file will import]
[List modules that will import this file]
[Describe data flow or control flow relationships]

OUTPUT REQUIREMENTS:
- Complete, self-contained file content
- Module-level docstring explaining purpose
- Inline comments only where behavior is non-obvious
- No debug code, print statements, or TODO markers
- Code must be production-ready, not a sketch or outline

EXAMPLE USAGE:
[If appropriate, provide a small code snippet showing how this module will be used]

```python
# Example of how this component will be used
from package_name.module import ExampleClass

obj = ExampleClass(param)
result = obj.public_method(arg)
```

=== END SUB-PROMPT ===
```

**Template Usage Guidelines:**

Every sub-prompt must be complete and self-contained. The code generation agent should not need to ask clarifying questions if the sub-prompt is properly filled out. The skill must populate all sections with specific, concrete information rather than generic placeholders.

When presenting the sub-prompt to the user for passing to their agent, the skill should indicate whether the sub-prompt should be used in a chat interface, as inline comments, or as a file specification, depending on the agent being used.

## Validation and Quality Assurance

After each atomic task produces code, the skill must perform automated validation to ensure quality and catch common errors before the user manually reviews the code.

### Automated Validation Checks

**Syntax Validation:**

The skill must verify that the generated Python file has valid syntax. This can be done using Python's built-in `py_compile` module or equivalent. If syntax errors are detected, the skill must report the specific errors and their line numbers.

**Contract Verification:**

The skill must verify that all required public API elements specified in the sub-prompt are present in the generated code. This includes checking for:

- Required class definitions with expected names
- Required method signatures with correct names and parameter lists
- Required function definitions
- Required constants or module-level variables

The skill should parse the generated code (using ast module or similar) to verify these elements exist.

**Import Validation:**

The skill must verify that the generated code only imports modules that were explicitly allowed in the sub-prompt constraints. Any imports of prohibited modules should be flagged as violations.

**Style Compliance:**

If the project has style requirements like black formatting, the skill should check that the generated code complies. This might involve running black in check mode or similar linters.

**Test Execution:**

If the atomic task included a test specification and tests have been generated, the skill should attempt to run the tests and report results. Tests should be run in an isolated environment to avoid side effects.

**Validation Result Reporting:**

After running all applicable checks, the skill must present results in this format:

```
Validation Results for Task [N]: [File path]

Syntax Check: [PASS | FAIL]
[If FAIL, include error details]

Contract Verification: [PASS | FAIL | PARTIAL]
Required elements found: [list]
Missing elements: [list if any]

Import Validation: [PASS | FAIL]
[If FAIL, list prohibited imports detected]

Style Compliance: [PASS | FAIL | NOT CHECKED]
[If FAIL, summarize issues]

Test Execution: [PASS | FAIL | SKIPPED]
[If run, show test results summary]

Overall Assessment: [ACCEPTABLE | NEEDS REVISION]
```

### Error Recovery and Repair

When validation fails, the skill must provide automated assistance to resolve issues without requiring manual code editing by the user.

**Automated Fix Attempts:**

For certain classes of errors, the skill may attempt up to two automated fixes:

1. **Missing Imports:** If contract verification succeeds but the code fails due to undefined names, the skill can attempt to add appropriate imports.

2. **Formatting Issues:** If only style compliance fails, the skill can run black or the specified formatter and present the reformatted code.

3. **Minor Syntax Errors:** For simple syntax errors like missing colons or unmatched brackets, the skill may attempt to fix them.

The skill must present each attempted fix to the user and explain what was changed. The user must approve any automated fixes before they are applied.

**Manual Revision Process:**

If automated fixes do not resolve the issues, the skill must present the user with options:

A. **Accept As-Is:** Proceed despite validation failures, acknowledging the issues will need manual fixing later.

B. **Request Modification:** The user will manually edit the code and re-submit for validation.

C. **Regenerate with Additional Guidance:** Create an enhanced sub-prompt that includes information about the validation failures and asks the agent to specifically address those issues.

D. **Skip Task:** Defer this task and proceed to the next one, marking this task for later attention.

**Failure Tracking:**

The skill must maintain a log of all validation failures and resolutions. This log will be included in the final report provided at the end of the workflow.

## README Generation Requirements

README files are critical documentation that must be generated with high quality. The skill must generate at least one primary README.md file and optionally additional README files in other languages.

### Primary README.md Generation

The primary README must be comprehensive, well-structured, and appropriate for the project type. The skill must generate README content that includes the following sections in order:

**Project Title and Description:**

The README must begin with the project name as a level-1 heading, followed immediately by a concise one-sentence description that clearly states what the project does and who it is for. Below this, provide a more detailed paragraph explaining the project's purpose and value proposition.

**Status Badges:**

Include relevant badges that convey important project metadata. Always include:

- License badge showing the license type
- Python version badge indicating minimum supported Python version

Conditionally include if applicable:

- CI/CD status badge if GitHub Actions or similar is configured
- Coverage badge if test coverage tracking is implemented
- PyPI version badge if the package will be published
- Documentation status badge if using ReadTheDocs or similar

**Installation Instructions:**

For uv-based projects:

```bash
# Install uv if not already installed
pip install uv

# Install the package
uv pip install package-name

# Or for development
git clone https://github.com/user/repo-name.git
cd repo-name
uv venv
uv pip install -e ".[dev]"
```

For pip-based projects:

```bash
pip install package-name
```

For Poetry projects:

```bash
poetry add package-name
```

For PDM projects:

```bash
pdm add package-name
```

**Quick Start Example:**

Provide a minimal code example showing the simplest possible usage of the package. This should be complete and runnable code that demonstrates the core functionality:

```python
from package_name import MainClass

# Create instance with minimal configuration
instance = MainClass(required_param="value")

# Perform primary operation
result = instance.main_method()
print(result)
```

**Core Features:**

List the primary features or capabilities of the backend in a clear, scannable format. Focus on what the package does, not how it does it. Each feature should be described in one or two sentences.

**Architecture Overview:**

Provide a high-level explanation of how the backend is organized. Describe the main components and how they interact. If the project has a specific architectural pattern, name it and explain why it was chosen. Keep this section concise but informative.

**Repository Structure:**

Show the directory tree structure with brief explanations of what each major directory contains:

```
repository-name/
├── src/package_name/     # Core package source code
├── tests/                # Test suite
├── docs/                 # Documentation source
├── examples/             # Usage examples
└── pyproject.toml        # Project configuration
```

**Usage Documentation:**

Provide more detailed usage examples beyond the quick start. This section should include common use cases and patterns. Organize by scenario or feature area. Include code examples for each usage pattern.

**Configuration:**

Explain how to configure the backend, including what configuration options are available, where configuration should be placed, and what the defaults are. If using environment variables, list all supported variables. If using configuration files, provide an example configuration file.

**Development Setup:**

Explain how to set up a development environment for contributing to the project. Include:

- Cloning the repository
- Installing the appropriate dependency manager if not using pip (with specific instructions for uv, Poetry, or PDM)
- Installing development dependencies using the project's chosen tool
- Setting up pre-commit hooks if applicable
- Running tests locally
- Building documentation locally

For uv-based projects, emphasize the speed benefits and include instructions for common operations:

```bash
# Clone and setup
git clone https://github.com/user/repo-name.git
cd repo-name

# Create virtual environment and install dependencies (fast!)
uv venv
uv pip install -e ".[dev]"

# Run tests
uv run pytest

# Add a new dependency
uv pip install new-package
# Then manually add to pyproject.toml dependencies

# Update lock file
uv lock
```

**Testing:**

Describe the testing approach and how to run tests:

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=package_name

# Run specific test file
pytest tests/test_module.py
```

Explain what types of tests exist (unit, integration, end-to-end) and what they cover.

**Contributing:**

Provide basic contribution guidelines or link to a separate CONTRIBUTING.md file if one exists. Include information about:

- Code style requirements
- How to submit issues
- Pull request process
- Where to ask questions

**License:**

State the license type clearly and link to the LICENSE file:

```
This project is licensed under the MIT License - see the LICENSE file for details.
```

**Additional Sections (Conditional):**

Include these sections only if the corresponding features were enabled in Stage 3:

- **API Documentation:** Link to the API documentation site or OpenAPI spec
- **Web UI:** Explain how to access and use the UI
- **Examples:** List and describe the available example code
- **Deployment:** Instructions for deploying the backend to production

**README Quality Standards:**

The README must:

- Use proper Markdown formatting with consistent heading levels
- Include working links to all referenced documentation and resources
- Contain only accurate information that reflects what actually exists in the repository
- Avoid placeholder text or TODO items
- Be written in clear, professional English
- Be appropriate for the intended audience identified in Stage 0

### Additional Language README Generation

If the user requested README files in additional languages during Stage 3, the skill must generate separate README files for each requested language.

**Naming Convention:**

Additional README files must follow the pattern README.[language-code].md where language-code is the ISO 639-1 two-letter language code. Examples:

- README.zh.md for Chinese
- README.ja.md for Japanese
- README.es.md for Spanish
- README.fr.md for French

**Content Requirements:**

Each language-specific README must:

- Contain a complete translation of the primary README.md content
- Maintain the same section structure and order as the English README
- Translate all prose, headers, and descriptive text into the target language
- Keep code examples, commands, and technical identifiers in English
- Preserve all links, even if they point to English-language resources
- Include a note at the top indicating this is a translated version: "This is a [Language] translation of the README. The primary documentation is in English."

**Translation Quality:**

The skill must generate translations that:

- Use appropriate technical terminology for the target language
- Maintain professional tone consistent with the English version
- Preserve formatting, code blocks, and structure exactly
- Do not use machine translation artifacts like awkward phrasing

**Maintenance Note:**

The skill should add a comment to each translated README explaining that it may become outdated as the project evolves and should be kept in sync with the English version.

## Comprehensive Final Report

When all atomic tasks are completed or the user chooses to finalize the workflow, the skill must generate a comprehensive final report that documents the entire generation process, all decisions made, validation results, and next steps.

### Report Structure and Content

**Executive Summary:**

Provide a high-level overview including:

- Project name and purpose
- Total number of atomic tasks planned
- Number of tasks successfully completed
- Number of tasks that failed or were skipped
- Overall assessment of repository readiness

**Design Decisions Record:**

Document all major decisions made during the workflow:

- Stage 0 Design Intake responses
- Stage 1 Architecture choices
- Stage 2 Repository structure
- Stage 3 Feature selection
- Design Review Critic scores and recommendations at each checkpoint

**Generation Results:**

For each atomic task, document:

- Task identifier and file path
- Generation status (completed, failed, skipped, deferred)
- Validation results summary
- Any automated fixes applied
- User feedback or modification requests

**File Inventory:**

List all files created during the workflow with:

- File path
- File size
- Purpose
- Status (validated, needs review, has issues)

**Quality Metrics:**

Provide aggregate quality metrics:

- Total lines of code generated
- Number of files with passing validation
- Test coverage percentage if calculable
- Style compliance rate

**Outstanding Issues:**

List any problems that were not resolved:

- Files that failed validation
- Missing functionality
- Known bugs or limitations
- Technical debt items

**Next Steps:**

Provide clear actionable guidance on what the user should do next:

1. How to verify the repository is functional
2. Commands to run tests and confirm everything works
3. What manual work remains to be done
4. Recommended next development tasks

**Warnings and Recommendations:**

Include any important notices:

- Security considerations
- Performance implications of design choices
- Maintenance recommendations
- Suggested improvements

**Appendices:**

Include supporting information:

- Complete list of all sub-prompts used
- All Design Review Critic reports
- Log of user confirmations and decisions

### Report Format Options

The skill must offer the report in multiple formats:

- **Markdown:** A comprehensive .md file suitable for viewing in any text editor or committing to the repository
- **HTML:** A formatted HTML document with navigation and styling for easier reading
- **JSON:** A structured JSON file for programmatic processing
- **Plain Text:** A simple .txt file for minimal environments

The user should be asked which format they prefer, with Markdown as the recommended default.

## Skill-Level Documentation Requirements

In addition to generating backend repositories, the python-backend-creator skill itself must include comprehensive documentation that explains how to use the skill effectively.

### Skill README.md Structure

The skill's own README.md file must follow this structure:

**Title and Value Proposition:**

Begin with a clear, compelling title: "python-backend-creator: Interactive, Human-in-the-Loop Python Backend Scaffolding"

Follow with a one-sentence value proposition explaining the problem the skill solves: "python-backend-creator guides you through creating industrial-grade Python backend repositories using a structured, decision-driven workflow that produces minimal, testable, well-documented code atomically rather than through fragile end-to-end generation."

**Problem Statement:**

Explain why this skill exists:

"Traditional code generation tools produce complete projects in one operation, often making assumptions about architecture, dependencies, and structure without user input. This leads to bloated repositories with unnecessary complexity, poor documentation, and code that does not match the user's actual requirements. When generation fails partway through, users are left with partial, broken repositories that are difficult to salvage."

**Solution Approach:**

Explain how python-backend-creator addresses these problems:

"python-backend-creator uses a human-in-the-loop methodology where every major decision is explicitly confirmed before any code is generated. The workflow breaks repository creation into small atomic tasks that are independently generated, validated, and approved. A built-in Design Review Critic evaluates architecture choices before implementation begins, catching issues early when they are easier to fix."

**Key Features:**

List the distinguishing features:

- Stage-based workflow with explicit user confirmation at each stage
- Atomic file-by-file generation with validation after each file
- Built-in Design Review Critic providing engineering maturity scoring
- Support for multiple code generation agents through standardized sub-prompts
- Comprehensive quality assurance including syntax checking, contract verification, and test execution
- Flexible feature selection allowing users to include only what they need
- Production-ready defaults including CI/CD, containerization, and documentation

**Supported Environments:**

Clearly document where and how the skill can be used:

"python-backend-creator is designed to work with any code generation agent that can interpret structured prompts and generate Python code. It has been specifically tested and optimized for:

- GitHub Copilot in Visual Studio Code
- Claude Code command-line tool
- Cursor IDE
- ChatGPT Code Interpreter
- Other LLM-based coding assistants

The skill provides agent-specific integration guidance to help you use it effectively with your preferred tool."

**Installation and Setup:**

Provide clear instructions for adding the skill to different environments:

For GitHub Copilot:
[Specific instructions for loading the skill in Copilot]

For Claude Code:
[Specific instructions for using with Claude Code]

For general use:
[Instructions for using the skill prompts directly]

**Workflow Overview:**

Provide a high-level summary of the workflow stages:

"The python-backend-creator workflow consists of five mandatory stages:

Stage 0: Design Intake - Comprehensive questionnaire capturing project requirements, goals, and constraints

Stage 1: Core Architecture - Explicit decisions on dependency management, configuration, error handling, logging, and testing

Stage 2: Repository Structure - Definition and approval of exact directory layout and file organization

Stage 3: Optional Features - Selection of API layers, UIs, documentation, and other add-ons

Stage 4: Atomic Generation - Execution of individual file generation tasks with validation and approval

Each stage includes checkpoints where the Design Review Critic evaluates the design for quality and consistency."

**Example Usage:**

Provide a concrete example showing how to invoke the skill:

"To begin using python-backend-creator, start by invoking the skill with your initial project description:

'I want to create a Python backend for [brief description]. The backend should [key requirements]. I plan to [deployment context].'

The skill will guide you through the structured workflow, asking clarifying questions and requiring explicit confirmation at each stage."

Include a sample interaction showing a few rounds of the question-and-answer flow.

**Design Philosophy:**

Explain the principles behind the skill:

"python-backend-creator is built on the belief that good architecture emerges from explicit, informed decisions rather than automated defaults. By forcing users to think through design choices early and validating those choices before implementation, the skill helps create backends that truly match requirements and are easier to maintain long-term."

**Contributing to the Skill:**

Explain how users can contribute improvements, report issues, or request features for the skill itself.

**License:**

State the license under which the skill is distributed.

## Security and Safety Requirements

The skill must enforce security best practices and never generate code that poses security risks.

### Prohibited Patterns

The skill must never generate code that:

- Contains hard-coded secrets, API keys, passwords, or credentials of any kind
- Includes commented-out passwords or credentials as examples
- Makes unauthenticated network requests to production services
- Executes arbitrary code from untrusted sources without sandboxing
- Uses dangerous functions like eval() or exec() on user input
- Implements weak cryptography or deprecated security algorithms
- Exposes sensitive information in logs or error messages
- Contains SQL queries vulnerable to injection attacks

### Security Validation

During the validation phase for each atomic task, the skill must check for common security issues:

- Scan for hard-coded secrets using pattern matching
- Identify use of dangerous functions
- Check for potential injection vulnerabilities in database queries or system commands
- Verify that sensitive data is not logged

If any security issues are detected, the skill must flag them as critical validation failures and refuse to proceed until they are resolved.

### Secrets Management Guidance

When generating code that requires secrets, the skill must:

- Use environment variables for all secrets
- Include example .env.example files showing required variables without values
- Generate documentation explaining how to set up secrets securely
- Never include actual secret values in generated code or documentation

## Testing Requirements

All generated backends must include comprehensive testing infrastructure.

### Test Structure

Based on the testing strategy selected in Stage 1, the skill must generate:

**For unit testing:**

- A tests/ directory with one test file per source module
- pytest configuration in pyproject.toml or pytest.ini
- Test fixtures for common setup and teardown
- Parametrized tests for functions with multiple cases

**For integration testing:**

- Separate integration test directory or marked integration tests
- Fixtures that set up test databases or services
- Tests that verify components work together correctly
- Cleanup logic to ensure tests do not interfere with each other

### Test Quality Standards

All generated tests must:

- Run quickly (full test suite under 30 seconds for initial implementation)
- Be deterministic with no flaky tests
- Not require network access or external services
- Use synthetic or mocked data only
- Have clear, descriptive names following pattern test_[behavior]_[condition]
- Include docstrings explaining what is being tested and why
- Assert specific expected outcomes, not just that code runs without error

### Test Coverage

The skill should aim for reasonable test coverage:

- Core functionality: 80%+ coverage
- Utility functions: 100% coverage
- Integration points: Key paths covered
- Error handling: Exception paths tested

### CI Integration

The generated GitHub Actions or other CI configuration must:

- Run the full test suite on every push and pull request
- Fail the build if any tests fail
- Report test coverage if configured
- Cache dependencies to speed up test runs

## Continuous Integration and DevContainer Configuration

### GitHub Actions Workflow

The skill must generate a .github/workflows/ci.yml file that:

- Triggers on push to main branch and on all pull requests
- Uses a matrix strategy to test on multiple Python versions if appropriate
- Sets up Python using actions/setup-python
- Installs the appropriate dependency manager based on project choice
- Installs the package with development dependencies using the project's dependency manager
- Runs pytest with appropriate flags
- Runs code style checks (black, flake8, mypy as applicable)
- Caches dependencies appropriately based on the dependency manager used
- Uploads coverage reports if coverage tracking is configured
- Provides clear failure messages when checks fail

The dependency installation step must be customized based on the chosen dependency manager:

For uv projects:
```yaml
- name: Install uv
  run: pip install uv
  
- name: Install dependencies
  run: |
    uv venv
    uv pip install -e ".[dev]"
```

For Poetry projects:
```yaml
- name: Install Poetry
  uses: snok/install-poetry@v1
  
- name: Install dependencies
  run: poetry install
```

For PDM projects:
```yaml
- name: Install PDM
  run: pip install pdm
  
- name: Install dependencies
  run: pdm install
```

For pip projects:
```yaml
- name: Install dependencies
  run: pip install -e ".[dev]"
```

The caching strategy must also be adapted to the dependency manager:

For uv projects: Cache the uv cache directory and virtual environment

For Poetry projects: Use the Poetry-specific cache action or cache ~/.cache/pypoetry

For PDM projects: Cache __pypackages__ directory

For pip projects: Cache pip's cache directory

### DevContainer Configuration

The skill must generate a .devcontainer/devcontainer.json file that:

- Specifies an appropriate base Python image matching the target version
- Includes a postCreateCommand that installs development dependencies using the project's chosen dependency manager
- Configures useful VS Code extensions for Python development
- Sets up appropriate environment variables
- Mounts the repository code into the container
- Provides a description explaining the environment

The postCreateCommand must be tailored to the dependency management choice:

For uv projects: `"postCreateCommand": "pip install uv && uv venv && uv pip install -e .[dev]"`

For Poetry projects: `"postCreateCommand": "pip install poetry && poetry install"`

For PDM projects: `"postCreateCommand": "pip install pdm && pdm install"`

For pip projects: `"postCreateCommand": "pip install -e .[dev]"`

The DevContainer should allow developers to:

- Open the project in GitHub Codespaces with one click
- Use VS Code Remote Containers locally
- Have a consistent development environment across team members
- Have the chosen dependency manager pre-installed and configured

## Dependency Management Details

Based on the dependency management choice made in Stage 1, the skill must generate appropriate configuration files.

### For uv Projects

Generate pyproject.toml with:

- Complete package metadata in [project] section following PEP 621 standards (name, version, description, authors, license, readme)
- Python version requirement specified in requires-python field
- Core dependencies listed in dependencies array with version constraints
- Optional dependency groups in [project.optional-dependencies] for dev, docs, testing
- Build system configuration using [build-system] with appropriate backend
- Tool-specific sections for pytest, black, mypy, and ruff if applicable

Generate uv.lock file or include instructions to run `uv lock` to create the lock file ensuring reproducible installations.

Provide README instructions for:

```bash
uv venv                    # Create virtual environment
uv pip install -e ".[dev]" # Install in development mode with dev dependencies
uv pip install -e .        # Install only runtime dependencies
uv pip compile pyproject.toml -o requirements.txt  # Generate requirements file
uv pip sync requirements.txt  # Install from requirements file
uv run pytest              # Run tests in the uv-managed environment
```

Include a note explaining that uv provides significantly faster installation and resolution compared to traditional pip, making it particularly suitable for projects with many dependencies or CI/CD environments where speed matters.

### For Poetry Projects

Generate pyproject.toml with:

- Complete package metadata (name, version, description, authors, license)
- Python version requirement
- Core dependencies with version constraints
- Optional dependency groups for dev, docs, testing
- Build system configuration
- Tool-specific sections for pytest, black, mypy

Generate poetry.lock file or include instructions to run `poetry lock`.

Provide README instructions for:

```bash
poetry install           # Install all dependencies
poetry install --no-dev  # Install only runtime dependencies
poetry add package_name  # Add new dependency
poetry run pytest        # Run tests
```

### For PDM Projects

Generate pyproject.toml with:

- Package metadata following PEP 621 standards in [project] section
- Python version requirement
- Dependencies with version specifiers
- Optional dependency groups
- Build system configuration for PDM
- Tool configurations

Generate pdm.lock file or include instructions to run `pdm lock`.

Provide README instructions for:

```bash
pdm install              # Install all dependencies
pdm add package_name     # Add new dependency
pdm run pytest           # Run tests
pdm build                # Build distribution packages
```

### For pip Projects

Generate pyproject.toml with:

- Package metadata in [project] section
- Dependencies list
- Optional dependencies in [project.optional-dependencies]
- Build backend configuration

Generate requirements.txt with pinned versions if appropriate.

Generate requirements-dev.txt with development dependencies.

Provide README instructions for:

```bash
pip install -e ".[dev]"  # Install in development mode
pip install -r requirements.txt  # Install runtime dependencies
```

### Dependency Version Management

The skill must:

- Use version constraints that allow patch updates but prevent breaking changes
- Pin exact versions only for dependencies with known compatibility issues
- Separate core runtime dependencies from development dependencies
- Document why any unusual version constraints exist
- For uv projects, leverage uv's fast resolver to handle complex dependency graphs efficiently
- For Poetry and PDM projects, commit lock files to version control for reproducibility
- For pip projects, consider using pip-tools for generating locked requirements files

## Documentation Generation

Based on the documentation choices made in Stage 3, the skill must generate appropriate documentation infrastructure.

### For Sphinx Documentation

Generate docs/ directory containing:

- conf.py with appropriate Sphinx configuration
- index.rst as the documentation homepage
- Makefile and make.bat for building documentation
- requirements.txt with sphinx and extensions
- Appropriate directory structure for guides, tutorials, API reference

Configure Sphinx to:

- Use a clean, modern theme like sphinx-rtd-theme
- Generate API documentation from docstrings using autodoc
- Support Markdown files using myst-parser if requested
- Include code highlighting and cross-references

### For MkDocs Documentation

Generate docs/ directory containing:

- mkdocs.yml with site configuration
- index.md as the homepage
- Directory structure for different documentation sections
- requirements.txt with mkdocs and plugins

Configure MkDocs to:

- Use material theme or similar modern theme
- Enable search functionality
- Support code highlighting and admonitions
- Include navigation structure

### Documentation Content

Generate documentation files for:

- Getting Started / Quickstart guide
- Installation instructions
- Basic usage examples
- Architecture overview
- Contributing guidelines
- API reference (if auto-generated from docstrings)

All documentation must:

- Be written in clear, professional English
- Include working code examples
- Be accurate and match the actual implementation
- Be formatted consistently
- Include navigation between related pages

## Final Skill Behavior Summary

To ensure the skill-creator fully understands what python-backend-creator must do, here is a complete summary of expected behavior:

When a user invokes python-backend-creator, the skill:

1. Presents the Design Intake Questionnaire asking comprehensive questions about project purpose, technical requirements, lifecycle, and constraints. Waits for complete answers and confirms all responses.

2. Requests explicit decisions on core architecture including dependency management, configuration approach, error handling, logging, and testing strategy. Summarizes choices and confirms.

3. Invokes Design Review Critic to evaluate architecture decisions, providing scores and identifying risks. Presents critique to user and offers options to proceed or revise.

4. Works with user to define exact repository structure through user specification, guided builder, or template selection. Confirms final structure before proceeding.

5. Asks explicit yes/no questions about optional features including API layer, web UI, demo code, documentation site, and additional language READMEs. Records selections clearly.

6. Invokes Design Review Critic again to evaluate repository structure and feature selection for consistency and appropriateness. Presents warnings and requires acknowledgment.

7. Creates detailed atomic generation plan listing every file to be generated as a separate task with dependencies, validation criteria, and sub-prompts. Presents plan and requires user approval.

8. Executes atomic tasks one by one, providing formatted sub-prompts for the code generation agent, performing validation checks on generated code, presenting results to user, and requiring confirmation before proceeding.

9. Generates comprehensive README.md and any requested additional language READMEs following specified structure and quality standards.

10. Generates final report documenting all decisions, generation results, quality metrics, outstanding issues, and next steps.

Throughout this process, the skill prioritizes clarity over speed, confirmation over assumption, and quality over quantity. The skill is helpful and supportive but firm about requiring explicit decisions and not proceeding with ambiguous or incomplete information.

## Deliverables Expected from skill-creator

When the skill-creator processes this prompt, it must produce:

1. A complete implementation specification for python-backend-creator including all workflow stages, validation logic, and user interaction patterns described in this document.

2. The formatted sub-prompt template that python-backend-creator will use for all atomic code generation tasks, filled out completely with no placeholder sections.

3. Five example sub-prompts showing exactly what python-backend-creator would send to a code generation agent for the first five atomic tasks of a typical Python backend repository. These examples must be complete, specific, and production-ready.

4. The Design Intake Questionnaire formatted as it would be presented to users, with all questions numbered and organized clearly.

5. The Design Review Critic scoring rubric with detailed descriptions of what constitutes each score level (0-5) for each of the five evaluation dimensions.

6. A sample final report showing the structure and content that python-backend-creator would generate after completing a repository generation workflow.

7. The skill's own README.md file following the structure specified in the Skill-Level Documentation Requirements section, written in clear professional English.

8. Integration guides for GitHub Copilot, Claude Code, and Cursor showing specifically how users would invoke python-backend-creator and use its sub-prompts with each agent.

9. A troubleshooting guide addressing common issues users might encounter such as validation failures, agent compatibility problems, or workflow interruptions.

10. Example invocation showing a complete interaction between a user and python-backend-creator from initial invocation through final report delivery, demonstrating the flow through all stages.

All deliverables must be in English. All code examples must be syntactically correct and follow Python best practices. All documentation must be clear, professional, and appropriate for experienced software developers who are the target users of this skill.

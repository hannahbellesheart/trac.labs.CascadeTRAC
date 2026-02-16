---
# INSTRUCTIONS
# - Files that contain instructions and/or rules, that provide specific guidelines for code generation, Q&A, and reviews in this repository. When present, they are combined in the context with the following precedence (highest to lowest): system instructions > developer instructions > user instructions > this file. If there are conflicting rules, follow the higher-precedence instructions first.
# - Attributes
#   - `description` (required): A brief summary of the instructions' purpose and scope.
#   - `applyTo` (optional): A comma-separated list of glob patterns. If provided, these instructions will automatically be included in the context for any files matching the specified patterns. If not provided, these instructions must be explicitly referenced by other instructions or prompts, or manually included in the context.
# - Best practices
#   - Written in markdown format with a YAML frontmatter header that supports limited attributes (description, applyTo).
#   - Provide clear, actionable guidelines specific to this project.
#   - Reference key files or documentation that exemplify important patterns.
#   - Avoid generic advice; focus on THIS project's specific approaches.
#   - Keep instructions concise (20-50 lines) but comprehensive enough to guide an AI agent effectively.
#   - Update instructions as the project evolves, and consider adding versioning or change logs if there are frequent updates.
#   - Typically stored in `.github/instructions/` or `.github/copilot-instructions.md`, but can be referenced from anywhere in the repo. 
#   - File names should be descriptive of the instructions' purpose (e.g., `backend-coding-standards.md`, `testing-guidelines.md`, `security-best-practices.md`, etc).
#   - YAML comments are allowed in the frontmatter and use the standard YAML syntax (lines starting with #).
#   - Markdown comments are allowed in the body using <!-- comment --> syntax.
description: Sample GitHub Copilot instructions for CascadeTRAC (code generation, Q&A, and reviews).
applyTo: "**/*.{ts,tsx,js,jsx,py,md,yml,yaml,json},**/Dockerfile,**/*.sh"
---
<!-- BODY -->

# CascadeTRAC Copilot Instructions (Sample)

Use these rules when generating code, answering questions, or reviewing changes in this repository.

## 1) Scope and precedence
- These instructions are repository-specific.
- If there is a conflict, follow higher-priority instructions first (system > developer > user > this file).

## 2) Project context
- Repo: `{$githubUsername$}/{$githubRepoName$}`
- Environment: Ubuntu 24.04.3 LTS dev container.
- Prefer minimal, targeted diffs over broad refactors.

## 3) External references
- Architecture: [../../docs/architecture.md](../../docs/architecture.md)
- Coding standards: [../../docs/coding-standards.md](../../docs/coding-standards.md)
- API contract: [../../docs/api/openapi.yaml](../../docs/api/openapi.yaml)
- GitHub Copilot custom instructions docs: <https://docs.github.com/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot>

If a referenced file is missing, state that clearly and continue with best effort.

## 4) Coding guidelines
### General
- Match existing style, naming, and patterns in nearby files.
- Keep changes small and cohesive.
- Avoid adding dependencies unless required.
- Update tests when behavior changes.

### Reliability and errors
- Handle error paths explicitly.
- Return actionable error messages.
- Do not swallow exceptions silently.

### Security
- Validate untrusted input.
- Never hardcode secrets or tokens.
- Avoid logging sensitive data.

## 5) Review/change expectations
- Include a short rationale for non-obvious changes.
- Mention risks and rollback considerations for impactful edits.
- Note what was tested and what was not.

## 6) Command and tooling notes
- Provide commands compatible with Ubuntu-based dev containers.
- To open a URL in the host browser, use:
  - `"$BROWSER" <url>`

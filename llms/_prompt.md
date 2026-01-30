# LLM Documentation Generator

This folder contains LLM-friendly copies of the keystone-suite documentation.

## Instructions for LLM

When asked to update this folder, follow these steps:

### 1. Discover all documentation files

Search the repository for markdown files:
- `docs/readme.md` - Main suite overview
- `docs/technical/*.md` - Technical documentation
- `docs/planning/*.md` - Planning documents (if any)

### 2. Generate consolidated files

Create the following files in this folder:

#### `keystone.md`
Combine all documentation into a single file with clear section headers. Structure:
- Overview (from readme.md)
- Architecture (from technical/architecture.md)
- Infrastructure (from technical/infrastructure.md)
- Any other technical docs

#### `keystone.txt`
Same content as `keystone.md` but in plain text format (no markdown formatting).

### 3. Format guidelines

- Use clear section separators (`---` or `===`)
- Include source file paths as comments at the start of each section
- Remove redundant content between files
- Keep all code examples intact
- Preserve tables in a readable format

### 4. Exclude from consolidation

- This `_prompt.md` file
- Git-related files
- Workflow files (`.github/`)
- Any files with credentials or secrets

## File manifest

After generating, update this section with the list of generated files:

| File | Description | Last Updated |
|------|-------------|--------------|
| keystone.md | Full documentation in markdown format | 2025-01-28 |
| keystone.txt | Full documentation in plain text format | 2025-01-28 |

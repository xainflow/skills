# Contributing to Xainflow Skills

Thank you for your interest in contributing skills to the Xainflow community! This guide explains how to submit your own creative AI skills.

## Before You Start

1. Read the [SKILL.md Specification](spec/SPEC.md) to understand the format
2. Browse [existing skills](skills/official/) for examples
3. Make sure your skill idea doesn't duplicate an existing one

## Contribution Workflow

### 1. Fork and Clone

```bash
git clone https://github.com/YOUR_USERNAME/skills.git
cd skills
```

### 2. Create Your Skill

Create a new folder under `skills/community/`:

```bash
mkdir skills/community/your-skill-name
```

Create a `SKILL.md` file inside it:

```bash
touch skills/community/your-skill-name/SKILL.md
```

### 3. Write Your Skill

Your `SKILL.md` must include:

- **Valid YAML frontmatter** with all required fields (`name`, `description`, `category`, `argument-hint`)
- **Clear instructions** in the body that Claude can follow
- **Cost estimation** — always show credit costs before execution
- **User confirmation** — always ask before executing

See [spec/SPEC.md](spec/SPEC.md) for the complete format reference.

### 4. Validate Your Skill

Before submitting, check:

- [ ] Folder name matches the `name` field in frontmatter
- [ ] `name` is lowercase with hyphens only (max 64 chars)
- [ ] `category` is one of: photography, social, branding, tools, video, marketing, workflow, custom
- [ ] `argument-hint` is wrapped in brackets: `[your-hint]`
- [ ] No workspace-specific UUIDs in the file
- [ ] Referenced MCP tools exist (see spec for the full list)
- [ ] Instructions include a confirmation step before executing
- [ ] Credit budget is realistic for the described workflow

### 5. Submit a Pull Request

```bash
git checkout -b add-your-skill-name
git add skills/community/your-skill-name/SKILL.md
git commit -m "feat: add your-skill-name skill"
git push origin add-your-skill-name
```

Open a Pull Request against `main`. Fill out the PR template.

## Skill Guidelines

### Do

- Write clear, step-by-step instructions
- Include cost estimates and confirmation steps
- Use specific MCP tool names (e.g., `xainflow_generate_image`)
- Specify aspect ratios for all image outputs
- Handle edge cases (no assets found, insufficient credits)
- Write everything in English

### Don't

- Include workspace-specific IDs (folder IDs, style IDs, etc.)
- Hardcode file paths or URLs
- Create skills that duplicate existing ones
- Exceed reasonable credit budgets without justification
- Skip the user confirmation step

## Naming Conventions

- **Folder name**: lowercase, hyphens only (e.g., `my-cool-skill`)
- **Skill name**: must match folder name exactly
- **Display name**: optional, human-readable version (e.g., `My Cool Skill`)

## Categories

| Category | Use For |
|---|---|
| `photography` | Product shots, compositions, photo workflows |
| `social` | Social media content, platform-specific formats |
| `branding` | Brand-consistent assets, style-driven generation |
| `tools` | Utility workflows, batch processing, cleanup |
| `video` | Video generation, motion content |
| `workflow` | Multi-step automated pipelines |
| `custom` | Anything else |

## Review Process

1. A maintainer will review your PR
2. We check for format compliance, clarity, and usefulness
3. You may receive feedback requesting changes
4. Once approved, your skill is merged and available on the Hub

## Questions?

Open an issue on the repository if you have questions about the format or contribution process.

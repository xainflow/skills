# SKILL.md Format Specification

Version: 1.0

## Overview

A skill is a markdown file (`SKILL.md`) with YAML frontmatter that defines a reusable AI workflow for the Xainflow platform. Skills are executed by Claude via MCP tools and provide structured instructions for creative content generation.

## File Structure

Every skill lives in its own folder:

```
{skill-name}/
└── SKILL.md
```

The folder name **must** match the `name` field in the frontmatter.

## Frontmatter

The frontmatter is a YAML block delimited by `---` at the top of the file.

### Required Fields

| Field | Type | Description | Example |
|---|---|---|---|
| `name` | string | Unique identifier. Lowercase, hyphens only, max 64 chars. Must match folder name. | `product-shots` |
| `description` | string | One sentence describing what the skill does. | `Generate professional product photography variations from a source image.` |
| `category` | enum | One of: `photography`, `social`, `branding`, `tools`, `video`, `workflow`, `custom` | `photography` |
| `argument-hint` | string | What the user provides when invoking the skill. Wrapped in brackets. | `[product-image-or-description]` |

### Optional Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `display-name` | string | Derived from `name` | Human-readable name shown in UI |
| `tags` | string[] | `[]` | Keywords for search and filtering |
| `output-folder` | string | — | Folder name where outputs are saved |
| `create-folder` | boolean | `false` | Whether to create the output folder if it doesn't exist |
| `default-model` | string | — | Default AI model to use (e.g., `gemini-3-pro-image-preview`) |
| `default-ratio` | string | — | Default aspect ratio (e.g., `1:1`, `16:9`, `9:16`) |
| `credit-budget` | integer | — | Maximum credits the skill should consume per execution |
| `asset-type` | enum | — | Type of output: `image`, `video`, `svg` |
| `reference-assets` | string | — | Description of input assets the skill expects |
| `use-styles` | boolean | `false` | Whether the skill reads workspace styles |
| `use-variables` | boolean | `false` | Whether the skill reads workspace variables |
| `requires-context` | string | — | Comma-separated list of context keys to resolve from workspace data |

### Example Frontmatter

```yaml
---
name: product-shots
description: Generate professional product photography variations from a source image.
argument-hint: [product-image-or-description]

output-folder: Product Shots
create-folder: true
reference-assets: product images
asset-type: image
default-model: gemini-3-pro-image-preview
default-ratio: 1:1
credit-budget: 100
---
```

## Body

The body is standard Markdown following the frontmatter. It contains the instructions that Claude follows when executing the skill.

### Recommended Sections

| Section | Purpose |
|---|---|
| `# {Skill Name}` | H1 heading matching the skill name |
| `## Inputs` | What the user needs to provide and how to gather it |
| `## Workflow structure` | What to generate, models to use, ratios, variations |
| `## Organization` | Where and how to save outputs |
| `## Before executing` | What to show/confirm before running |

### Writing Guidelines

1. **Be specific about tools** — Reference Xainflow MCP tools by name (e.g., `xainflow_generate_image`, `xainflow_remove_background`)
2. **Include ratios** — Always specify aspect ratios for each output
3. **Show costs** — Always include credit cost information before execution
4. **Ask for confirmation** — Skills should always confirm with the user before executing
5. **Handle errors gracefully** — Include fallback instructions when relevant

## Context Injection

Skills can request workspace-specific context using `requires-context`:

```yaml
---
name: brand-social-pack
requires-context: brand_guidelines, color_palette
---
```

When executed, the platform resolves these keys against the workspace's styles and prompt variables, injecting personalized data. This means the same skill produces different results per workspace.

### Resolution Order

1. Match against `styles` table (style_prompt of the most recent non-system style)
2. Match against `prompt_variables` table (name ILIKE match, type = 'text')

## Available MCP Tools

Skills can reference these Xainflow platform tools:

| Tool | Description |
|---|---|
| `xainflow_generate_image` | Generate images with various AI models |
| `xainflow_generate_video` | Generate videos from images or prompts |
| `xainflow_remove_background` | Remove image backgrounds |
| `xainflow_upscale` | Upscale image resolution |
| `xainflow_vectorize` | Convert images to SVG vectors |
| `xainflow_list_assets` | Browse workspace assets and folders |
| `xainflow_list_styles` | Get workspace style definitions |
| `xainflow_list_variables` | Get workspace prompt variables |
| `xainflow_manage_folders` | Create, rename, or delete folders |
| `xainflow_manage_assets` | Move, rename, or delete assets |

## Categories

| Category | Description |
|---|---|
| `photography` | Product photography, shots, compositions |
| `social` | Social media content for various platforms |
| `branding` | Brand-consistent assets, logos, guidelines |
| `tools` | Utility workflows (batch processing, cleanup) |
| `video` | Video generation and editing |
| `workflow` | Multi-step automated pipelines |
| `custom` | Anything that doesn't fit above |

## Validation Rules

1. Frontmatter must be valid YAML between `---` markers
2. `name` must match the containing folder name
3. `name` must be lowercase with hyphens only, max 64 characters
4. `category` must be one of the allowed values
5. `credit-budget` must be a positive integer when specified
6. No workspace-specific UUIDs (folder IDs, style IDs, variable IDs)
7. Referenced tools must exist in the MCP tools list above
8. `default-ratio` must use the format `W:H` (e.g., `1:1`, `16:9`)

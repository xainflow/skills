# Xainflow Skills Repository

## Overview

Public open-source repository for Xainflow creative AI skills. Contains official skills by Xainflow and community-contributed skills. All skills use the SKILL.md format.

Repository: https://github.com/xainflow/skills

## Repository Structure

```
skills/
├── README.md
├── CONTRIBUTING.md
├── LICENSE (MIT)
├── CLAUDE.md
├── spec/
│   └── SPEC.md                      ← SKILL.md format specification
├── skills/
│   ├── official/                     ← Maintained by Xainflow team
│   │   ├── product-shots/SKILL.md
│   │   ├── social-media-kit/SKILL.md
│   │   ├── brand-assets/SKILL.md
│   │   ├── background-batch/SKILL.md
│   │   └── video-promo/SKILL.md
│   └── community/                    ← Community contributions via PR
│       └── {skill-name}/SKILL.md
└── .github/
    └── PULL_REQUEST_TEMPLATE.md
```

## Git Workflow

- **Main branch**: `main` — protected, requires PR review
- NEVER push directly to main
- All changes via Pull Requests
- Commit messages: Conventional Commits, max 50 chars, imperative

## Content Rules

- ALL content in English
- Skills must follow the SKILL.md specification in `spec/SPEC.md`
- Every skill is a folder containing at minimum a `SKILL.md` file
- Folder names must match the skill `name` field (lowercase, hyphens only)
- No workspace-specific UUIDs in any skill file (folder IDs, style IDs, etc.)

## SKILL.md Format

Required frontmatter fields:
- `name`: lowercase, hyphens, max 64 chars
- `description`: one sentence, what the skill does
- `category`: one of photography, social, branding, tools, video, workflow, custom
- `argument-hint`: what the user provides (in brackets)

Optional frontmatter fields:
- `display-name`, `tags`, `output-folder`, `create-folder`, `default-model`
- `default-ratio`, `credit-budget`, `asset-type`, `reference-assets`
- `use-styles`, `use-variables`, `requires-context`

## Xainflow Platform Context

Skills are executed on the Xainflow platform via MCP tools:
- `xainflow_generate_image` — Image generation (multiple models)
- `xainflow_generate_video` — Video generation
- `xainflow_remove_background` — Background removal
- `xainflow_upscale` — Image upscaling
- `xainflow_vectorize` — SVG vectorization
- `xainflow_list_assets` — Browse workspace assets
- `xainflow_list_styles` — Get workspace styles
- `xainflow_list_variables` — Get workspace variables
- `xainflow_manage_folders` — Create/manage folders
- `xainflow_manage_assets` — Move/rename assets

## When Editing Skills

1. Ensure frontmatter is valid YAML between `---` markers
2. Verify `name` field matches the folder name
3. Check that referenced tools actually exist (see list above)
4. Keep credit-budget realistic for the workflow described
5. Never include workspace-specific IDs or paths

## Adding a New Official Skill

1. Create folder: `skills/official/{skill-name}/`
2. Create `SKILL.md` with valid frontmatter + instructions
3. Update `README.md` official skills table
4. PR to main

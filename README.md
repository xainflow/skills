# Xainflow Skills

Creative AI skills for Xainflow — reusable workflows for image, video, and brand content generation.

## What are Skills?

Skills are reusable AI workflows written in Markdown (`SKILL.md`) that Claude executes on the [Xainflow](https://xainflow.com) platform. Each skill defines a structured creative workflow — from product photography to social media content to video promos — that can be cloned into any workspace and personalized with your brand context.

## Official Skills

| Skill | Category | Description |
|---|---|---|
| [product-shots](skills/official/product-shots/SKILL.md) | photography | Professional product photography variations optimized for e-commerce and social media |
| [social-media-kit](skills/official/social-media-kit/SKILL.md) | social | Multi-platform content from a single concept — Instagram, TikTok, stories, banners |
| [brand-assets](skills/official/brand-assets/SKILL.md) | branding | Brand-consistent images using workspace styles and variables |
| [background-batch](skills/official/background-batch/SKILL.md) | tools | Batch background removal for multiple images at once |
| [video-promo](skills/official/video-promo/SKILL.md) | video | Short promotional videos from product images or concepts |

## Community Skills

Community-contributed skills live in [`skills/community/`](skills/community/). Want to contribute? See [CONTRIBUTING.md](CONTRIBUTING.md).

## How It Works

1. **Browse** the Skills Hub inside Xainflow or via MCP (`xainflow_list_skills hub:true`)
2. **Clone** a skill to your workspace with one click (or `xainflow_clone_skill`)
3. **Execute** — Claude follows the skill instructions using your workspace's brand context
4. **Same skill, different results** — context injection personalizes outputs per workspace

## SKILL.md Format

Skills use a simple Markdown format with YAML frontmatter. See the full [specification](spec/SPEC.md).

```yaml
---
name: my-skill
description: What this skill does in one sentence.
category: photography
argument-hint: [what-the-user-provides]

output-folder: My Outputs
create-folder: true
default-model: gemini-3-pro-image-preview
credit-budget: 100
---

# My Skill

Instructions that Claude follows when executing this skill.
```

## Contributing

We welcome community contributions! Read the [Contributing Guide](CONTRIBUTING.md) to get started.

## License

MIT — see [LICENSE](LICENSE).

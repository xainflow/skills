---
name: brand-assets
description: Generate brand-consistent images using workspace styles and variables. Leverages existing brand guidelines for visual consistency.
argument-hint: [description-of-what-you-need]

output-folder: Brand Assets
create-folder: true
use-styles: true
use-variables: true
default-model: gemini-3-pro-image-preview
credit-budget: 200
---

# Brand Assets

Generate images that follow the workspace brand guidelines.

## Setup

1. Check available styles with xainflow_list_styles. If the workspace has styles, ask which one to apply. If a default-style is set above, suggest it first.
2. Check available variables with xainflow_list_variables. Show relevant variables and their values.
3. Ask the user what they need (campaign images, social posts, product visuals, etc.)

## Workflow structure

Build a workflow that combines:
- The selected style as visual reference (use style_prompt for prompt consistency)
- Relevant variables expanded into multiple prompts
- Appropriate ratios for the use case

Use the default-model from the frontmatter.

## Organization

Save outputs to the output-folder specified above. Create it if it does not exist.

## Before executing

Show which style, variables, and combinations will be used. Show total credit cost (respect credit-budget). Ask for confirmation.

---
name: social-media-kit
description: Generate a set of images optimized for multiple social media platforms from a single concept or image. Creates variations for Instagram, TikTok, stories, and web banners.
argument-hint: [concept-or-image]

output-folder: Social Media
create-folder: true
asset-type: image
default-model: gemini-3-pro-image-preview
credit-budget: 100
---

# Social Media Kit

Create a multi-platform content workflow from a single concept or image.

## Inputs

Ask the user for their concept (text prompt) or source image, and which platforms they need content for.

## Workflow structure

Create a workflow with platform-specific variations:
- Instagram feed post (1:1)
- Instagram/TikTok story (9:16)
- Web banner / Twitter header (16:9)
- Pinterest pin (2:3)

Each variation should adapt the composition for the target format, not just crop. Use the default-model from the frontmatter.

## Organization

Save outputs to the output-folder specified above. Create it if it does not exist.

## Before executing

Show a summary with platforms, total images, and credit cost. Ask for confirmation.

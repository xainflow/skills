---
name: product-shots
description: Generate professional product photography variations from a source image. Creates a workflow with multiple shots optimized for e-commerce and social media.
argument-hint: [product-image-or-description]

output-folder: Product Shots
create-folder: true
reference-assets: product images
asset-type: image
default-model: gemini-3-pro-image-preview
default-ratio: 1:1
credit-budget: 100
---

# Product Shots

Create a product photography workflow with multiple shot types.

## Inputs

Ask the user for their product image. Accept a file path, URL, or an existing asset from their Xainflow library (use xainflow_list_assets to browse).

## Workflow structure

Create a workflow with these shot variations:
- Clean product on white background (1:1) — e-commerce ready
- Lifestyle/environmental shot (4:3) — contextual usage
- Detail/macro close-up (1:1) — texture and quality

Use the default-model from the frontmatter (gemini-3-pro-image-preview, 20cr, highest quality). Let the user choose a different model if they prefer.

## Organization

Save outputs to the output-folder specified above. Create it if it does not exist. Name the workflow after the product.

## Before executing

Show a summary with number of shots, model, and total credit cost. Ask for confirmation.

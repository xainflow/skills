---
name: video-promo
description: Create a short promotional video from a product image or concept. Generates a video using AI video models.
argument-hint: [product-image-or-concept]

output-folder: Video Promos
create-folder: true
asset-type: image
---

# Video Promo

Create a short promotional video from an image or concept.

## Inputs

Ask the user for:
- Source image or text concept (browse assets with xainflow_list_assets if needed)
- Video style/mood (dynamic, elegant, energetic, minimal)
- Duration preference if relevant

## Workflow structure

Create a workflow with:
- An asset or prompt node for the source
- A video generator node configured for the chosen style

Video generation is asynchronous. After executing, poll with xainflow_get_workflow_run until the video is ready.

## Organization

Save outputs to the output-folder specified above. Create it if it does not exist.

## Before executing

Show the estimated credit cost and video model being used. Ask for confirmation. Remind the user that video generation takes longer than images.

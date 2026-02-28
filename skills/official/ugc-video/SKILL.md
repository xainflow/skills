---
name: ugc-video
description: Create UGC-style video ads with AI-generated creators showcasing products. Builds a complete workflow with character creation, script writing, and video generation.
argument-hint: [product-image-or-brief]

output-folder: UGC Videos
create-folder: true
reference-assets: product images, character photos
asset-type: all
default-model: seedance-1.5-pro
default-ratio: 9:16
credit-budget: 500
---

# UGC Video Creator

Create an authentic UGC (User Generated Content) video ad workflow. UGC videos feature a creator naturally showcasing a product with casual, authentic energy — like a real person recommending something they love on TikTok or Instagram Reels.

## Phase 1: Collect Inputs

Adapt to what the user already has. Ask one thing at a time.

### Product

Ask the user for their product. Accept any of:
- An existing asset from their library (use `xainflow_list_assets` to browse)
- A file path or URL to a product image
- A text description (you will generate the product image later)

If the user provides a text description only, note that you will need to generate a product image using `xainflow_generate_image` before building the workflow.

### Character / Creator

Ask the user if they have a character/creator image or want one generated.
- If they have one: accept an existing asset, file path, or URL
- If they want one generated: ask for a brief description (age, gender, ethnicity, style, setting). You will generate it using `xainflow_generate_image` with a detailed prompt for a realistic person looking at camera in a casual setting.

### Orientation

Ask the user what format they want:
- **9:16** (vertical) — TikTok, Instagram Reels, YouTube Shorts (recommended for UGC)
- **16:9** (landscape) — YouTube, ads
- **1:1** (square) — Instagram feed

Default to 9:16 if they do not specify.

### Brief

Ask the user about their product:
- Product name and what it is
- Key selling points (2-3 max)
- Target audience
- Tone: casual/fun, professional, excited, calm
- Any specific phrases or CTAs they want included

## Phase 2: Script Generation

Write a UGC video script based on the brief. UGC scripts sound natural and unscripted.

### Script Guidelines

**Intro (hook) — 2-4 seconds:**
- Start with a casual hook that grabs attention
- Examples: "Okay so I have to tell you about this...", "I just found the best [product category]...", "POV: you finally discover [benefit]..."
- Must feel spontaneous, not rehearsed

**Product showcase — 4-8 seconds:**
- Naturally introduce the product
- Show genuine enthusiasm, not salesy pitch
- Mention 1-2 key benefits conversationally
- Examples: "Look at this packaging though...", "And the best part is...", "I've been using it for a week and honestly..."

### Present to User

Show the script to the user with clear labels:
```
INTRO: "[hook text]"
PRODUCT: "[product showcase text]"
```

Ask if they want to edit anything. Iterate until they approve.

## Phase 3: Generate Missing Assets

If the user needs a character or product image generated:

### Character Generation

Use `xainflow_generate_image` with:
- model: `gemini-pro` (highest quality for realistic people)
- aspect_ratio: match the chosen orientation (9:16, 16:9, or 1:1)
- prompt: Detailed description of a real person. Include:
  - Physical appearance (age, features, hair)
  - Clothing style (casual, trendy — matches UGC aesthetic)
  - Setting (home, kitchen, bathroom, outdoors — wherever product is used)
  - Expression (genuine smile, excited, natural)
  - Camera: medium close-up, looking at camera, natural lighting
  - IMPORTANT: Do NOT mention any product in the character prompt
- save_to_gallery: true

Show the generated character to the user. Ask if they want to regenerate or proceed.

### Product Generation (if needed)

Use `xainflow_generate_image` with:
- model: `gemini-pro`
- aspect_ratio: 1:1 (product shots work best square)
- prompt: Clean product photo on simple background
- save_to_gallery: true

## Phase 4: Build Workflow

Use `xainflow_create_workflow` with a complete `flow_state` containing all nodes and edges.

### Workflow Name

Name it: "UGC - [Product Name]" (e.g., "UGC - Organic Ketchup")

### Required Nodes

Build the workflow with these nodes. Positions assume 9:16 orientation — adjust group widths if landscape.

Use the user's chosen orientation for `ratio` (imageGenerator) and `videoRatio` (videoGenerator).

Use the user's chosen orientation to determine video duration:
- Short scripts (under 15 words): `"4"` seconds
- Medium scripts (15-30 words): `"6"` seconds
- Long scripts (30+ words): `"8"` seconds

**GROUPS (7 groups):**

| ID | Label | Position | Size (w x h) | Color |
|----|-------|----------|--------------|-------|
| group-characters | Characters | (470, 30) | 1650 x 550 | cyan |
| group-input | Input | (150, 700) | 450 x 380 | yellow |
| group-initial-video | Initial Video | (470, 700) | 1950 x 550 | green |
| group-object | Object | (150, 1370) | 450 x 380 | yellow |
| group-message-object | Message Object | (150, 1850) | 450 x 380 | yellow |
| group-image-object | Image Object | (470, 1370) | 1650 x 550 | blue |
| group-video-object | Video Object | (2250, 1370) | 700 x 550 | green |

All groups: `zIndex: -1`, `fontSize: "lg"`

**NODES inside groups:**

| ID | Type | Parent Group | Position (relative) | Key Data |
|----|------|-------------|---------------------|----------|
| asset-character | asset | group-characters | (40, 80) | `{ assetType: "character", selectedAsset: { file_url: "[CHARACTER_URL]" }, output: "[CHARACTER_URL]" }` |
| prompt-char-desc | promptGenerator | group-characters | (400, 80) | `{ prompt: "[DETAILED_CHARACTER_DESCRIPTION]", expandEnabled: true }` |
| img-character | imageGenerator | group-characters | (1000, 80) | `{ model: "gemini-3-pro-image-preview", ratio: "[ORIENTATION]", numberOfResults: 1, imageSize: "2K" }` |
| prompt-intro | promptGenerator | group-input | (40, 80) | `{ prompt: "[INTRO_SCRIPT]", expandEnabled: false }` |
| comp-initial | composition | group-initial-video | (40, 80) | `{ name: "Initial Video Composition" }` |
| img-initial | imageGenerator | group-initial-video | (500, 80) | `{ model: "gemini-3-pro-image-preview", ratio: "[ORIENTATION]", numberOfResults: 1, imageSize: "2K" }` |
| vid-initial | videoGenerator | group-initial-video | (1100, 80) | `{ videoModel: "seedance-1.5-pro", videoDuration: "[DURATION]", videoResolution: "720p", videoRatio: "[ORIENTATION]", audioEnabled: true, mode: "image-to-video" }` |
| asset-product | asset | group-object | (40, 80) | `{ assetType: "product", selectedAsset: { file_url: "[PRODUCT_URL]" }, output: "[PRODUCT_URL]" }` |
| prompt-product | promptGenerator | group-message-object | (40, 80) | `{ prompt: "[PRODUCT_SCRIPT]", expandEnabled: false }` |
| comp-product | composition | group-image-object | (40, 80) | `{ name: "Product Composition" }` |
| img-product | imageGenerator | group-image-object | (600, 80) | `{ model: "gemini-3-pro-image-preview", ratio: "[ORIENTATION]", numberOfResults: 1, imageSize: "2K" }` |
| vid-product | videoGenerator | group-video-object | (40, 80) | `{ videoModel: "seedance-1.5-pro", videoDuration: "[DURATION]", videoResolution: "720p", videoRatio: "[ORIENTATION]", audioEnabled: true, mode: "image-to-video" }` |

### Edges

| Source | Source Handle | Target | Target Handle |
|--------|--------------|--------|---------------|
| asset-character | asset-output | comp-initial | asset-input-slot-1 |
| prompt-char-desc | prompt-output | img-character | prompt-input |
| asset-character | asset-output | img-character | asset-input-slot-char |
| prompt-intro | prompt-output | comp-initial | prompt-input |
| img-character | image-output | comp-initial | asset-input-slot-2 |
| comp-initial | composition-output | img-initial | composition-input |
| img-initial | image-output | vid-initial | first-frame-input |
| prompt-intro | prompt-output | vid-initial | prompt-input |
| asset-character | asset-output | comp-product | asset-input-slot-3 |
| asset-product | asset-output | comp-product | asset-input-slot-4 |
| prompt-product | prompt-output | comp-product | prompt-input |
| comp-product | composition-output | img-product | composition-input |
| img-product | image-output | vid-product | first-frame-input |
| prompt-product | prompt-output | vid-product | prompt-input |

### Character Description Prompt

Write a detailed visual description of the character for the `prompt-char-desc` node. This prompt is used to generate a high-quality reference image. Include:
- Physical appearance matching the character asset
- Setting/environment where the UGC video takes place
- Clothing and styling
- Natural pose and expression
- Lighting (natural, warm, casual)
- DO NOT mention any product

### Video Prompt Guidelines

When setting prompts for video nodes:
- Include explicit motion (action verbs with direction/tempo)
- Include camera movement (slow pan, dolly, tracking shot, steadicam)
- With first frame (image-to-video): DON'T re-describe what's visible. Focus on what MOVES and HOW
- Motion verbs: billows, flutters, sways, cascades, drifts, sweeps, blooms, ripples
- For 4-5s: 1 action + 1 camera move
- For 6-10s: 2 beats (start → change)
- No meta-language ("a video of"), no filler ("beautiful", "stunning")
- Include speech in quotes for audio-enabled videos

## Phase 5: Summary & Execution

### Show Summary

Before executing, show the user:
```
UGC Video Workflow Summary:
- Character: [description or asset name]
- Product: [product name]
- Script:
  INTRO: "[intro text]"
  PRODUCT: "[product text]"
- Format: [9:16 / 16:9 / 1:1]
- Video model: Seedance 1.5 Pro (720p, with audio)
- Estimated credits: ~300-500

The workflow will generate:
1. Character reference image (150 cr)
2. Character intro image (150 cr)
3. Intro video clip (~48-96 cr)
4. Character + product image (150 cr)
5. Product video clip (~48-96 cr)
```

Ask for confirmation before executing.

### Execute

Use `xainflow_execute_workflow` with the workflow_id.

**Important:** Video nodes are ASYNCHRONOUS. After execution:
1. Image nodes will complete immediately — report their results
2. Video nodes will return `generation_id` — tell the user videos are processing
3. Wait ~60 seconds, then poll each video with `xainflow_get_video_status(generation_id)`
4. Keep polling every 30 seconds until all videos complete or fail
5. Report final results with video URLs

### Output

Save all generated assets to the output-folder ("UGC Videos"). Create the folder if it does not exist using `xainflow_manage_folders`.

Present final results:
```
UGC Video Complete!

Generated assets:
- Character image: [link]
- Intro video: [link] ([duration]s)
- Product composite image: [link]
- Product video: [link] ([duration]s)

Total credits used: [amount]
```

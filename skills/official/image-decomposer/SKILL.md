---
name: image-decomposer
description: Analyze any image, extract individual elements (characters, UI, icons, text, decorations), and generate isolated versions of each.
argument-hint: [image-to-decompose]

output-folder: Decomposed
create-folder: true
reference-assets: composite images
asset-type: image
default-model: gpt-image-1-5
credit-budget: 200
---

# Image Decomposer

Decompose any composite image into individual layers and elements. Takes a screenshot, banner, ad, mockup, or game asset and creates a workflow that extracts each element in isolation.

## Phase 1: Image Analysis

Analyze the provided image using vision. Identify and list all distinct visual elements with their categories:

| Category | Aspect Ratio | Remove BG | Description |
|---|---|---|---|
| **Background** | Original ratio | No | Scenes, landscapes, ambient backgrounds, effects, patterns, textures |
| **Character** | 1:1 | Yes | People, mascots, avatars, animals |
| **Icon** | 1:1 | Yes | Small symbols, emojis, badges |
| **Text/Title** | 1:1 | Yes | Headings, stylized typography |
| **UI/CTA** | 1:1 | Yes | Buttons, timers, progress bars, badges |
| **Logo/Brand** | 1:1 | Yes | Logotypes, watermarks, brand marks |
| **Product** | 1:1 | Yes | Packshots, physical objects, merchandise |
| **Decorative** | 1:1 | Yes | Frames, borders, ornaments, sparkles, ribbons |

Present the detected elements as a numbered list:

```
Detected elements:
1. Pirate character (Character) - 1:1 + RemoveBG
2. Ship deck and ocean background (Background) - 16:9
3. Title "Plunder the Loot" with skull decorations (Text/Title) - 1:1 + RemoveBG
4. Reward icons: gold coins, cash bills (Icon) - 1:1 + RemoveBG
5. Ship wheel with target board (Decorative) - 1:1 + RemoveBG
6. CTA button "Default" (UI/CTA) - 1:1 + RemoveBG
```

## Phase 2: User Confirmation

Let the user review and adjust the element list:
- Remove elements: "remove #6"
- Add elements: "add the corner skulls as decorative"
- Edit categories: "#5 is an icon, not decorative"
- Merge elements: "merge #4 and #5"
- Confirm: "ok" / "looks good"

## Phase 3: Build Workflow

Use `xainflow_create_workflow` to create a workflow named "Decompose - [image name]".

### Workflow Structure

Build the workflow with these nodes:

**Asset node** (original image):
```
Position: (0, 0)
Type: asset
Data: { selectedAsset: { file_url: "[IMAGE_URL]" }, output: "[IMAGE_URL]" }
```

**For each element**, create an imageGenerator node:
```
Position: (400, element_index * 200)
Type: imageGenerator
Model: gpt-image-1-5 (or user's choice — models with high reference support work best)
Ratio: category ratio from table above
```

**Extraction prompt for each element:**
```
Extract only the [element description] from this reference image.
Generate it isolated on a clean white background.
Maintain the exact same style, colors, proportions, and details as in the original.
Do not add, remove, or modify any details.
Output only the [element description], nothing else.
```

**For elements that need background removal**, add a removeBackground node:
```
Position: (800, element_index * 200)
Type: removeBackground
```

### Edges

- Asset → each imageGenerator (asset-input)
- imageGenerator → removeBackground (where applicable)

### Layout

Nodes organized in 3 columns:

```
Col 1 (x=0)          Col 2 (x=400)              Col 3 (x=800)
[Asset: Original]  -> [Gen: Background]
                   -> [Gen: Character]         -> [RemoveBG: Character]
                   -> [Gen: Title]             -> [RemoveBG: Title]
                   -> [Gen: Icons]             -> [RemoveBG: Icons]
                   -> [Gen: Decorative]        -> [RemoveBG: Decorative]
                   -> [Gen: CTA]               -> [RemoveBG: CTA]
```

Vertical spacing: ~200px between nodes.

### Recommended Models

Models with high reference support for faithful extraction:
- **GPT Image 1.5** (16 references) — highest fidelity
- **Nano Banana Pro** (14 references) — good balance
- **SeeDream 4.5** (10 references) — alternative

## Before Executing

Show a summary with:
- Number of elements to extract
- Which elements get background removal
- Model being used
- Estimated credits: ~[N elements] x [model cost] + [M removals] x 2 cr

Ask for confirmation.

## Edge Cases

- **Very complex images** (20+ elements): Group similar items (e.g., "all reward icons" as one extraction)
- **Abstract/artistic images**: Propose broad categories, let user refine
- **Text-heavy images**: Each text block can be separate or grouped

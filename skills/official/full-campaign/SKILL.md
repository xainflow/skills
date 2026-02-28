---
name: full-campaign
description: Create a complete marketing campaign with product shots, model photos, multi-platform formats, and promotional videos.
argument-hint: [product-image-or-brief]

output-folder: Campaign
create-folder: true
reference-assets: product images, model/character photos, environment references
asset-type: all
default-model: gemini-3-pro-image-preview
default-ratio: auto
credit-budget: 500
---

# Full Campaign Creator

Create a complete marketing campaign in one workflow. Generate product photography, lifestyle shots with models, multi-platform format adaptations, and promotional videos — all from a single brief.

## CRITICAL: References-First Architecture

**Every campaign workflow follows this pattern:**

```
LAYER 1 — REFERENCES (generated or uploaded first)
  Product image(s), Character/Model image(s), Environment image(s)
  These are the SOURCE OF TRUTH for visual consistency.

LAYER 2 — CAMPAIGN SHOTS (all receive references as input)
  Every shot uses composition nodes to combine the relevant references.
  The prompt describes the SCENE, not the subjects.

LAYER 3 — VIDEOS (use best shots as first frames)
  Promotional clips animated from the generated photos.
```

**WHY:** Without references, each imageGenerator creates its own interpretation of the product/model — resulting in inconsistent looks across the campaign. By generating high-quality references FIRST and feeding them to every downstream node, the AI reproduces the exact same product and person across all shots.

## Phase 1: Collect Inputs

Adapt to what the user has. Ask one thing at a time, be conversational.

### Product

Ask for the product. Accept:
- Existing asset from library (`xainflow_list_assets`)
- File path or URL
- Text description (generate later as a reference)

Ask for: product name, category, key visual features, brand name if any.

### Characters / Models

Ask: "Do you want people/models in your campaign?"

If yes:
- How many different models? (1-3 recommended)
- For each: brief description (age, gender, style, vibe) or existing image
- If generating: use `xainflow_generate_image` with `gemini-pro`, detailed realistic person prompt
- **IMPORTANT**: Character generation prompts must NOT mention the product

If no: skip — campaign will be product-only shots.

### Environments / Settings

Suggest 2-3 environments that match the product category:
- Tech → modern desk, minimal workspace, urban cafe
- Fashion → studio, street, lifestyle apartment
- Food/Drink → kitchen, cafe terrace, picnic, dinner table
- Beauty → bathroom vanity, natural light room, spa
- Fitness → gym, park, living room, trail
- Home → living room, bedroom, kitchen, garden

Present suggestions and let the user pick, modify, or add their own.

### Target Platforms

Ask which platforms the campaign targets:
- **Instagram** feed (1:1, 4:5), stories/reels (9:16), carousel
- **TikTok** (9:16)
- **YouTube** thumbnails (16:9)
- **LinkedIn** posts (1:1, 16:9)
- **Web** banners (16:9, 21:9)
- **E-commerce** product pages (1:1, 3:4)

Default: Instagram + TikTok if they don't specify.

### Content Types

Ask what kind of shots they want (can select multiple):
- **Product hero shots** — clean product on styled backgrounds
- **Lifestyle/model shots** — model using or showcasing the product
- **Different angles** — multi-angle product views (front, side, detail, 45°)
- **Carousel** — connected sequence of images telling a story
- **Environment variations** — same product in different settings
- **Promotional videos** — short video clips for ads/reels

Recommend a combination based on the platforms they chose.

### Video Preferences (if they want videos)

Ask:
- Videos with model + product, product-only motion, or both? (recommend both)
- How many video clips? (2-4 recommended)
- With audio/speech? (recommend yes for model videos, no for product-only)

### Language

Detect the user's language from conversation. Use it for any text, prompts, or speech in videos. Do not ask — adapt naturally.

## Phase 2: Campaign Plan

Create a structured campaign plan before building. Present it clearly.

### Plan Format

```
CAMPAIGN: "[Campaign Name]"
Product: [name]
Models: [count and descriptions]
Environments: [list]
Platforms: [list]

REFERENCES TO GENERATE:
- R1: Product — clean product shot, white/neutral background (1:1)
- R2: Model A — [description], portrait, no product (3:4)
- R3: Environment A — [setting description], empty scene (16:9)
- R4: Environment B — [setting description], empty scene (16:9)

SHOT LIST (all use references above):

1. Product Hero — R1 on styled background (1:1) → uses R1
2. Product Hero — R1 on styled background (4:5) → uses R1
3. Lifestyle — R2 + R1 in R3 setting (9:16) → uses R1, R2, R3
4. Lifestyle — R2 + R1 in R4 setting (1:1) → uses R1, R2, R4
5. Detail shot — R1 close-up (1:1) → uses R1
6. Carousel 1 — R2 discovers R1 (4:5) → uses R1, R2
7. Carousel 2 — R2 holds R1 close-up (4:5) → uses R1, R2
8. Carousel 3 — R1 on lifestyle table (4:5) → uses R1
9. Carousel 4 — R2 with R1, CTA mood (4:5) → uses R1, R2
...

VIDEOS:
A. Model promo — R2 + R1, uses shot #3 as first frame (9:16, 6s)
B. Product motion — R1 hero, uses shot #1 as first frame (16:9, 5s)
```

Ask user to review. Add, remove, or modify shots until approved.

## Phase 3: Model Selection

Present model options based on the campaign scope:

### PRODUCTION Quality (recommended for final campaign)
- **Images**: `gemini-3-pro-image-preview` (Nano Banana Pro) — 150 cr/image, highest quality, up to 14 references, 4K capable
- **Videos**: `kling-v3` (Kling 3.0) — 168-336 cr/s, best video quality, audio

### TESTING / Draft (cheaper, faster iteration)
- **Images**: `seadream-4-5` (SeeDream 4.5) — 40 cr/image, good quality, up to 10 references
- **Videos**: `seedance-1.5-pro` (Seedance 1.5 Pro) — 12-120 cr/s at 480p, cheapest and fast

### Budget-Conscious Mix
- **Images**: `gemini-2-5-flash-nano-banana` (Nano Banana) — 40 cr/image, fast, up to 3 references
- **Videos**: `seedance-1.5-pro` at 720p — 30-60 cr/s, good quality/cost ratio

Show estimated total cost for each tier. Let the user choose or mix models per section.

## Phase 4: Build Workflow

Use `xainflow_create_workflow` with complete flow_state. Name it: "Campaign — [Product Name]".

### CRITICAL: Workflow Structure

The workflow MUST follow this exact layer pattern:

```
┌─────────────────────────────────────────────────────────────┐
│ LAYER 1: REFERENCES (top of canvas)                         │
│                                                             │
│  [Product]     [Character]    [Environment A]  [Env B]      │
│  asset/imgGen  asset/imgGen   promptGen→imgGen  prompt→img  │
│                                                             │
│  These nodes generate or hold the base reference images.    │
│  They are the ONLY nodes that create subjects from scratch. │
└─────────────────────────────────────────────────────────────┘
        │              │              │            │
        ▼              ▼              ▼            ▼
┌─────────────────────────────────────────────────────────────┐
│ LAYER 2: CAMPAIGN SHOTS (middle of canvas)                  │
│                                                             │
│  Each shot group:                                           │
│  [Composition] ← receives relevant refs from Layer 1       │
│       │          + promptGenerator for scene description    │
│       ▼                                                     │
│  [ImageGenerator] ← composition-input                       │
│                                                             │
│  The composition bundles refs + prompt into one input.      │
│  The imageGenerator creates the final shot using them.      │
└─────────────────────────────────────────────────────────────┘
        │ (best shots feed into videos)
        ▼
┌─────────────────────────────────────────────────────────────┐
│ LAYER 3: VIDEOS (bottom of canvas)                          │
│                                                             │
│  [PromptGen(scene)] → [ImageGen(first frame)] → [VideoGen] │
│                              ↑                    ↑         │
│                     refs from Layer 1    prompt(motion)      │
│                     + shot from Layer 2 as additional ref    │
└─────────────────────────────────────────────────────────────┘
```

### Layer 1: References — Node Details

Each reference is a group with a descriptive label.

**Product reference** (if user has image):
```
Group: "Product" (yellow, 450x380, position: x=0, y=0)
  └─ asset node (position: 40, 80)
     data: { selectedAsset: { file_url: "[URL]" }, output: "[URL]" }
```

**Product reference** (if generating):
```
Group: "Product" (yellow, 700x380, position: x=0, y=0)
  ├─ promptGenerator (position: 40, 80)
  │  data: { prompt: "Clean product shot of [product description], white background, studio lighting, centered, high detail" }
  └─ imageGenerator (position: 350, 80)
     data: { model: "[chosen]", ratio: "1:1", numberOfResults: 1 }
  Edge: prompt(prompt-output) → image(prompt-input)
```

**Character reference** (if user has image):
```
Group: "Character — [Name]" (cyan, 450x380, position: x=500+, y=0)
  └─ asset node (position: 40, 80)
     data: { selectedAsset: { file_url: "[URL]" }, output: "[URL]" }
```

**Character reference** (if generating):
```
Group: "Character — [Name]" (cyan, 1650x380, position: x=500+, y=0)
  ├─ promptGenerator (position: 40, 80)
  │  data: { prompt: "[detailed person description, NO product, casual/natural setting]" }
  └─ imageGenerator (position: 350, 80)
     data: { model: "[chosen]", ratio: "3:4", numberOfResults: 1 }
  Edge: prompt(prompt-output) → image(prompt-input)
```

**Environment reference** (always generated):
```
Group: "Environment — [Name]" (yellow, 1650x380, position: x=..., y=0)
  ├─ promptGenerator (position: 40, 80)
  │  data: { prompt: "[detailed empty scene: setting, lighting, mood, colors, textures — NO people, NO product]" }
  └─ imageGenerator (position: 350, 80)
     data: { model: "[chosen]", ratio: "16:9", numberOfResults: 1 }
  Edge: prompt(prompt-output) → image(prompt-input)
```

### Layer 2: Campaign Shots — Node Details

**EVERY shot group** follows this pattern:

```
Group: "[Shot description]" (blue/green, 1650x380)
  ├─ composition (position: 40, 80)
  │  data: { name: "[shot description]" }
  ├─ promptGenerator (position: 40, 250) OR inside composition prompt-input
  │  data: { prompt: "[SCENE description — pose, action, lighting, angle. Do NOT describe the product or person appearance]" }
  └─ imageGenerator (position: 700, 80)
     data: { model: "[chosen]", ratio: "[platform ratio]", numberOfResults: 1 }

Edges:
  - prompt(prompt-output) → composition(prompt-input)
  - composition(composition-output) → imageGenerator(composition-input)
  - [For each relevant reference from Layer 1:]
    referenceNode(image-output or asset-output) → composition(asset-input-{unique-slot-id})
```

**Which references to connect to each shot:**

| Shot Type | Connect Product Ref? | Connect Character Ref? | Connect Environment Ref? |
|-----------|---------------------|----------------------|------------------------|
| Product hero | YES | no | optional (for styled bg) |
| Product angles | YES | no | no |
| Product detail/close-up | YES | no | no |
| Lifestyle (model+product) | YES | YES | YES |
| Model-only shot | no | YES | optional |
| Environment product shot | YES | no | YES |
| Carousel (with model) | YES | YES | optional |
| Carousel (product only) | YES | no | optional |

### Layer 2: Prompt Writing Rules

**CRITICAL — prompts for shots must NOT re-describe the referenced subjects.**

When a composition has references connected:
- Say "the product" or its brand name — do NOT describe its visual appearance
- Say "the model" or "the person" — do NOT describe their face, hair, clothing
- The AI model already SEES the reference images — re-describing causes conflicts

**CORRECT prompt example:**
"The model holds the product at chest height in a modern luxury apartment, looking directly at camera with a confident smile. Warm afternoon window lighting, soft bokeh background. Medium close-up, slightly low angle. Instagram-ready 4:5 composition."

**WRONG prompt example:**
"A young woman in her late 20s with dark brown hair wearing a black dress holds a black leather handbag with gold buckle details in a modern apartment..."

**Focus the prompt on:**
- Pose and action (holds, shows, places, wears, unboxes)
- Setting details (lighting, time of day, atmosphere)
- Camera angle (eye-level, low angle, overhead, medium shot, close-up)
- Composition (centered, rule of thirds, negative space)
- Platform context ("Instagram carousel slide", "TikTok vertical", "YouTube thumbnail")

### Layer 2: Carousel Strategy

For carousel content (3-5 slides):
- Each slide is a separate composition → imageGenerator group
- ALL slides use the same ratio (typically 4:5 for Instagram)
- ALL slides connect to the same product/character references for consistency
- Slides should tell a visual story or show progression:
  - Slide 1: attention-grabbing hero/lifestyle shot
  - Slide 2-3: features, details, use cases
  - Last slide: product + CTA mood ("shop now" feeling)

### Layer 2: Different Angles Strategy

For multi-angle product shots:
- Each angle is a separate composition → imageGenerator group
- ALL angle nodes connect to the same product reference
- The prompt specifies the camera direction:
  - "Front-facing, straight-on product view, studio lighting"
  - "45-degree angled view from the right, dramatic side light"
  - "Top-down flat lay view, centered, even lighting"
  - "Close-up detail of [specific feature], macro style"
  - "Low-angle hero shot, looking up at product, dramatic"

### Layer 3: Videos — Node Details

Each video group:

```
Group: "Video — [description]" (green, 2500x380)
  ├─ promptGenerator (position: 40, 80)
  │  data: { prompt: "[SCENE for first frame — same rules as Layer 2 prompts]" }
  ├─ imageGenerator (position: 700, 80) — generates the first frame
  │  data: { model: "[chosen]", ratio: "[video ratio]", numberOfResults: 1 }
  ├─ promptGenerator (position: 1300, 80) — motion/video prompt
  │  data: { prompt: "[MOTION description — what moves, camera movement, speech in quotes]" }
  └─ videoGenerator (position: 1800, 80)
     data: { videoModel: "[chosen]", mode: "image-to-video", videoResolution: "720p", videoRatio: "[ratio]", videoDuration: "[5-8]", audioEnabled: true/false }

Edges:
  - scenePrompt(prompt-output) → composition or imageGenerator(prompt-input)
  - [relevant refs from Layer 1] → imageGenerator(asset-input-{slotId}) or via composition
  - imageGenerator(image-output) → videoGenerator(first-frame-input)
  - motionPrompt(prompt-output) → videoGenerator(prompt-input)
```

**Optionally**, connect a Layer 2 shot's imageGenerator output as an additional reference to the video's first-frame imageGenerator, so the video matches an already-generated campaign photo.

### Video Prompt Guidelines

Video prompts describe MOTION, not appearance:
- Explicit action verbs with direction/tempo
- Camera movement: slow push-in, orbit, tracking shot, dolly zoom
- With first frame: DON'T re-describe what's visible — only describe what MOVES
- For product-only: "The product rotates slowly on a turntable, catching light highlights. Smooth 360° orbit, soft shadows shift."
- For model+product: "She lifts the product closer to camera with a genuine smile, turning it slightly to show details. Slow steadicam push-in. She says 'You need to see this.'"
- Include speech in quotes for audio-enabled videos

### Layout Positioning

Arrange groups on canvas:

**Layer 1 — References:** y = 0, spread horizontally with 150px gaps
**Layer 2 — Shots:** Start at y = 550, arrange in 2-column grid
  - Column 0: x = 0
  - Column 1: x = 1850
  - Row gap: 130px between groups
  - Group size: 1650 x 380 (or adjust height for tall-ratio images)
**Layer 3 — Videos:** After all Layer 2 groups, with 200px gap
  - Full width: x = 0
  - Group size: 2500 x 380

## Phase 5: Summary & Execution

### Show Summary

```
CAMPAIGN: "[name]"
Product: [name]
Models: [count]

References: [N] (product + characters + environments)
Campaign shots: [N photos]
Videos: [N clips]

Image model: [name] ([cost] cr each)
Video model: [name] ([cost] cr/s)

Estimated credits:
- References: [count] x [cost] = [subtotal]
- Product shots: [count] x [cost] = [subtotal]
- Lifestyle shots: [count] x [cost] = [subtotal]
- Carousel: [count] x [cost] = [subtotal]
- Video first frames: [count] x [cost] = [subtotal]
- Video clips: [details] = [subtotal]
- TOTAL: ~[amount] credits
```

Ask for confirmation. Show cost clearly.

### Execute

Use `xainflow_execute_workflow`. Videos are async:
1. All images complete immediately — report their results
2. Videos return generation_id — tell user they're processing
3. Poll with `xainflow_get_video_status` after ~60 seconds
4. Keep polling every 30 seconds until complete
5. Report final results with all asset links

### Post-Execution

After everything completes, present organized results:
```
CAMPAIGN COMPLETE!

References Generated:
- Product: [link]
- Character: [link]
- Environment A: [link]
- Environment B: [link]

Product Shots:
- Hero 1:1: [link]
- Hero 4:5: [link]
- Detail: [link]

Lifestyle:
- Model in apartment: [link]
- Model on street: [link]

Carousel:
- Slide 1: [link]
- Slide 2: [link]
- Slide 3: [link]
- Slide 4: [link]

Videos:
- Model promo: [link] ([duration]s)
- Product motion: [link] ([duration]s)

Total credits used: [amount]
```

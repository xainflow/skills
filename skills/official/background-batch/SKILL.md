---
name: background-batch
description: Remove backgrounds from multiple images in batch. Processes a folder or selection of assets and saves results in a dedicated folder.
argument-hint: [folder-or-asset-description]

output-folder: No Background
create-folder: true
reference-assets: images to process
asset-type: image
---

# Background Batch

Remove backgrounds from multiple images at once.

## Inputs

Ask the user which images to process. Options:
- A specific folder (use xainflow_list_assets with folder_id to browse the reference-folder if set above)
- Specific assets by name or description matching the reference-assets filter
- All recent uploads

## Process

1. List the matching assets and confirm the selection with the user
2. Save outputs to the output-folder specified above, creating it if it does not exist
3. Process each image with xainflow_remove_background, saving to the folder
4. Report results: how many succeeded, total credits used

## Cost

2 credits per image. Show the total estimate before starting and ask for confirmation.

# comfy-featured

This repository is the content source for AFL CANVAS ComfyUI featured apps.

## Purpose

- Maintain cloud featured apps in plain files
- Edit everything in IDE
- Push to GitHub
- Let AFL CANVAS sync from `manifest.json`

## Repo Root

The Git repository root should directly contain:

```text
manifest.json
apps/
README.md
```

## Structure

```text
apps/
  rh-2031016553440878594/
    item.json
    cover.jpg
  local-cinematic-lighting/
    item.json
    cover.jpg
    workflow.app.json
  _templates/
    rh-template/
      item.json
      README.md
    local-template/
      item.json
      README.md
```

## Naming Rules

- App IDs use `kebab-case`
- RunningHub IDs use `rh-<webappId>`
- Local IDs use `local-<slug>`
- Directory name must match `item.json.id`
- `manifest.json.items[].id` must match both

## Cover Rules

- Preferred filename: `cover.jpg`
- Alternative: `cover.webp`
- Preferred aspect ratio: `4:3`
- Recommended sizes: `1200x900` or `1600x1200`
- Recommended size budget: under `1 MB`

## RunningHub Notes

- `webappId` is the real source
- `cover.jpg` is optional but recommended for stable card display

## Local Notes

- Local apps should include `workflow.app.json`
- This repo stores source files
- Built-in local presets are still shipped from:

```text
E:\AFL CANVAS\Public_assests\ComfyAppLibrary\featured_local\
```

## Manifest URL

If this repository root directly contains `manifest.json` and the default branch is `main`, AFL CANVAS should use:

```text
https://raw.githubusercontent.com/recursionlaplace-eng/comfy-featured/main/manifest.json
```

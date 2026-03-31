---
name: update
description: Version bump a documentation file — archive the old version and create a new one with updates
user_invocable: true
---

# Documentation Update Skill

When the user invokes `/update`, follow these steps:

## 1. Identify the file and change type

Ask the user (if not already provided):
- Which file to update
- Whether this is a **minor** or **major** change

**Version bump rules:**
- **Minor** (prompt tweaks, added sections, wording changes): increment minor version. `v1` → `v1.1`, `v1.1` → `v1.2`, `v2` → `v2.1`
- **Major** (structural rework, architecture change, option chosen from alternatives): increment major version. `v1.2` → `v2`, `v2.1` → `v3`
- First major version has no `.0`: use `v1`, `v2`, `v3`
- Minor versions always have a dot: `v1.1`, `v2.1`

## 2. Archive the current version

- Move the current file to the `Archive/` folder at the project root
- Keep the original filename (with its current version number)
- If a file with the same name already exists in Archive, do NOT overwrite — ask the user

## 3. Create the new version

- Create the new file in the **same directory** as the original
- Update the version number in the filename
- Do NOT put version numbers in the H1 title
- Update the `Updated:` date in the file header to today's date
- Apply the content changes the user requested
- Add a brief `Updates:` line at the bottom of the file listing what changed

## 4. Confirm

Show the user:
- Archived: `[old filename]` → `Archive/`
- Created: `[new filename]`
- Changes applied: brief summary

## Rules

- Never delete the original — always archive first
- The H1 title must NOT contain a version number
- Only the filename carries the version
- No internal `Version:` lines in the file body
- Preserve the `> Created:` date, only update the `> Updated:` date

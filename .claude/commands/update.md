Update a file with proper versioning. The user will specify which file(s) to update and what changes to make.

## Versioning standard

- **Filename version:** `vX.Y` (two-part only). Skip `.0` on initial major versions (`v1`, `v2`). Add minor once bumped (`v2.1`).
- **Metadata line:** `> Created: [Full Month] [Day], [Year] | Updated: [Full Month] [Day], [Year]` (e.g., `> Created: March 2, 2026 | Updated: March 3, 2026`)
- **H1 title:** NO version number — version lives in the filename only.
- **Internal `Version:` lines:** Do NOT add. Redundant with filename.

## Steps

1. **Identify the file** — The user will provide the file path or name. If ambiguous, list matching files and ask.

2. **Determine version bump type** — Ask the user if not clear from context:
   - **Major** (e.g., v1 → v2, v2.1 → v3) — structural changes, new approach, option switch
   - **Minor** (e.g., v1 → v1.1, v1.2 → v1.3, v2 → v2.1) — logic tweaks, corrections, small additions

3. **Create the new version file** in the SAME directory as the original:
   - Copy the file content
   - Update the version in the filename (two-part format: `vX.Y`)
     - Major bump: `v1` → `v2`, `v2.1` → `v3`
     - Minor bump: `v1` → `v1.1`, `v1.2` → `v1.3`, `v2` → `v2.1`
   - Keep the H1 title as-is (no version in title)
   - Update the `Updated:` date in the metadata line to today ($CURRENT_DATE), keep `Created:` unchanged

4. **Apply the requested changes** to the new version file. Make ONLY the changes the user described.

5. **Move the old version to Archive** — Move (not copy) the previous version file to the `/Archive/` directory at the project root. Use `git mv` if the file is tracked by git.

6. **Summary** — Show the user:
   - Old file: `<path>` → moved to `Archive/<filename>`
   - New file: `<path>` (with version and changes listed)
   - Remind the user to review the new file before committing

## Important
- Do NOT commit automatically. The user will review first.
- If multiple files need updating together (e.g., Qualification framework + Decision tree + Handoff rules), process them all in one go with the same version bump logic.
- Preserve all formatting, structure, and content that isn't being intentionally changed.
- The project root is: /Users/katerynakaminska/sales-ai-agent-documentation
- The archive directory is: /Users/katerynakaminska/sales-ai-agent-documentation/Archive

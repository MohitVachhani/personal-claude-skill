---
name: mr-writeup
description: Generate a concise MR title and structured MR description with feature-wise changes, code logic summary, bug fixes, and DB/schema notes. Use when the user wants merge request or pull request writeup.
---

# MR Writeup Generator

Generate a merge request writeup in the following format.

## Output format

Title: <short MR title>

Description:

### Changes done feature wise
#### Frontend
- <bullet points only if frontend changes exist>

#### Backend
- <bullet points only if backend changes exist>

### Code changes done
- <high-level logic changes only>
- <no deep implementation details>

### Bug fix
- <mention bug fixed, only if applicable>
- <mention how to reproduce the bug, only if applicable>

### DB / Schema changes
- <mention schema/data model changes, only if applicable>

### Auth / Permissions changes
- <mention auth/access-related changes, only if applicable>

### Testing / Follow-up
- <mention testing done or important follow-up, only if applicable>

## Style rules
- Keep bullet points short and review-friendly
- A bullet should not exceed 2 lines
- If more detail is needed, use sub-bullets
- Mention frontend and backend separately if both changed
- Mention DB/schema changes explicitly
- Do not include API curl examples
- Avoid generic bullets like "improved code quality"
- Avoid hype words
- Keep it practical and concrete
- Do not invent changes that are not supported by diff/context

## Title rules
- Prefer maximum 10 words
- Title should describe the main change clearly
- Use implementation-focused wording
- Prefer present tense

## Description rules
- Only include sections that are relevant
- Group changes naturally
- In "Changes done feature wise", describe user-facing or module-level changes
- In "Code changes done", describe logic changes at a high level
- In "Bug fix", mention:
  - what was broken
  - how it could be reproduced
  - what changed to fix it
- In "DB / Schema changes", mention table/column/index/migration/model updates
- In "Auth / Permissions changes", mention role checks, token flow, auth integration, or access rules
- In "Testing / Follow-up", mention validation or known follow-up only if useful

## Preferred workflow

Before generating the MR writeup:

1. Ask the user for the base branch of the MR
   - Example: "What is the base branch against which this MR is being created?"
   - Default is often `dev`, but confirm with user if not provided

2. Identify the current branch

3. Collect code differences using git in the following priority:

   a) Unstaged changes (working directory):
      - `git diff`

   b) Staged changes:
      - `git diff --cached`

   c) Committed changes vs base branch:
      - `git diff <base-branch>...HEAD`
      - `git diff --stat <base-branch>...HEAD`
      - `git diff --name-only <base-branch>...HEAD`

4. Combine all relevant diffs to understand the full scope of changes

5. Use this combined diff as the primary context

6. From the diff, infer:
   - product/feature-level changes
   - frontend changes
   - backend changes
   - logic/code changes
   - bug fixes
   - DB/schema changes
   - auth/permission changes

7. If the diff is not enough to clearly infer intent:
   - ask the user what logic change was made
   - ask only for missing context, not obvious changes

## Context priority

Use context in this order:

1. Unstaged changes (`git diff`)
2. Staged changes (`git diff --cached`)
3. Diff vs base branch (`git diff <base-branch>...HEAD`)
4. Changed file names
5. User-provided summary
6. User clarification on business logic (only if needed)

If multiple sources are available, combine them.
Prefer real diff over user description.
Do not over-explain.

## Interpretation guidance
- Treat git diff as the source of truth for code-level changes
- Convert code changes into review-friendly business or feature language where possible
- Do not simply restate filenames
- Do not dump low-level implementation details unless needed for clarity
- If a bug fix is present, explain it in terms of:
  - failing scenario
  - reproduction path
  - fix summary
- If DB/schema changes are present, mention them explicitly even if small
- If there are no changes for a section, omit that section

## Example output

Title: Add connector catalog and connection state

Description:

### Changes done feature wise
#### Frontend
- Added live connector catalog on the Tools page
- Added connected state, search, and category-based filtering

#### Backend
- Integrated connector listing from deployment service
- Returned tenant-specific connected integrations in API response

### Code changes done
- Replaced static connector data with backend-driven listing
- Mapped deployment service response into dashboard-friendly connector cards

### Auth / Permissions changes
- Routed connector actions through the platform service account flow

### Testing / Follow-up
- Verified connected connectors render correctly without page reload

If no diff is found, inform the user and ask for changes summary instead of generating empty output.

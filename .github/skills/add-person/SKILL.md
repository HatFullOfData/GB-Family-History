---
name: add-person
description: 'Add a new person record to the family history site by asking guided questions, accepting blank unknowns, resolving relationship IDs or names/paths where possible, generating a unique id, and creating content/people/<slug>/index.md in the repo schema.'
argument-hint: 'Optional: provide a known full name, id, or notes; leave empty for fully guided mode'
user-invocable: true
---

# Add Person

Create a new person page in this repository using a question-driven workflow that tolerates unknown information.

## When to Use
- User asks to add a new person.
- User has partial data and wants prompts for missing details.
- User wants unknown fields left blank instead of guessed.

## Inputs
- Optional argument: free text notes or a known name.
- Interview answers collected with `vscode_askQuestions`.

## Procedure
1. Confirm schema and existing data.
- Read `docs/schema.md` and one existing person file in `content/people/**/index.md`.
- Search existing ids and slugs before creating anything.

2. Run a guided interview with blank-friendly questions.
- Use `vscode_askQuestions` with `allowFreeformInput: true` for all prompts.
- Include explicit guidance in each prompt: `Leave blank if unknown`.
- Ask in this order:
  1. Full name (or title)
  2. Given name
  3. Middle names
  4. Surname
  5. Known as / nickname
  6. Summary sentence
  7. Birth date (ISO YYYY-MM-DD, optional)
  8. Birth display (for humans)
  9. Birth place
  10. Death date (ISO, optional)
  11. Death display
  12. Death place
  13. Parent ids (comma-separated)
  14. Spouse ids (comma-separated)
  15. Body text paragraph

3. Decide required follow-up questions.
- If title is blank but names exist, derive title from names.
- If given name or surname is blank after one follow-up, continue by creating a draft with placeholders.
- Placeholder policy for required fields only: `given_name: "Unknown"`, `surname: "Unknown"`.
- Never invent factual details such as dates, places, or relationships.

4. Build slug and id.
- Slug source: title, else given + surname.
- Slug format: lowercase, hyphen-separated ASCII.
- Suggested id pattern: `P-<SURNAME3>-<YYYYMMDD>-<NN>`.
- If birth date missing, use `UNK` in id date block.
- Ensure id uniqueness by checking existing person ids. Increment `NN` as needed.

5. Build frontmatter using repository schema.
- Include at minimum: `id`, `title`, `given_name`, `surname`, `birth.display`, `parents`, `spouses`.
- Prefer full template from `docs/schema.md` with optional fields left blank.
- `parents` and `spouses` must be arrays.
- For parent/spouse inputs, accept ids, names, or paths.
- If names/paths are provided, attempt to resolve to `person_id` by scanning existing people files.
- For spouse entries, include `person_id`. Add `person` path only if confidently resolvable.

6. Create file.
- Write file to `content/people/<slug>/index.md`.
- If slug exists, disambiguate slug (for example with birth year).
- Preserve project style and YAML formatting.

7. Validate before finishing.
- Re-read created file to ensure YAML is valid.
- Confirm id is unique and required fields are present.
- If relationship inputs do not resolve, keep those entries unlinked and report unresolved values in the final message.

8. Body text fallback.
- If body text is blank, auto-generate one neutral sentence from known facts only.
- Preferred pattern: `<Title> appears in the family history archive.`
- If birth display exists, optional extension: `<Title> appears in the family history archive, with a recorded birth of <birth.display>.`

## Quality Checks
- No fabricated dates, places, or relationships.
- Unknown optional values remain blank strings.
- Required missing names may use `Unknown` placeholders for draft creation.
- New record uses repo schema from `docs/schema.md`.
- File path follows `content/people/<slug>/index.md`.

## Output
- A created person file and a brief summary of:
  - chosen slug
  - generated id
  - unresolved relationship ids (if any)

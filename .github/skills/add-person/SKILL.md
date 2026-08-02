---
name: add-person
description: "Use when adding people or relatives in this Hugo family history repo: parents, spouses, and children via child pages."
argument-hint: "person name and known relatives"
---

# Add Person

Use this skill when the user wants to add a person, add relatives to a person, or connect parents, spouses, or children in this Hugo family history site.

## Repo Model

- Person pages live at `content/people/<slug>/index.md`.
- Person front matter is YAML between `---` markers.
- Stable identity is `id`; paths are readable links and may change.
- Preferred relationship links use `person_id`; keep `person` path links when useful for compatibility with existing content.
- Parent relationships are stored on the child page in `parents`.
- Children are not stored as a `children` array. Adding a child means creating or editing that child person's page so its `parents` array includes the parent. The site derives children by scanning people whose `parents` include the current person's `id` or `person` path.
- Spouse relationships are stored in `spouses` and should usually be reciprocal on both person pages.

## Workflow

1. Clarify the intended change.
   - Identify the focus person.
   - For each parent, spouse, or child, determine whether the person already exists or needs a new page.
   - Ask only for facts needed to make a correct structured edit: full name, birth date or display date, surname, living status, relationship status, marriage facts, and whether narrative body text is wanted.
2. Resolve existing people before creating new pages.
   - Search `content/people/**/index.md` for matching `title`, `given_name`, `surname`, `known_as`, and `id`.
   - Reuse an existing page if it clearly matches.
   - If there are ambiguous matches, ask the user which person is intended.
3. Create new person pages when needed.
   - Use slug style `content/people/<given-name-surname>/index.md`.
   - If the name is duplicated, disambiguate with a birth year or another clear suffix.
   - Generate IDs as `P-<SURNAME3>-<YYYYMMDD>-<NN>` when birth date is known, or `P-<SURNAME3>-UNK-<NN>` when unknown.
   - Ensure the new `id` is unique before writing the page.
   - Start from `archetypes/person.md` but include `id`, `title`, `summary`, `given_name`, `surname`, `living`, `birth`, `death` when known, `parents`, `spouses`, and `events` as appropriate.
4. Add parent links.
   - Edit the child person's `parents` array.
   - Use entries like:

```yaml
parents:
  - person_id: "P-EXA-19000101-01"
    person: "/people/example-parent/"
```

5. Add children.
   - Do not add a `children` field.
   - A child relationship is represented by a child person page that refers to the parent.
   - For an existing child, edit the child page and add the focus person to that child's `parents` array.
   - If the child is new, create the child page with its `parents` already set.
6. Add spouse links.
   - Add one `spouses` entry per spouse relationship.
   - Preserve any existing spouse entries.
   - Add the reciprocal spouse entry to the other person's page unless the user explicitly asks for a one-sided draft.
   - Include `status`, `marriage`, and `end` details only when known.

```yaml
spouses:
  - person_id: "P-EXA-19000101-01"
    person: "/people/example-spouse/"
    status: "married"
    marriage:
      date: "1920-04-09"
      display: "9 April 1920"
      place: "Example Place"
      location: "/locations/example-place/"
```

7. Keep prose conservative.
   - Do not add narrative body text unless the user asks for it.
   - Prefer structured front matter for relationship work.
   - If body text is requested, keep it short and factual.
8. Validate.
   - Run `hugo -D` after edits.
   - Check for duplicate IDs and unresolved relationship IDs if the change involved new or modified relationships.
   - If Hugo reports unrelated pre-existing warnings, mention them separately and do not rewrite unrelated content.

## Useful References

- Person schema: `docs/schema.md`
- Person archetype: `archetypes/person.md`
- Relationship behavior: `layouts/partials/child-pages.html`, `layouts/partials/spouse-list.html`
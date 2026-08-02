# Family History Person Schema

This document defines the frontmatter schema for person records in `content/people/**/index.md`.

## Design principles

1. **Stable identity is `id`**  
   - `id` is required, unique, and immutable.
   - Paths/slugs are for readability only and may change.

2. **Relationships use IDs**  
   - Link people with `person_id` (not name text).
   - Optional `person` path may be kept temporarily for backward compatibility.

3. **Human-readable + machine-sortable dates**  
   - Use ISO date fields (`YYYY-MM-DD`) for sorting/filtering.
   - Use `display` fields for narrative text.

---

## Required fields (minimum)

```yaml
id: "P-GRB-19681112-01"
title: "Laura Graham-Brown"
given_name: "Laura"
surname: "Graham-Brown"

birth:
  display: "12 November 1968"

parents: []
spouses: []
```

---

## Recommended full person template

```yaml
id: "P-GRB-19681112-01"
title: "Laura Graham-Brown"
summary: "Laura Graham-Brown was born in Lusaka, Zambia."

given_name: "Laura"
middle_names: ""
surname: "Graham-Brown"
known_as: ""

birth:
  date: "1968-11-12"          # ISO optional but recommended
  display: "12 November 1968"
  place: "Lusaka, Zambia"      # display text
  location: "/locations/lusaka/" # optional link to location page

living: true                    # set true for living people; summary will show present

death:
  date: ""                    # ISO optional
  display: ""
   place: "Lusaka, Zambia"      # display text
   location: "/locations/lusaka/" # optional link to location page
  location: ""                 # optional link to location page

parents:
  - person_id: "P-EXM-19400101-01"
   place: ""                    # display text
   location: ""                 # optional link to location page

spouses:
  - person_id: "P-GRB-19631125-01"
    person: "/people/george-graham-brown/"  # optional compatibility field
    status: "married"                 # married | divorced | widowed | separated | partnered
    marriage:
      date: "1994-04-09"              # ISO optional but recommended
      display: "9 April 1994"
      place: "Stowe School, Buckingham" # display text
      location: "/locations/stowe-school/" # optional link to location page
    end:
      date: ""                        # optional ISO
      reason: ""                      # divorce | death | separation
      display: ""                     # optional human text

events:
  - type: "lived"                     # lived | worked | baptized | buried | residence | occupation | other
    date: ""                          # optional ISO date
    display: ""                       # optional human date/range, e.g. "about 1901" or "1912-1918"
    place: ""                         # display text
    location: "/locations/example-place/" # optional link to location page
    description: ""                   # optional note shown on the location page
```

---

## Location-linked events

Location pages show an **Events** section by scanning people records for location links.

Use readable `place` text for display, and add a `location` path when there is a matching page under `content/locations/**/index.md`.

Built-in event fields:

```yaml
birth:
  date: "1870-02-12"
  display: "12 February 1870"
  place: "Leeds, Yorkshire"
  location: "/locations/leeds/"

death:
  date: "1941-09-03"
  display: "3 September 1941"
  place: "Leeds, Yorkshire"
  location: "/locations/leeds/"
```

Marriage locations live inside the relevant spouse entry:

```yaml
spouses:
  - person_id: "P-EXM-18700101-01"
    status: "married"
    marriage:
      date: "1894-04-09"
      display: "9 April 1894"
      place: "Stowe School, Buckingham"
      location: "/locations/stowe-school/"
```

Use `events` for other place-linked facts:

```yaml
events:
  - type: "worked"
    display: "about 1901"
    place: "Leeds, Yorkshire"
    location: "/locations/leeds/"
    description: "Worked as a blacksmith."

  - type: "lived"
    display: "1911"
    place: "Leeds, Yorkshire"
    location: "/locations/leeds/"
    description: "Recorded there in the 1911 census."
```

---

## Spouse relationships (multiple over lifetime)

`spouses` is an array.  
Add **one entry per spouse relationship**, and do not overwrite prior entries.

Example with two spouses:

```yaml
spouses:
  - person_id: "P-AAA-19600101-01"
    status: "divorced"
    marriage:
      date: "1988-05-14"
      display: "14 May 1988"
      place: "Leeds, England"
      location: "/locations/leeds/"
    end:
      date: "1996-09-01"
      reason: "divorce"
      display: "September 1996"

  - person_id: "P-BBB-19720202-01"
    status: "married"
    marriage:
      date: "2001-07-21"
      display: "21 July 2001"
      place: "Durham, England"
      location: "/locations/durham/"
```

---

## ID format

Recommended pattern:

`P-<SURNAME3>-<YYYYMMDD>-<NN>`

- `SURNAME3`: first 3 letters of surname (uppercase, ASCII)
- `YYYYMMDD`: date of birth if known
- `NN`: sequence number for collisions

Examples:
- `P-GRB-19681112-01`
- `P-GRB-19631125-01`
- Unknown DOB: `P-GRB-UNK-01`

Rules:
- IDs must be unique across repo.
- IDs must never be reassigned.
- If a slug/path changes, `id` stays the same.

---

## Slug/path policy

Current style is fine:
- `content/people/laura-graham-brown/index.md`

If duplicate names appear, disambiguate slug:
- `content/people/george-graham-brown-1963/index.md`
- `content/people/george-graham-brown-1898/index.md`

Slug is not identity; `id` is.

---

## Validation rules (recommended)

1. Every person has a non-empty `id`.
2. No duplicate `id` values.
3. Every `person_id` in relationships resolves to exactly one person.
4. `spouses` and `parents` are arrays (not strings/objects).
5. If `birth.date` exists, it is valid ISO `YYYY-MM-DD`.

---

## Migration strategy

1. Add `id` to all existing person files.
2. Add `person_id` to relationship entries.
3. Keep legacy `person` path links during transition.
4. Update templates/build logic to resolve by `person_id` first.
5. Optionally remove legacy `person` fields later.

# GB-Family-History

Production-oriented Hugo architecture for a family history site.

## Content model

Use `content/` for publishable entities that need their own URL and human-written narrative:

- `content/people/`
- `content/locations/`
- `content/stories/`
- `content/sources/`

Each content file should keep only canonical, page-owned fields plus markdown body content.

### Canonical ownership

- `people` own identity, vital facts, and biography
- `locations` own canonical place description, hierarchy, and coordinates
- `stories` own event participation, places involved, and sources cited
- `sources` own citation metadata and source notes

Avoid manually maintaining both sides of relationships. Derived views should be rendered from the canonical source at build time.

## Data files

Use `data/` for structured reference data that does not need its own permalink:

- controlled vocabularies
- normalization maps
- shared labels

Current example:

- `/home/runner/work/GB-Family-History/GB-Family-History/data/vocab/place-types.yaml`

## Taxonomies

Use taxonomies for browse and classification pages, not for domain relationships.

Current taxonomies:

- `surname`
- `tag`
- `place_type`

## Layout architecture

Use partials for shared rendering concerns:

- entity headers
- metadata lists
- taxonomy chips
- canonical relationship lists
- derived backlink lists

Use shortcodes only for editor-authored narrative embeds inside markdown content:

- `person`
- `location`
- `source`
- `callout`
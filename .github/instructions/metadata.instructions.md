---
applyTo: "_data/*.csv"
---

# CollectionBuilder-CSV Data File Instructions

These files drive the entire site — never edit Liquid templates or layouts to accomplish what a config CSV can do.

## Metadata CSV (e.g. `demo-metadata.csv`, `demo-compoundobjects-metadata.csv`)

The active metadata file is pointed to by `metadata:` in `_config.yml` (no `.csv` extension). Each row = one collection item. Item pages are auto-generated from this file by `_plugins/cb_page_gen.rb`.

### Required Columns

| Column | Rules |
|--------|-------|
| `objectid` | Unique, lowercase, no spaces. Becomes the item page filename (`items/<objectid>.html`). Used in feature includes: `{% include feature/image.html objectid="demo_001" %}` |
| `display_template` | Layout selector — must be exactly one of: `image` `video` `pdf` `audio` `record` `compound_object` `panorama` `multiple`. No `item/` prefix. |
| `title` | Required for meaningful item pages |

### Object File Columns

| Column | Purpose |
|--------|---------|
| `object_location` | URL or path to the primary file (image, PDF, audio, video) |
| `image_small` | Mid-size image URL/path — used on browse cards and map popups |
| `image_thumb` | Thumbnail URL/path — used in galleries, featured item, timeline |
| `image_alt_text` | Accessible alt text for screen readers |
| `object_transcript` | Full transcript text (audio/video) — used for search indexing |

### Compound Object Columns

| Column | Purpose |
|--------|---------|
| `parentid` | Set on child items — must exactly match the parent row's `objectid`. Rows with a non-empty `parentid` do **not** get their own item page. |

Compound objects require a **parent row** and one or more **child rows**. The `parentid` column must be present in the CSV for any compound objects to work.

**Parent row rules:**
- `objectid`: required, unique, follows normal naming rules
- `parentid`: leave **empty**
- `display_template`: must be `compound_object` or `multiple` (see below)
- `image_small` / `image_thumb`: required — these images represent the item on browse cards, the map, timeline, and all other visualizations
- All other metadata describes the item as a whole

**Child row rules:**
- `objectid`: required and unique (used internally even though no item page is generated)
- `parentid`: must exactly match the parent's `objectid`
- `display_template`: set to the child's own media type (`image`, `video`, `pdf`, etc.) — this controls how the child renders inside its modal
- Metadata fields describe the individual child file

**`compound_object` vs `multiple`:**

| Template | Use when | Child behavior |
|----------|----------|----------------|
| `compound_object` | Children are any mix of media types, or need full individual metadata | Each child opens in a browsable modal with its own metadata |
| `multiple` | All children are images with minimal per-image metadata | Children display as a vertical scroll of zoomable images; only `title` is shown per child |

### Geo Columns

| Column | Format |
|--------|--------|
| `latitude` | Decimal degrees (e.g. `46.725562`) |
| `longitude` | Decimal degrees (e.g. `-117.009633`) |

### Multivalued Fields

Use semicolons (`;`) to separate multiple values: `universities; buildings; campuses`  
These are split and counted by Liquid filters on subjects, locations, and cloud pages.

### Metadata Field Notes

Any column can be referenced from `config-metadata.csv` to display on item pages. Columns added here must also be added to `config-metadata.csv` (and optionally `config-browse.csv`, `config-search.csv`) to appear anywhere on the site.

---

## `config-metadata.csv` — Item Page Field Display

Controls which metadata columns appear on **item pages** and how.

```
field,display_name,browse_link,external_link,dc_map,schema_map
```

| Column | Values/Rules |
|--------|-------------|
| `field` | Must exactly match a column name in the metadata CSV (case-sensitive) |
| `display_name` | Label shown on item page. Leave blank → field is not shown as a labeled row |
| `browse_link` | `true` → value becomes a clickable link to browse search results for that value |
| `external_link` | `true` → value rendered as a `<a href>` hyperlink |
| `dc_map` | Dublin Core mapping (e.g. `DCTERMS.title`) — used for OG/meta on production |
| `schema_map` | Schema.org property name — used for OG/meta on production |

Row order = display order on item page.

---

## `config-browse.csv` — Browse Page Card Display

Controls fields on browse cards, facets, and sort options.

```
field,display_name,btn,hidden,sort_name,facet_name
```

| Column | Values/Rules |
|--------|-------------|
| `field` | Must exactly match a metadata CSV column name |
| `display_name` | Label on card. Blank = uses field name |
| `btn` | `true` → values rendered as clickable badge buttons |
| `hidden` | `true` → field loaded for search/sort but not visibly rendered on card |
| `sort_name` | Custom label in the sort dropdown. Blank = field excluded from sort |
| `facet_name` | Label for the faceted search sidebar panel. Requires `advanced-search: true` in `_data/theme.yml` |

---

## `config-search.csv` — Lunr.js Search Index

Defines which fields are indexed and shown in search results.

```
field,index,display
```

| Column | Values/Rules |
|--------|-------------|
| `field` | Must match a metadata CSV column name |
| `index` | `true` → field content added to the client-side Lunr.js search index |
| `display` | `true` → field value shown in the search result snippet |

---

## `config-nav.csv` — Navigation Links

Drives both the top navbar and the footer navigation.

```
display_name,stub,dropdown_parent
```

| Column | Values/Rules |
|--------|-------------|
| `display_name` | Link text. Must be unique if used as a `dropdown_parent` target |
| `stub` | Relative URL or absolute URL. Leave blank on a dropdown parent row |
| `dropdown_parent` | Set to exactly match another row's `display_name` to nest this row under a dropdown |

To create a dropdown: one parent row (no `stub`) + one or more child rows with `dropdown_parent` matching the parent's `display_name`.

---

## `config-map.csv` — Map Popup Fields

Controls which metadata fields appear in Leaflet map marker popups.

```
field,display_name,search
```

| Column | Values/Rules |
|--------|-------------|
| `field` | Metadata CSV column name |
| `display_name` | Label in popup |
| `search` | `true` → field is searchable via the map search box |

---

## `config-table.csv` — Data Page Table Columns

Controls which columns appear in the DataTables table on `/data.html`.

```
field,display_name
```

Row order = column order in table.

---

## `config-theme-colors.csv` — Bootstrap Color Overrides

Overrides Bootstrap 5 semantic colors for `btn-*`, `bg-*`, `text-*` classes.

```
color_class,color
primary,#17a2b8
secondary,
```

| Column | Values/Rules |
|--------|-------------|
| `color_class` | Bootstrap color name: `primary` `secondary` `success` `info` `warning` `danger` `light` `dark` |
| `color` | Any CSS color value (`#hex`, `rgb()`, `hsl()`). Leave blank to keep Bootstrap default |

Text contrast is automatically calculated to meet WCAG 4.5:1 — do not override button text color separately.

---

## Cross-File Relationships

```
_data/<metadata>.csv           ← source of truth for collection items
  ↓ field names referenced by ↓
config-metadata.csv            → which fields appear on item pages
config-browse.csv              → which fields appear on browse cards / facets / sort
config-search.csv              → which fields are in the Lunr.js index
config-map.csv                 → which fields appear in map popups
config-table.csv               → which fields appear in the data table
```

When you add a new metadata column, decide which of the config-*.csv files should reference it.

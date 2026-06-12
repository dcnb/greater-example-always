---
name: collectionbuilder-csv
description: "Work with CollectionBuilder-CSV Jekyll sites. Use when: adding collection items or metadata, customizing page layouts, building about pages with feature includes, configuring browse/search/map/timeline, changing visual theme, adding navigation, editing SCSS, or troubleshooting why a page looks wrong. Covers the full data-driven architecture: config-*.csv files, theme.yml, _includes/feature/ system, SASS theming, and item page generation."
argument-hint: "Describe the task, e.g. 'add a gallery to the about page' or 'customize the browse facets'"
---

# CollectionBuilder-CSV Skill

## What This Skill Covers

End-to-end guidance for customizing and developing CollectionBuilder-CSV sites. CB-CSV is a Jekyll static site generator where **all major UI surfaces are configured through data files** — not by editing HTML. Understanding this layered architecture is the key to working effectively.

## Customization Priority Order (Always Check Top-Down)

```
1. _config.yml              → site identity, metadata CSV pointer
2. _data/theme.yml          → visual/behavioral settings
3. _data/config-*.csv       → field-level UI surface configuration
4. pages/*.md + feature/    → authored page content
5. _sass/_custom.scss        → CSS overrides only when layers 1–4 can't do it
```

Never skip to layer 5 (CSS) when a higher layer can solve the problem.

---

## Common Task Workflows

### Adding/Editing Navigation Links

Edit `_data/config-nav.csv` — never touch `_includes/collection-nav.html`.

```csv
display_name,stub,dropdown_parent
Home,/,
Browse,/browse.html,
About,/about.html,
Resources,,               ← parent row: no stub = dropdown header
External Site,https://example.com,Resources
```

`dropdown_parent` value must exactly match the parent's `display_name`. The same CSV drives both the top navbar and the footer.

---

### Adding Rich Content to About/Content Pages

Use `_includes/feature/` includes. Full reference in `_includes/cb/feature_options.md`.

#### Images
```liquid
{% include feature/image.html objectid="demo_001" %}
{% include feature/image.html objectid="demo_001" width="75" caption="Custom caption" %}
{% include feature/image.html objectid="demo_001;demo_002;demo_003" %}
{% include feature/image.html objectid="demo_001" caption=false %}
{% include feature/image.html objectid="https://example.com/img.jpg" alt="Description" %}
```
`width` values: `25` `50` `75` `100`. Multiple objectids (`;` separated) create equal-width columns.

#### Cards
```liquid
{% include feature/card.html header="Title" text="**Markdown** body" objectid="demo_004" %}
{% include feature/card.html header="Title" text="Text" width="50" centered="true" %}
{% include feature/card.html header="Title" text="Text" objectid="demo_004" float="end" width="25" %}
```
`float` values: `start` `end`

#### Galleries
```liquid
{% include feature/gallery.html %}
{% include feature/gallery.html filter-field="subject" filter-value="Mining" %}
{% include feature/gallery.html filter="item.subject contains 'mining'" %}
{% include feature/gallery.html item-size="small" heading="Postcards" %}
{% include feature/gallery.html gallery-type="video" heading="Videos" %}
{% include feature/gallery.html image-heading="demo_008" heading="Gallery Title" %}
```
`item-size` values: `tiny` (6/row) `small` (4/row) `medium` (3/row, default) `large` (2/row).  
**Note:** `filter` values are **case-sensitive**. `filter` param must start with `item.`

#### Hero Jumbotron
```liquid
{% include feature/jumbotron.html %}
{% include feature/jumbotron.html objectid="demo_001" heading="My Heading" text="Subtitle" %}
{% include feature/jumbotron.html objectid="demo_001" heading=false text=false padding="10em" %}
{% include feature/jumbotron.html objectid="demo_001" position="top" %}
```
`position` values: `center` `top` `bottom`

#### Alert, Button, Blockquote, Accordion, Collapse
```liquid
{% include feature/alert.html text="**Warning** message" color="warning" align="center" %}
{% include feature/button.html text="Browse" link="/browse.html" color="primary" size="lg" centered=true %}
{% include feature/blockquote.html text="Quote text" speaker="Author" source="Work" link="https://..." %}

{% capture col1 %}
Content with **markdown** here.
{% endcapture %}
{% include feature/accordion.html title1="Section One" text1=col1 title2="Section Two" text2="Simple text" open=true %}

{% capture hidden %}
Hidden **content** here.
{% endcapture %}
{% include feature/collapse.html button="Read More" text=hidden color="outline-primary" %}
```

#### Video, Audio, Cloud, Mini-Map
```liquid
{% include feature/video.html objectid="demo_003" width="75" %}
{% include feature/video.html objectid="https://youtu.be/VIDEOID" caption="Title" %}
{% include feature/audio.html objectid="demo_007" %}
{% include feature/cloud.html fields="subject;creator" min=2 %}
{% include feature/mini-map.html objectid="demo_001" %}
```

---

### Configuring Item Pages (What Fields Display)

Edit `_data/config-metadata.csv`. Do not edit `_includes/item/metadata.html`.

```csv
field,display_name,browse_link,external_link,dc_map,schema_map
title,Title,,,,
creator,Creator,true,,,
date,Date Created,,,,
subject,Subjects,true,,,
rightsstatement,,,,,
```

- `display_name` blank → field is not shown as a labeled row (but may still be used for metadata richness)
- `browse_link: true` → value becomes a clickable browse search link
- `external_link: true` → value rendered as a `<a href>` hyperlink
- Row order in the CSV = display order on the item page

---

### Configuring the Browse Page

Edit `_data/config-browse.csv`. Do not touch `_layouts/browse.html`.

```csv
field,display_name,btn,hidden,sort_name,facet_name
date,Date,,,Date Created,
description,,,true,,
creator,Creator,,,,
subject,,true,,, 
location,,true,,,Location Depicted
identifier,,,true,Identifier,
```

- `btn: true` → renders values as clickable badge buttons
- `hidden: true` → loaded for search/sort but not visibly displayed on card
- `sort_name` → custom label in the sort dropdown
- `facet_name` → label for the faceted search sidebar panel (requires `advanced-search: true` in `theme.yml`)

---

### Configuring Search (Lunr.js Index)

Edit `_data/config-search.csv`. This defines what goes into the client-side full-text index.

```csv
field,index,display
title,true,true
creator,true,false
description,true,true
subject,true,true
```

- `index: true` → field added to Lunr search index
- `display: true` → field shown in search result snippet

---

### Configuring the Map

Enable map features in `_data/theme.yml`:
```yaml
map-base: Esri_WorldStreetMap
map-search: true
map-cluster: true
auto-center-map: true
latitude: 46.727
longitude: -117.014
zoom-level: 5
```

Configure map popup fields in `_data/config-map.csv`:
```csv
field,display_name,search
date,Date,true
creator,Creator,true
subject,Subjects,true
```

---

### Changing Visual Theme

**Typography and colors via `_data/theme.yml`:**
```yaml
base-font-size: 1.0em
base-font-family: '"Source Sans Pro", sans-serif'
font-cdn: 'https://fonts.googleapis.com/css2?family=Source+Sans+Pro'
text-color: "#191919"
link-color: "#17a2b8"
```

**Navbar:**
```yaml
navbar-color: dark      # dark or light (controls text color)
navbar-background: bg-dark  # Bootstrap bg-* class
```

**Bootstrap semantic colors via `_data/config-theme-colors.csv`:**
```csv
color_class,color
primary,#17a2b8
secondary,#6c757d
```
Leave `color` blank to keep Bootstrap defaults.

**Custom Bootswatch theme** (replaces Bootstrap entirely):
```yaml
bootswatch: journal   # see https://bootswatch.com/ for available themes
```

**Custom CSS in `_sass/_custom.scss`:**
```scss
// Override or extend anything. This file wins specificity over all CB defaults.
.navbar-brand {
  font-weight: 700;
  letter-spacing: 0.05em;
}
```

---

### Adding a New Page

1. Create `pages/mypage.md` with front matter:
```yaml
---
title: My Page
layout: page
permalink: /mypage.html
---
```
2. Add a row to `_data/config-nav.csv` if it needs a nav link:
```csv
My Page,/mypage.html,
```
3. Write Markdown content with feature includes as needed.

For a narrow-column layout use `layout: page-narrow`. For full-width use `layout: page-full-width`.

---

### Understanding the Metadata CSV

The active metadata file is set by `metadata:` in `_config.yml` (no `.csv` extension). Key columns:

| Column | Purpose |
|--------|---------|
| `objectid` | Unique ID; used in feature includes and as the item page filename |
| `display_template` | Item page layout: `image` `video` `pdf` `audio` `record` `compound_object` `panorama` `multiple` |
| `object_location` | URL or path to the file |
| `image_small` | URL/path to a mid-size image (used in browse) |
| `image_thumb` | URL/path to a thumbnail (used in galleries, featured, etc.) |
| `parentid` | For compound objects — child items' `parentid` = parent's `objectid` |
| `title`, `creator`, `date`, etc. | Metadata fields; any added column automatically available for config |

Access in Liquid: `site.data[site.metadata]` (never `site.data.metadata` — the bracket notation resolves the variable).

---

### Item Page Auto-Generation

`_plugins/cb_page_gen.rb` generates item pages at build time. Key behaviors:
- Reads `_data/<metadata>.csv`, generates one page per row into `items/`
- Routes to `_layouts/item/<display_template>.html`
- Skips rows with a non-empty `parentid` column (child objects)
- The `display_template` value is e.g. `image` — the plugin prepends `item/` → `_layouts/item/image.html`

Never create `.html` files manually in `items/`. Never add `item/` prefix in the `display_template` column.

---

## SCSS Architecture

```
assets/css/cb.scss        ← Compiled entry point (has YAML front matter for Liquid processing)
  ├── _sass/_base.scss    ← Bootstrap overrides, layout defaults (DO NOT EDIT)
  ├── _sass/_pages.scss   ← Feature/page-specific styles (DO NOT EDIT)
  ├── _sass/_theme-colors.scss  ← Bootstrap color utilities (DO NOT EDIT)
  └── _sass/_custom.scss  ← YOUR customizations (ONLY file to edit)
```

`cb.scss` uses Jekyll/Liquid front matter (`---`) to compile `theme.yml` values and `config-theme-colors.csv` into SCSS variables at build time. This is why font/color changes in `theme.yml` take effect without touching SCSS.

---

## Troubleshooting Common Problems

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Nav link missing | Didn't add to CSV | Add row to `_data/config-nav.csv` |
| Field missing on item page | Not in config-metadata.csv | Add row with `field` and `display_name` |
| Browse not showing a field | Not in config-browse.csv | Add row |
| Custom CSS not working | Wrote CSS in wrong file | Move to `_sass/_custom.scss` |
| `metadata.csv` → 404 on build | `.csv` in config | Set `metadata: filename` (no extension) |
| Gallery images missing | Wrong objectid casing | objectid is case-sensitive; must match CSV exactly |
| Item page using wrong template | display_template typo | Check against valid values above; no `item/` prefix |
| Analytics not showing locally | Expected behavior | Analytics only run with `JEKYLL_ENV=production` |
| Bootstrap class not working | Using BS4 syntax | Use BS5: `ms-`/`me-`, `float-start`, `data-bs-toggle` |
| `site.data.metadata` returns nil | Dot notation | Use `site.data[site.metadata]` with brackets |
| Dropdown nav not working | Missing parent row | Create parent row with no `stub`, children set `dropdown_parent` |

---

## Key Documentation Files in This Repo

| File | Covers |
|------|--------|
| `docs/markup.md` | All feature includes with full parameter reference |
| `docs/metadata.md` | Metadata CSV column definitions |
| `docs/color_theme.md` | Theme colors and Bootstrap theming |
| `docs/advanced_theme.md` | Fonts, Bootswatch, advanced SCSS |
| `docs/maps.md` | Map configuration options |
| `docs/gallery.md` | Gallery include options |
| `docs/tables.md` | Data page table configuration |
| `_includes/cb/feature_options.md` | Inline feature include docs |
| `_includes/cb/about_the_about.md` | About page structure guide |

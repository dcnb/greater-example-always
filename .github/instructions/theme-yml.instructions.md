---
applyTo: "_data/theme.yml"
---

# `_data/theme.yml` Instructions

This file controls all visual settings and behavioral feature toggles for the site. It is the **second stop** in the customization priority order (after `_config.yml`). Values here are consumed both by Jekyll/Liquid templates at build time and by `assets/css/cb.scss` as SCSS variables.

Do not add arbitrary keys — only keys recognized by the templates will have any effect.

---

## Home Page

```yaml
featured-image: demo_001       # objectid from metadata CSV OR relative image path OR full URL
featured-image-alt-text:       # required if using a URL (objectid alt text is auto-populated from metadata)
home-title-y-padding: 10em     # vertical space above the title in the banner
home-banner-image-position: center  # CSS background-position: center | top | bottom
```

---

## Browse Page

```yaml
advanced-search: true   # adds an "Advanced Search" modal with per-field search builders
faceted-search: true    # adds a left-sidebar facet panel (uses facet_name from config-browse.csv)
default-sort-field:     # must exactly match a `field` value in config-browse.csv; blank = random sort
```

To define facet labels and which fields are facetable, edit `_data/config-browse.csv`.

---

## Item Page

```yaml
browse-buttons: true    # adds previous/next arrows to item pages; increases build time on large collections
```

---

## Subjects Page

```yaml
subjects-fields: subject;creator   # semicolon-separated metadata field names to include in the cloud
subjects-min: 1                    # minimum occurrences for a term to appear (raise to reduce noise)
subjects-stopwords:                # semicolon-separated terms to exclude, e.g. "unknown;n/a"
```

Leave `subjects-fields` blank/commented to disable the Subjects page cloud.

---

## Locations Page

```yaml
locations-fields: location   # semicolon-separated fields for the locations cloud
locations-min: 1
locations-stopwords:
```

---

## Map Page

```yaml
auto-center-map: true          # auto-fit map view to all items; overrides lat/long/zoom when true
latitude: 46.727485            # manual center latitude (used when auto-center-map is false)
longitude: -117.014185         # manual center longitude
zoom-level: 5                  # 1 (world) to 18 (street level)
map-base: Esri_WorldStreetMap  # base tile layer — valid values:
                               #   Esri_WorldStreetMap | Esri_NatGeoWorldMap
                               #   Esri_WorldImagery | OpenStreetMap_Mapnik
map-search: true               # enables a search box on the map (not recommended for large collections)
map-search-fuzziness: 0.35     # 0 = exact match only, 1 = match anything
map-cluster: true              # clusters nearby pins (recommended for large collections)
map-cluster-radius: 25         # cluster radius in pixels (10–80)
```

Configure which fields appear in map popups in `_data/config-map.csv`.

---

## Timeline Page

```yaml
year-navigation: "1900;1910;1920;1930"   # manually specify year dropdown options
year-nav-increment: 10                   # OR auto-generate nav years at this interval
```

Use one or the other, not both. Leave both blank for no year navigation dropdown.

---

## Data Downloads

```yaml
metadata-export-fields: "title,creator,date,description,subject"  # comma-separated fields for CSV/JSON export
metadata-facets-fields: "subject,creator,format"                  # comma-separated fields for facets data download
```

---

## Compound Objects

Only relevant when your metadata CSV includes compound objects (rows with `parentid`).

```yaml
map-child-objects: true       # child objects with lat/long appear on map
timeline-child-objects: true  # child objects with date appear on timeline
data-child-objects: false     # child objects linked in data table
browse-child-objects: false   # child objects appear on browse page and populate clouds
search-child-objects: true    # child objects appear in search results
```

---

## Navbar Colors

Uses Bootstrap 5 color utilities. See https://getbootstrap.com/docs/5.3/components/navbar/

```yaml
navbar-color: dark        # text scheme: "dark" (white text) or "light" (dark text)
navbar-background: bg-black  # background class: bg-primary | bg-secondary | bg-dark | bg-light
                              # bg-white | bg-success | bg-danger | bg-warning | bg-info
```

For custom hex colors not in Bootstrap's palette, override in `_sass/_custom.scss`.

---

## Bootswatch Theme

Replaces the standard Bootstrap theme entirely.

```yaml
bootswatch: journal   # leave blank for default Bootstrap
```

Valid values: `cerulean` `cosmo` `cyborg` `darkly` `flatly` `journal` `litera` `lumen` `lux` `materia` `minty` `pulse` `sandstone` `simplex` `sketchy` `slate` `solar` `spacelab` `superhero` `united` `yeti`

When using Bootswatch, navbar and button colors come from the theme rather than Bootstrap defaults.

---

## Typography

These values are injected into SCSS as variables via `assets/css/cb.scss`.

```yaml
base-font-size: 1.2em
text-color: "#191919"
link-color: "#0d6efd"
base-font-family: "Roboto, sans-serif"   # comment out for Bootstrap default stack
font-cdn: '<link href="https://fonts.googleapis.com/css2?family=Roboto&display=swap" rel="stylesheet">'
```

`font-cdn` is injected verbatim into `<head>` — must be a valid `<link>` tag string.

---

## Icons

Override Bootstrap Icon names for item type indicators. Find icon names at https://icons.getbootstrap.com/

```yaml
icons:
  icon-image: image
  icon-audio: soundwave
  icon-video: film
  icon-pdf: file-pdf
  icon-record: file-text
  icon-panorama: image-alt
  icon-compound-object: collection
  icon-multiple: postcard
  icon-default: file-earmark    # fallback for unrecognized display_template
  icon-back-to-top: arrow-up-square
```

---

## What NOT to Do Here

- Do not add page-level content here — use page front matter or `_includes/feature/` includes
- Do not define metadata field display here — use `_data/config-*.csv` files
- Do not add custom CSS classes here — use `_sass/_custom.scss`
- Do not add Bootstrap semantic color values here — use `_data/config-theme-colors.csv`

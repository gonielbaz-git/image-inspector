# Image Inspector

A single HTML file for visual inspection of image sets. Open it in a browser, point it at
your images and (optionally) a catalog CSV, click through them with the keyboard, and export
your classifications as a CSV.

**[Open the tool →](https://gonielbaz-git.github.io/image-inspector/)** (or download `index.html` and open it offline)

No install, no server, no dependencies. Nothing is uploaded anywhere — the file reads your
images locally and saves progress in your browser's `localStorage`.

It was written for classifying galaxy cutouts (multi-band imaging from a survey), but nothing
in the tool is survey- or astronomy-specific: the image panels, the categories, and the
metadata columns are all defined in a config you can edit in the page itself.

## Quick start

1. Download [`index.html`](index.html) and open it in your browser (double-click is enough).
2. Open **Configuration**, edit the JSON — or load one of the [`examples/`](examples) configs —
   and press **Apply**.
3. Select your image files (one file picker per panel), optionally a catalog CSV, and press
   **Start Inspection**.
4. Classify with the number keys, navigate with `←`/`→`, and press **Export CSV** when done.

## How images are matched up

Each panel gets its own file selection, and files are grouped into objects by the ID taken
from the filename. So `12345_data.png` and `12345_model.png` land side by side as object
`12345`.

By default the ID is the filename with the extension and the panel's `suffix` stripped off.
If your names don't fit that shape, set `idRegex` and the first capture group becomes the ID:

```json
{ "idRegex": "^M(\\d+)_" }     // M98765_hscdr3.jpg  ->  98765
```

Objects missing an image in some panel are still shown, with that slot dimmed.

## Catalog CSV (optional)

Any CSV (or tab-separated file) with a column of object IDs. The ID column is found by name
from `idColumns`. Quoted fields containing commas are handled.

With `"metadata": "auto"` every other column is displayed. To choose and group the columns
yourself:

```json
"metadata": [
  { "label": "Magnitudes", "columns": [
    { "label": "g", "column": "mag_g" },
    { "label": "r", "column": "mag_r" }
  ]},
  { "label": "Colors", "columns": [{ "label": "g-r", "column": "g-r" }] }
]
```

Numeric values are rounded to `decimals` places (default 2); text is shown as-is.

## Config reference

| Key | Meaning |
| --- | --- |
| `title`, `subtitle` | Headings on the setup screen |
| `formTitle`, `categoryPrompt` | Headings above the category buttons |
| `panels` | One entry per image shown side by side: `key`, `label`, `suffix`, `hint` |
| `categories` | The choices, in keyboard order (keys `1`–`9`): `value`, `label` |
| `metadata` | `"auto"`, or explicit column groups (above) |
| `idRegex` | Optional regex; capture group 1 is the object ID |
| `idColumns` | Candidate ID column names in the catalog CSV |
| `storagePrefix` | Namespace for saved progress — give each dataset its own |
| `outputPrefix` | Filename stem for the exported CSV |
| `decimals` | Rounding for numeric catalog values |

`storagePrefix` is what keeps two datasets from overwriting each other's saved progress in the
same browser. Change it when you start a new project.

## Output

`Export CSV` writes one row per classified object:

```
id,classification,classification_label,comments,inspector,timestamp
```

The inspector name comes from the optional field on the setup screen, which makes collating
several people's files straightforward. To resume later — or to hand a partly-finished list to
someone else — load an exported CSV under *Resume from a previous classification CSV*.

## Keyboard shortcuts

| Key | Action |
| --- | --- |
| `←` / `→` | Previous / next object |
| `1`–`9` | Pick a category (saves immediately) |
| `Enter` | Save and advance |
| `U` | Jump to the next unclassified object |
| `Ctrl`/`Cmd`+`E` | Export CSV |

Clicking an image zooms it.

## Notes and limits

- Progress lives in the browser's `localStorage`, so it is per-browser and per-machine. Export
  early and often; clearing site data clears your progress.
- Images are read from your local disk each session — the browser can't remember file
  selections, so re-select the same folders when you come back. Saved classifications are
  matched up again by ID.
- Practical batch size is a few hundred to a few thousand objects per session.

## License

MIT — see [LICENSE](LICENSE).

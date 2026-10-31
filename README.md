# mapAnalyzer

A single-file, offline analyzer for Arm armlink MAP files. Open `index.html` in a browser — no install, no server, no network — and turn the linker's text report into an interactive view of memory layout, component sizes, symbols, object dependencies, removed sections and generated diagnostics. The interface is Simplified Chinese.

The demo MAP is embedded, so the page is useful before you have a file to open. Load a real one with **打开 MAP** or by dropping it anywhere on the page.

## Features

- **Overview** — ROM/RAM totals, per-region utilization, Flash/RAM composition, an address-ordered footprint strip, code and RAM top lists, and a severity-sorted insight preview.
- **Memory layout** — load and execution regions with base, size and max, plus every RO/RW/ZI input section. PAD rows are kept so region sizes can be reconciled; filter by region, section type and attribute.
- **Components** — object files, library members and library rollups with Code/RO/RW/ZI sizes and ROM/RAM totals. Code composition by category, largest components, and a drill-down drawer that links a component to its sections and symbols.
- **Symbols** — local and global symbols, defined, undefined and absolute, with value, size, section and owning object. Filter by scope, symbol type and definition state.
- **Dependencies** — object-level graph aggregated from `Section Cross References`, with hover highlighting, category filter, node limit, SCC-based cycle detection and a paginated edge table including the first few contributing symbols and section flow.
- **Removed** — every input section the linker removed, aggregated per object and compared against the current image size. Zero-byte sections are kept.
- **Insights** — diagnostics generated from utilization, composition, padding, stack/heap, symbols and edges, ordered by severity.
- **Cross-view** — global search over components, sections, symbols and removed sections; sortable, searchable, paginated tables; a details drawer for every entity; light/dark theme that follows the system on first run and is remembered afterwards; responsive layout for narrow screens.
- **Audit** — the overview compares the section-derived image size against the component totals and reports the delta, so a silently incomplete parse does not pass unnoticed.

## Getting started

1. Download or clone this repository.
2. Open `index.html` directly in a browser (`file://` is fine).
3. Click **打开 MAP**, or drag an `.map` file anywhere onto the page.
4. Use the embedded demo at any time by reloading without a file.

No build step, package manager or web server is involved.

## Input compatibility

mapAnalyzer parses the text report written by Arm armlink. It relies on these standard blocks:

- `Section Cross References`
- `Removing Unused input sections from the image`
- `Image Symbol Table` (`Local Symbols` / `Global Symbols`)
- `Memory Map of the image` (load regions, execution regions and PAD)
- `Image component sizes`

It was validated against Arm Compiler 6.24 `armlink` output. GNU ld `.map` files are not supported. A report that omits one of these blocks will simply lack the corresponding view.

Only these blocks are read; other report text is ignored. The consistency check in the overview only passes when the parsed section sizes add up to the reported component totals.

## Browser requirements

A current browser with `color-mix()` support: Chrome/Edge 111+, Firefox 113+, Safari 16.2+. There is no polyfill, transpiler or bundler.

## Exports

- **完整模型 (JSON)** — the complete derived model: metadata, regions, sections, components, symbols, references, removed sections, cycles and statistics.
- **组件用量 / 节区布局 / 符号表 / 已移除节区 (CSV)** — one table per file, ready for a spreadsheet.
- **复制文本摘要 (Clipboard)** — a compact text summary. If the clipboard API is unavailable (for example on an insecure origin), the same summary is downloaded as a `.txt` file instead.

## Keyboard shortcuts

| Key | Action |
| --- | --- |
| `Ctrl`/`Cmd` + `K`, `/` | Open global search |
| `Ctrl`/`Cmd` + `O` | Open a MAP file |
| `T` | Toggle light/dark theme |
| `1` … `7` | Switch view |
| `↑` / `↓`, `Enter` | Move through search results, open the selected one |
| `Esc` | Close the search, help, details or export layer |

## Privacy

Everything runs in the browser. The MAP file is read locally, never uploaded, and is not persisted. The only stored value is the theme preference in `localStorage`. There are no external scripts, fonts, images or analytics requests.

## Layout

```
index.html    standalone application: markup, styles, parser, UI and demo MAP inline
LICENSE       BSD-3-Clause
README.md
```

The single-file form is intentional: it can be copied to a machine without network access, opened from a USB stick, and kept under version control as one artifact. No external assets are referenced.

## Limits

- The analyzer reports what armlink reports. It does not read the ELF file, so it cannot recover information absent from the MAP (for example, symbol types that armlink did not print).
- Removed-section totals are the linker's own accounting. They are not guaranteed to equal the Flash saving after a rebuild, because removal can change alignment and placement.
- Parsing starts after the whole file has been read and runs synchronously; a very large report can block the page briefly while it is processed.
- Cycle detection excludes self-references; a self-loop is not treated as a dependency cycle.

## License

BSD-3-Clause. See [LICENSE](LICENSE).

# Utilities

Various utilities for everyone.

## Better Designs Workbench (v3.2)

The latest workbench is [`Better_Designs_Workbench_v3_2.html`](Better%20Designs%20Workbench/Better_Designs_Workbench_v3_2.html). It hosts three independent utilities in sandboxed iframes. Each session keeps its own state for all three utilities, so multiple files and reports can remain open in parallel. Switching utilities with the tabs or `Alt+1/2/3` preserves loaded files, searches, scroll positions, and filters.

### 1. gigaView — Large Text File Analyzer (`Alt+1`)

Open, browse, and search very large text files without loading them fully into memory. Files are read in slices from disk and are not uploaded.

**File handling**

- Open files with the Open button or drag and drop them into the view.
- Warns before opening a file containing NUL bytes and detects UTF-16 files (line splitting assumes UTF-8/ASCII).
- Builds the line index asynchronously, with checkpoints every 1,000 lines, so the viewer can be used while indexing continues.
- Displays the file name, size, and live line-count/size information.

**Navigation and display**

- Configurable paged view (10–5,000 lines per page; default 200), with First, Prev, Next, and Last controls.
- Go to a line number, a percentage (after indexing), or a relative offset (`+500`, `-100`). Jumps to lines not yet indexed are deferred.
- `Alt+←` / `Alt+→` changes pages; scrolling beyond a page flows to its neighbour.
- Shows the current line range and total line count when available.
- Font size controls (8–32 px), word wrap, synchronized light/dark theme, line numbers, and highlighted search hits.
- Click line numbers to toggle bookmarks; bookmarked lines are tinted and their numbers bolded.

**Search and side panel**

- Search supports match case (`Aa`), whole word (`W`), and regular expression (`.*`) options.
- Searches stream across the file with cancellable progress; the first hit appears immediately, followed by the full result list.
- Find, Previous match, Next match, and Stop controls; Enter / Shift+Enter moves through matches, as do `F3` / `Shift+F3`.
- `Ctrl+F` focuses search, `Ctrl+G` focuses Go to, and `Esc` clears search.
- Search history provides up to 30 entries. Regex length is capped at 2,000 characters, results at 200,000 matches; risky nested-quantifier patterns and zero-width-match loops are guarded.
- The side panel has four tabs:
  - **Results** — matching line numbers, snippets, and navigation.
  - **Bookmarks** — bookmarked lines with editable notes and removal controls.
  - **Saved** — save and rerun searches (including their options), delete searches, or erase stored history, saved searches, and bookmarks.
  - **Stats** — line count, file size, empty-line count, longest line (bytes and line number), average line length, and line-ending style (LF / CRLF / mixed).

**Export and persistence**

- Export a selected line range (for example, `100-250`) to a new `.txt` file using a slice of the original file. Uses `showSaveFilePicker` when available, with a download-link fallback.
- Preferences (theme, font size, wrapping, and lines per page), search history, saved searches, and bookmarks persist across reloads.
- History, saved searches, and bookmarks are keyed by a hash of the file name, size, and modification time. Bookmark preview text is not stored; it is read again from the open file after indexing.
- Stored data is validated on load, with values clamped and shapes whitelisted.

### 2. IB Error Log Analyzer — PeopleSoft Integration Broker Logs (`Alt+2`)

Load one or more PeopleSoft `errorLog.html` exports, extract structured fields for each error, and interactively filter, group, and inspect them.

**Ingestion and parsed data**

- Add multiple `.html`, `.htm`, or `.txt` files at once, or drag and drop multiple files.
- Files over 64 MB are skipped with a notification; duplicate files with the same name and size are skipped.
- Parses files with live progress. Individual record failures are counted and reported without preventing other records from loading.
- Files stack in a session, can be removed individually, and the dataset is re-aggregated.
- Extracts timestamps, date, hour, error level, type, description, exception, message-set/ID, message parameters, Java exception class, normalized error signature, operation and type, queue, requesting and destination nodes, connector class, target host, endpoint, HTTP method and response status, request/response body type, PeopleTools release, URL, response title, stack trace, request body, and response body.

**Overview, analysis, and errors**

- **Overview** KPI tiles show error totals, distinct signatures and endpoints, files loaded, and first/last timestamps.
- An SVG timeline automatically selects its bucket size (minute through day); clicking a bar zooms into that time range and adds a time filter.
- Shows attributes common to every error in the current view and six configurable breakdown cards (defaulting to signature, endpoint, operation, status, node, and file). Clicking a bar adds an include filter.
- **Analyze** groups and optionally splits by any field. Results show counts, percentages, first/last seen, and split-value heatmap columns. Click a row to filter; splitting by source file enables side-by-side log comparison. The top 150 groups are shown.
- **Errors** presents sortable Time, File, Error signature, Operation, Endpoint, and Status columns, paginated 100 per page. Clicking a row opens its details.

**Filters, search, and details**

- The left sidebar provides collapsible facets with counts and mini-bars. Click a value to include it; Shift+click to exclude it. Facets can expand to show up to 300 values.
- Active include/exclude and timeline filters appear as removable chips; Clear filters resets them.
- Free-text search supports AND-matched words, quoted exact phrases, excluded `-words`, and an optional case-insensitive regex mode. Regex input is capped at 2,000 characters, nested-quantifier patterns are rejected, and search is debounced by 250 ms.
- The detail drawer has **Fields** (with links to filter by a value), **Stack trace**, **Request**, and **Response** tabs. JSON request and response bodies are pretty-printed.
- Previous/Next moves through errors in the filtered view. The drawer also provides Close, Copy, and a secrets-masking toggle that masks or reveals authorization headers, password/token/secret parameters, and credential-like query parameters in both the view and copied output.

**Summary and export**

- Generate an editable Markdown report with files, scope, active filters, period, signature count, common attributes, top 10 signatures and their first/last-seen times, and breakdowns by endpoint, operation, status, node, and file.
- The report includes Observations headings for Root cause, Impact, Recommended fix, and Next steps; it can be copied or downloaded as `.md`.
- Export currently filtered errors as UTF-8 CSV with a BOM and all parsed fields. Formula injection is guarded.

### 3. PS Compare Report Viewer — PeopleSoft Compare Reports (`Alt+3`)

Browse PeopleSoft compare reports from XML project exports, binary `.idx` / `.prt` files, or ZIP archives without ActiveX, XSLT, or IE mode.

**Ingestion and report details**

- Open a report from a folder, one or more ZIP files, or selected XML / IDX / PRT files; folders, archives, and loose files can also be dragged and dropped.
- ZIP extraction is capped at 256 MB and validates entry bounds. XML entity declarations are rejected, control characters are stripped, and out-of-range numeric character references are clamped.
- Detects UTF-16LE, UTF-16BE, and UTF-8. Per-file parse warnings are reported without preventing other files from loading.
- Multiple projects can be open at once, selected in the sidebar, and closed individually.
- Reads XML reports (`projectList.xml`, `project.xml`, and per-object-type folders) and PeopleTools binary `.idx` / `.prt` cell-cache data, including headers, keys, attributes, and wrapped PeopleCode lines.
- Displays report headers including project name and description, run date, source/target database names and dates, compare method, and PeopleTools release.

**Navigation and comparison**

- Sidebar includes report details, a **Status Summary** matrix of definition types and categories, and a **Definition Types** list with object counts. Selecting a status-matrix cell lists its objects; empty definition types are greyed out.
- Each object card shows its name and primary keys, color-coded source-to-target status badges (`*` indicates customized), Action and Upgrade flags, and expandable side-by-side Source / Target attribute tables.
- Highlights changed, source-only, and target-only attributes and code lines.
- Filter by object name or status, expand/collapse all objects, and show more results (100 initially, then 200 at a time).
- **Differences only** hides unchanged rows and shows two lines of code context around changes.
- Global search can include values and code with an optional deep-search mode; results display their source type.

**Export**

- Export selected projects to PDF (landscape A4, monospaced) or editable Word `.docx` (Verdana and Consolas).
- Export projects one after another or merge them into one report with Summary of Projects and Summary by Object Type sections.
- Choose all or selected statuses and export all attributes/lines or differences only (with two lines of code context).
- PDF output uses a monospaced font chosen to distinguish `l`, `I`, `1`, `0`, and `O`, with sectioned pages and running project/report headers and page-number footers.
- DOCX output is OOXML with project section breaks, headers/footers, page fields, and document relationships.
- Progress, completion, and errors are reported in the export dialog.

### Cross-cutting workbench features

- **Sessions** — create (`Alt+N`), rename (edit button, double-click, or `F2`), close (with confirmation when content is loaded), and switch (`Alt+[` / `Alt+]`). Each session maintains independent state for all three utilities.
- **Utility tabs** — switch with `Alt+1/2/3`. Each tab shows a status dot when it has content and the current file/report label.
- **Theme and shortcuts** — light/dark theme is shared with the sub-apps; the Shortcuts button opens a keyboard reference.
- **Unsaved-work guard and notifications** — prompts before leaving when a session contains loaded content; toasts provide non-blocking status, error, and warning messages.
- **Clipboard helper** — `bdCopy` falls back to a textarea and `execCommand` when asynchronous clipboard access is blocked.
- **Storage broker** — the parent mediates persistence through `bdStore`, whitelists keys (`prefs`, `hist`, `saved`, `b:<hash>`), caps value size, and permits writes only from gigaView; sub-apps cannot write arbitrary `localStorage` keys.
- **Message broker and CSP** — messages are accepted only from the parent page's own iframes with the correct per-frame nonce; theme messages are one-way from parent to child and validated. The shell declares a CSP, and each sub-app iframe receives a per-frame nonce-based `script-src` policy with restrictive defaults and no network, object, or nested-frame sources.
- **Local processing** — the workbench does not use network I/O; files are processed locally without fetch, XMLHttpRequest, WebSocket, sendBeacon, remote images, or analytics.

# PG_DB_Viewer

A single-file, client-side viewer for PostgreSQL `pg_dump` SQL files. Drop in a `.sql` dump and browse its tables in a searchable, sortable grid — entirely in your browser. No server, no upload, no dependencies.

## Why

`pg_dump` output is plain text built around `COPY ... FROM stdin;` blocks, which isn't easy to skim by eye. This tool parses those blocks and renders each table as a proper data grid so you can quickly see what's inside a dump without spinning up a database.

## Features

- **Drag-and-drop or file picker** — load a `.sql` file directly from disk
- **Table sidebar** — lists every table found in the dump with its row count
- **Sortable columns** — click a column header to sort ascending/descending (numeric-aware)
- **Filter/search** — live text filter across all columns in the current table
- **Expandable cells** — click any long value (e.g. email bodies, JSON blobs) to view it in full
- **NULL-aware** — distinguishes `\N` (NULL) from empty strings
- **Handles large tables** — caps rendering at 2,000 rows at a time with a note, so huge tables don't freeze the page
- **100% local** — parsing happens in-browser via JavaScript; the file never leaves your machine

## Usage

1. Open `PG_DB_Viewer.html` in any modern browser (or visit it via GitHub Pages if enabled)
2. Drop your `.sql` dump file onto the page, or click to choose one
3. Select a table from the left sidebar
4. Click column headers to sort, use the search box to filter, click long cells to expand them

## How it works

The parser scans the dump line-by-line for:

```sql
COPY public.table_name (col1, col2, ...) FROM stdin;
val1	val2	...
val1	val2	...
\.
```

Each `COPY` block's tab-separated rows are parsed into a JS object keyed by table name, with `\N` mapped to `null` and standard `COPY` escape sequences (`\t`, `\n`, `\r`, `\\`) unescaped. Everything renders client-side — there's no backend and no external requests.

## Limitations

- Only parses `COPY ... FROM stdin` blocks (the default `pg_dump` data format). Dumps using `INSERT INTO` statements instead of `COPY` are not currently supported.
- Table schema (column types, constraints) is not parsed — only column names and row data.
- Very large dumps (tens of thousands of rows per table) may be slow to filter/sort since everything runs in-memory in the browser.

## Security note

This tool displays exactly what's in the dump you load, including any sensitive fields (passwords, credentials, PII) present in the source file. Since it's fully client-side, nothing is transmitted anywhere — but treat the HTML file and any dump you load the same way you'd treat the database itself.

## License

No license specified — add one if you plan to share or reuse this publicly.

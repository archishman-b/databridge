# DataBridge

**A no-code data workbench for consultants and strategy professionals.**

> Built because of a real problem: moving from a large conglomerate with a dedicated analytics team into consulting, where you're often the only person who needs to wrangle data — with no SQL access, no Alteryx licence, and one consultant on the team.

---

## What it is

DataBridge is a **single HTML file** that runs entirely in your browser. No installation. No Python. No server. No cost. Load a CSV or Excel file, apply operations from the panel, and export a clean result.

It covers the 20–25% of data operations that account for 90% of what consultants actually do on pricing, GTM, customer analytics, and finance projects.

**[→ Open DataBridge](https://archishman-b.github.io/databridge/databridge.html)**

---

## How to use it

**Option A — Use online (no setup)**
Click the link above. Everything runs in your browser.

**Option B — Use offline**
Download `databridge.html` → double-click → opens in any browser. Works with no internet connection.

**Loading data**
- Click "Load dataset" or drag and drop files anywhere on the page
- Supports: CSV, TSV, Excel (.xlsx, .xls)
- Large files work fine — tested on 500K+ row transaction datasets

---

## Operations covered

### Joins & Combine
| Operation | What it does |
|---|---|
| **Join tables** | Inner, left, right, full outer join on a key column |
| **Anti-join** | Find rows in this table with no match in another |
| **Union / Append** | Stack two tables vertically |
| **Lookup / VLOOKUP** | Bring a single column from another table by key |

### Filter & Subset
| Operation | What it does |
|---|---|
| **Filter rows** | Keep rows matching conditions — AND/OR logic, IN lists, `.contains()` |
| **Select / drop columns** | Choose which columns to keep or remove |
| **Deduplicate** | Remove duplicates — keep first, last, or flag all |
| **Top / Bottom N** | Get top or bottom N rows, overall or per group |
| **Random sample** | Sample by % or fixed count, with reproducible seed |

### Aggregate & Summarise
| Operation | What it does |
|---|---|
| **Group by & summarise** | Sum, count, count distinct, avg, min, max, median |
| **Pivot** | Turn row values into columns |
| **Unpivot / Melt** | Turn columns into rows |
| **Running total** | Cumulative sum within a group |
| **Percent of total** | Each row as % of group or grand total |

### Calculate & Transform
| Operation | What it does |
|---|---|
| **Calculated column** | Add a column with arithmetic, IF/THEN/ELSE, or CASE WHEN |
| **Date parts** | Extract year, quarter, month, week, day, weekday |
| **Date difference** | Days, months, or years between two date columns |
| **Text operations** | Trim, upper/lower, concat, split, left/right/mid, extract numbers |
| **Find & replace** | Replace values — plain text or regex |
| **Bin / bucket** | Turn continuous numbers into range buckets |
| **Rank** | Rank, dense rank, or row number within a group |
| **Lag / Lead** | Previous or next row value within a group |
| **Fill nulls** | Replace missing values with constant, mean, median, forward/backward fill |
| **Rename columns** | Rename one or more columns |
| **Change data type** | Cast to number, text, or date |

### Inspect & Validate
| Operation | What it does |
|---|---|
| **Column summary** | Min, max, mean, median, distinct count, % null for every column |
| **Null audit** | Count and % of missing values per column |
| **Duplicate finder** | Show which rows are duplicates |
| **Value frequencies** | Top N most common values in a column |
| **Crosstab** | Two-way frequency table between two columns |
| **Correlation matrix** | Pairwise correlation for numeric columns |
| **Pareto / 80-20** | Cumulative % of total, sorted descending |

### Segmentation & Cohort Logic
| Operation | What it does |
|---|---|
| **Conditional tagging** | Assign segment labels based on IF/THEN rules |
| **Percentile / decile** | Assign quartile, decile, or custom percentile buckets |
| **Flag first / last** | Flag first or last occurrence per group |

### Time Intelligence
| Operation | What it does |
|---|---|
| **Period comparison** | Add prior-period value column — YoY, MoM, WoW, QoQ |
| **Rolling average** | Trailing N-period average, sum, min, or max |
| **Fiscal calendar** | Map dates to a fiscal year starting any month |

---

## Formula syntax

DataBridge uses a natural language formula syntax — no SQL required.

```
[Revenue] / [Units]                          → arithmetic
[Price] * (1 - [Discount])                  → calculated column
IF [Status] == 'Won' THEN 1 ELSE 0          → conditional
CASE WHEN [Score] > 80 THEN 'High'
     WHEN [Score] > 50 THEN 'Mid'
     ELSE 'Low'                              → multi-condition
```

**Filter expressions:**
```
[Revenue] > 10000
[Region] == 'North' AND [Status] != 'Closed'
[Category] IN ['A', 'B', 'C']
[Name].contains('Smith')
[Date] > '2023-01-01' AND [Date] <= '2023-12-31'
```

---

## UX features

- **Pipeline bar** — every step you've applied is visible at the top. Remove any step to rewind.
- **Undo** — step back one operation at a time
- **Audit log** — every operation logged with input/output row counts. Click "Audit log" top right.
- **Preview with sort & search** — click any column header to sort; search box filters all columns
- **Export** — CSV or Excel (.xlsx). "Copy to clipboard" for pasting directly into slides.
- **Large file support** — renders 500 rows at a time, loads more on demand

---

## Why a single HTML file?

The target user is a strategy consultant or analyst who:
- Doesn't have Python or a terminal workflow
- Works on client machines where they can't install software
- Needs to share the tool with a colleague instantly ("just open this file")
- Works offline in client offices or on flights

A single `.html` file satisfies all of these. It runs on Windows, Mac, Linux, or any tablet. No dependencies. No login. No data ever leaves the browser.

---

## Performance notes

DataBridge uses JavaScript's native array operations. Tested on:
- 500K rows × 15 columns — loads in ~3 seconds, operations in <1 second
- 1M+ rows — loads in ~8 seconds, simple operations in 1–3 seconds
- For very large files, operations like correlation and crosstab may take a few seconds

For datasets above 2M rows, consider filtering down first or using a dedicated SQL tool.

---

## What's not covered (deliberately)

- Complex nested subqueries — the visual pipeline handles this implicitly by chaining steps
- Database connections — by design (works offline, no credentials)
- Machine learning / statistical modelling — use Python/R for that 5%
- Stored procedures, loops, variables

---

## Built by

**Archishman Bandyopadhyay** — strategy consultant, 12 years across Accenture, Monitor Deloitte, Mahindra, Tata Motors.

Built because I needed it. On my first pricing analytics engagement at Deloitte — working with a single consultant and no Alteryx licence — I spent too much time in Excel doing things that should have taken seconds. This is the tool I wish I'd had.

Part of a [50-week public build](https://github.com/archishman-b) — translating consulting experience into open-source tools.

---

## Contributing

Issues and PRs welcome. The most useful contributions are:
- Additional formula functions
- Reported edge cases on unusual CSV formats
- UI improvements for the operation modals

---

## License

MIT — use freely, in client engagements or otherwise.

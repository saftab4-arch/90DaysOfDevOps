
# Day 20 — Bash Scripting Challenge: Log Analyzer & Report Generator

> The bootcamp finale — a real log analyzer that counts errors, finds critical events, ranks the top issues, and emits a daily report.
> This is exactly the work SREs do at 3 AM when something breaks in production.

---

## Project Overview

Every Linux server generates log files full of system events. When something breaks, you need to quickly answer four questions:

1. How many errors happened?
2. Were there any critical events?
3. What's the most common error?
4. Can I get a summary I can send to the team?

This project builds `log_analyzer.sh` — a single Bash script that answers all four questions in seconds, then archives the processed log for compliance.

The script uses the **classic Unix text-processing toolkit**: `grep`, `awk`, `sort`, `uniq`, `sed`, and `wc`. Once you know this pipeline, you can analyze any log on any Linux system anywhere.

---

## Table of Contents

- [Setup — Sample Log File](#setup--sample-log-file)
- [Exploration — Meet the Tools](#exploration--meet-the-tools)
- [Task 1 — Input & Validation](#task-1--input--validation)
- [Task 2 — Error Count](#task-2--error-count)
- [Task 3 — Critical Events](#task-3--critical-events)
- [Task 4 — Top 5 Error Messages](#task-4--top-5-error-messages)
- [Task 5 — Summary Report](#task-5--summary-report)
- [Task 6 — Archive Processed Log](#task-6--archive-processed-log)
- [Full Script](#full-script)
- [Tools Used](#tools-used)
- [Key Learnings](#key-learnings)

---

## Setup — Sample Log File

To test the script I built a realistic sample log with all the patterns the analyzer needs to handle:

- 25 `ERROR` lines with repeating messages (so "top 5" has something to rank)
- 3 `Failed` lines (counted alongside ERRORs)
- 3 `CRITICAL` lines (for the critical events section)
- `INFO` and `WARN` noise so the script has to filter through clutter

### Sample log creation using heredoc

```bash
cat > sample_log.log << 'EOF'
2026-05-20 08:00:01 INFO  System startup complete
2026-05-20 08:01:15 ERROR Connection timed out
2026-05-20 08:10:00 CRITICAL Disk space below threshold
2026-05-20 08:13:44 Failed to authenticate user admin
...
EOF
```

### Heredoc Explained

| Piece | Meaning |
|-------|---------|
| `cat > file << 'EOF'` | **Heredoc** — feed everything between this line and `EOF` directly into the file |
| `'EOF'` (quoted) | Prevents Bash from expanding any `$variables` inside the content |

### Verification

```bash
wc -l sample_log.log       # → 40 lines
head -5 sample_log.log     # confirm format
```

### Screenshot

![Setup — sample log creation](screenshots/day20/setup-sample-log.png)

---

## Exploration — Meet the Tools

Before writing the script, I explored each tool individually to understand what they do.

### `grep` — Filter lines by pattern

```bash
grep -c "ERROR" sample_log.log              # count ERROR lines → 23
grep -c "Failed" sample_log.log             # count Failed lines → 3
grep -cE "ERROR|Failed" sample_log.log      # both combined → 26
grep -n "CRITICAL" sample_log.log           # show with line numbers
```

| Flag | Meaning |
|------|---------|
| `grep` | **G**lobal **r**egular **e**xpression **p**rint — searches lines matching a pattern |
| `-c` | **C**ount matching lines only (don't print them) |
| `-n` | Show line **n**umbers alongside matches |
| `-E` | **E**xtended regex — lets you use `\|` for logical OR inside the pattern |
| `"ERROR\|Failed"` | The `\|` in regex means "this OR that" |

### Screenshot — `grep` exploration

![grep exploration](screenshots/day20/grep-exploration.png)

The line-number output shows critical events at lines **14, 24, 34** — exactly where we placed them in the sample log.

### `awk` — Text processing language

`awk` splits each line into **fields** separated by whitespace. For a line like:

```
2026-05-20 08:01:15 ERROR Connection timed out
```

`awk` sees:
- `$1` = `2026-05-20` (date)
- `$2` = `08:01:15` (time)
- `$3` = `ERROR` (level)
- `$4` = `Connection`, `$5` = `timed`, `$6` = `out`
- `$0` = the whole line

The expression `{$1=$2=$3=""; print}` means **"set the first 3 fields to empty strings, then print the line"** — stripping out date/time/level and leaving just the error message.

### `sort` and `uniq` — The classic counting duo

- `sort` → sorts lines alphabetically (needed BEFORE `uniq` because `uniq` only collapses *adjacent* duplicates)
- `uniq -c` → collapses duplicates and prepends the count
- `sort -rn` → sorts numerically (`-n`) in reverse order (`-r`)
- `head -5` → first 5 lines only

| Flag | Meaning |
|------|---------|
| `-c` (uniq) | **C**ount how many times each line appeared |
| `-r` (sort) | **R**everse order |
| `-n` (sort) | **N**umeric sort (so `10` comes after `9`, not before alphabetically) |

### The full pipeline assembled

```bash
grep "ERROR" sample_log.log | awk '{$1=$2=$3=""; print}' | sort | uniq -c | sort -rn | head -5
```

Reading left-to-right:
1. **`grep "ERROR"`** → keep only ERROR lines
2. **`awk '{$1=$2=$3=""; print}'`** → strip date/time/level
3. **`sort`** → group identical messages together
4. **`uniq -c`** → collapse duplicates and prepend count
5. **`sort -rn`** → sort by count descending
6. **`head -5`** → take the top 5

**This is the most important pipeline pattern in Unix.** Once internalized, it applies to any log, any dataset, any text file.

### Screenshot — full pipeline test

![awk-sort-uniq pipeline](screenshots/day20/awk-sort-uniq-pipeline.png)

The output shows the ranking the script will produce:

```
8 Connection timed out
6 File not found
5 Permission denied
3 Disk I/O error
1 Out of memory
```

---

## Task 1 — Input & Validation

### Requirements
1. Accept log file path as command-line argument
2. Exit with error if no argument provided
3. Exit with error if file doesn't exist

### Code

```bash
if [ -z "${1:-}" ]; then
    echo "Error: No log file provided."
    echo "Usage: $0 <path/to/logfile>"
    exit 1
fi

LOG_FILE="$1"

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: File '$LOG_FILE' does not exist."
    exit 1
fi
```

### Explanation

| Element | Meaning |
|---------|---------|
| `${1:-}` | "Use `$1` if it exists, otherwise empty." Needed because `set -u` would otherwise crash before we can check |
| `-z` | String is empty test |
| `! -f` | Path is NOT a regular file |
| `exit 1` | Exit with code `1` (failure) |

---

## Task 2 — Error Count

### Requirement
Count lines containing `ERROR` or `Failed`, print to console.

### Code

```bash
ERROR_COUNT=$(grep -cE "ERROR|Failed" "$LOG_FILE" || true)
```

### Why `|| true`?

`grep` returns exit code `1` when it finds **zero** matches. Combined with `set -e`, that would crash the script. The `|| true` says "if `grep` returns failure, pretend it succeeded."

**This is a critical pattern** for any counting command inside strict-mode scripts.

### Result on sample log

```
Total errors: 26   (23 ERRORs + 3 Faileds)
```

---

## Task 3 — Critical Events

### Requirement
Print all `CRITICAL` lines with their line numbers in the format:
```
Line 14: 2026-05-20 08:10:00 CRITICAL Disk space below threshold
```

### Code

```bash
CRITICAL_LINES=$(grep -n "CRITICAL" "$LOG_FILE" || true)

if [ -z "$CRITICAL_LINES" ]; then
    echo "No critical events found."
else
    echo "$CRITICAL_LINES" | sed 's/^\([0-9]*\):/Line \1: /'
fi
```

### The `sed` substitution

`grep -n` outputs lines like `14:2026-05-20 08:10:00 CRITICAL ...`. We need to reformat that as `Line 14: 2026-05-20 ...`.

```bash
sed 's/^\([0-9]*\):/Line \1: /'
```

| Piece | Meaning |
|-------|---------|
| `sed` | **S**tream **ed**itor — find & replace on streams |
| `s/pattern/replacement/` | Substitute syntax |
| `^` | Anchor: start of line |
| `\([0-9]*\)` | **Capture group** — one or more digits, saved as group `\1` |
| `:` | Literal colon |
| `Line \1: ` | Replacement: "Line ", then captured digits, then ": " |

### Note on the `awk` approach

I initially tried `awk -F:` (split on colon) for this, but it broke because the timestamp portion (`08:10:00`) also contains colons. `awk` mis-counted field positions and the year `2026` got chopped to `26`. The `sed` capture-group approach is bulletproof because it only consumes the **first** colon at the start of the line.

**Lesson learned:** when the delimiter character also appears in the data, use regex capture groups instead of field splitting.

### Result on sample log

```
Line 14: 2026-05-20 08:10:00 CRITICAL Disk space below threshold
Line 24: 2026-05-20 08:20:00 CRITICAL Database connection lost
Line 34: 2026-05-20 08:30:44 CRITICAL Service unresponsive
```

---

## Task 4 — Top 5 Error Messages

### Requirement
Extract ERROR lines, identify the 5 most common messages, display with counts sorted descending.

### Code

```bash
TOP_ERRORS=$(grep "ERROR" "$LOG_FILE" \
    | awk '{$1=$2=$3=""; sub(/^ +/, ""); print}' \
    | sort \
    | uniq -c \
    | sort -rn \
    | head -5)
```

### The `awk` improvement — `sub(/^ +/, "")`

After emptying fields `$1`, `$2`, `$3`, the line has leading spaces. The `sub(/^ +/, "")` is an awk regex substitution:

- `^ +` → "one or more spaces at the start of the line"
- Replace with empty string
- Result: trimmed clean output

### Line continuation with `\`

The `\` at the end of each line tells Bash "this command continues on the next line." Purely cosmetic — makes long pipelines readable.

### Result on sample log

```
8 Connection timed out
6 File not found
5 Permission denied
3 Disk I/O error
1 Out of memory
```

---

## Task 5 — Summary Report

### Requirement
Generate `log_report_<date>.txt` containing date, log filename, total lines, error count, top 5 errors, and critical events.

### Code

```bash
{
    echo "==========================================="
    echo "         LOG ANALYSIS REPORT"
    echo "==========================================="
    echo "Date of analysis : $DATE_STAMP"
    echo "Log file         : $LOG_FILE"
    echo "Total lines      : $TOTAL_LINES"
    echo "Total errors     : $ERROR_COUNT"
    echo
    echo "--- Top 5 Error Messages ---"
    echo "$TOP_ERRORS"
    echo
    echo "--- Critical Events ---"
    if [ -z "$CRITICAL_LINES" ]; then
        echo "No critical events found."
    else
        echo "$CRITICAL_LINES" | sed 's/^\([0-9]*\):/Line \1: /'
    fi
    echo
    echo "==========================================="
    echo "Report generated: $(date '+%Y-%m-%d %H:%M:%S')"
    echo "==========================================="
} > "$REPORT_FILE"
```

### Command Group Redirection

The `{ ... } > file` syntax is the **command group** technique:

| Piece | Meaning |
|-------|---------|
| `{ ... }` | Command group — runs the enclosed commands as a single block |
| `> "$REPORT_FILE"` | Redirect ALL output from the block to the file in one go |

Way cleaner than appending each line individually with `>>`.

We **reuse** the variables (`$TOTAL_LINES`, `$ERROR_COUNT`, `$TOP_ERRORS`, `$CRITICAL_LINES`) computed earlier — no need to run `grep` twice. **Compute once, use many times.**

---

## Task 6 — Archive Processed Log

### Requirement
Create `archive/` directory, move the processed log into it, print confirmation.

### Code

```bash
ARCHIVE_DIR="archive"
mkdir -p "$ARCHIVE_DIR"

ARCHIVED_NAME="${ARCHIVE_DIR}/$(basename "$LOG_FILE").${DATE_STAMP}"
mv "$LOG_FILE" "$ARCHIVED_NAME"

echo "Log file archived to: $ARCHIVED_NAME"
```

### Explanation

| Element | Meaning |
|---------|---------|
| `mkdir -p` | Make directory, safe if it already exists |
| `basename "$LOG_FILE"` | Strip directory path, get just filename (Day 19) |
| `.${DATE_STAMP}` | Suffix with date so multiple days' archives don't collide |
| `mv` | Move (rename) the file |

---

## Full Script

The complete `log_analyzer.sh` lives alongside this documentation. Here are the key features summed up:

- **Strict mode** (`set -euo pipefail`) for production safety
- **Argument validation** with helpful usage messages
- **File existence check** before processing
- **Single-pass analysis** — values computed once, reused for console output AND report file
- **Graceful empty handling** — `|| true` on `grep` calls so no-match doesn't crash the script
- **Self-documenting headers** — section comments mark each task

### Screenshot — full script run

![Script run with all test cases](screenshots/day20/script-run-output.png)

This shows the script correctly handling:
1. No argument → usage error
2. Bad file → "does not exist" error
3. Real run → full analysis output

### Screenshot — generated report file

![Report file and archive directory](screenshots/day20/report-and-archive.png)

The `cat log_report_*.txt` output shows the generated report has all 6 required sections (date, log file, total lines, total errors, top 5 messages, critical events) plus a report-generation timestamp footer.

### Screenshot — final clean run after the sed fix

![Final clean run with correct date formatting](screenshots/day20/final-clean-run.png)

This is the final verification. Notice the Critical Events now show clean full dates (`2026-05-20`, not the chopped `26-05-20` from the initial `awk` attempt):

```
Line 14: 2026-05-20 08:10:00 CRITICAL Disk space below threshold
Line 24: 2026-05-20 08:20:00 CRITICAL Database connection lost
Line 34: 2026-05-20 08:30:44 CRITICAL Service unresponsive
```

---

## Tools Used

| Tool | Purpose |
|------|---------|
| `grep` | Pattern matching, line counting (`-c`), line numbering (`-n`), extended regex (`-E`) |
| `awk` | Field extraction, leading-space trimming with `sub()` |
| `sort` | Grouping (default alphabetic), numeric reverse sort (`-rn`) |
| `uniq -c` | Collapse adjacent duplicates with counts |
| `sed` | Regex find-and-replace with capture groups (`\1`) |
| `wc -l` | Line counting |
| `head -5` | Take top N |
| `mkdir -p` | Safe directory creation |
| `mv` | File move/rename |
| `basename` | Strip path, keep filename |
| `date +%Y-%m-%d` | Today's date for filename stamp |

---

## Key Learnings

### 1. The `grep | awk | sort | uniq -c | sort -rn | head` pipeline is the most important pattern in Unix

This six-step recipe — filter → project → group → count → rank → limit — analyzes any log, any dataset, any text file. It works the same on a laptop, on a production server, and inside CloudWatch Logs Insights queries (just with slightly different syntax). **Master this once, use it forever.** Future me will reach for it in Datadog, Splunk, BigQuery, and SQL the same exact way.

### 2. Delimiters that appear in your data will break field-based parsing

I learned this the hard way. My first attempt used `awk -F:` to split on colons after `grep -n`, but the timestamp portion (`08:10:00`) also contained colons — so `awk` miscounted fields and the year `2026` got chopped to `26`. The fix was switching to `sed` with a regex capture group that only consumed the **first** colon. **Lesson:** when a delimiter also appears in your data, use regex anchored patterns instead of field splitting. This applies everywhere — CSV files with commas in quoted strings, log lines with timestamps, anywhere the "separator" is also "content."

### 3. Strict mode + `|| true` is the standard pattern for counting commands

`set -e` is non-negotiable for production scripts, but commands like `grep -c` and `grep -n` return non-zero exit codes when they find zero matches — which technically isn't a "failure," just "no results." Without `|| true`, a script that says "look for critical events and count them" would crash if there happened to be zero critical events. Wrapping `grep` calls with `|| true` lets the script handle empty results gracefully. **This is also the pattern for `find` with no matches, `awk` with conditional prints, and any command where "zero results" is a valid outcome.**

---

## Files Created

| File | Purpose |
|------|---------|
| `log_analyzer.sh` | The analyzer script |
| `sample_log.log` | 40-line synthetic log for testing |
| `log_report_<date>.txt` | Auto-generated report (created by the script) |
| `archive/` | Auto-created directory holding processed logs |
| `day-20-solution.md` | This documentation |

---

## Conclusion

Day 20 was the **synthesis** of every concept from Days 16–19 plus introduced the Unix text-processing toolkit (`grep`/`awk`/`sort`/`uniq`/`sed`). The script demonstrates production patterns: strict mode, argument validation, graceful handling of zero-match cases, command-group redirection, and single-pass computation reused for multiple outputs.

More importantly, the underlying patterns transfer **directly to real ops work**. The same mental model — filter, project, group, count, rank — drives:

- CloudWatch Logs Insights queries
- Datadog log search
- Splunk SPL queries
- SQL aggregations (`GROUP BY ... ORDER BY count DESC LIMIT 5`)
- Any log analysis you'll ever do

Bootcamp complete. From here on out, when production logs need analyzing at 3 AM and the fancy SaaS tool is down, this script (or something like it) is what saves the day.

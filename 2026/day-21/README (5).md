# Shell Scripting Bootcamp — Daily Log

> A consolidated, day-by-day log of my Bash shell scripting journey.
> Every script, every flag, every output — all in one place.

---

## Table of Contents

- [Day 16 — Shell Scripting Basics](#day-16--shell-scripting-basics)
  - [Task 1 — Your First Script (`hello.sh`)](#task-1--your-first-script-hellosh)
  - [Task 2 — Variables (`variables.sh`)](#task-2--variables-variablessh)
  - [Task 3 — User Input with `read` (`greet.sh`)](#task-3--user-input-with-read-greetsh)
  - [Task 4A — Number Checker (`check_number.sh`)](#task-4a--number-checker-check_numbersh)
  - [Task 4B — File Existence Check (`file_check.sh`)](#task-4b--file-existence-check-file_checksh)
  - [Task 5 — Service Status Check (`server_check.sh`)](#task-5--service-status-check-server_checksh)
- [Day 17 — Loops, Arguments & Error Handling](#day-17--loops-arguments--error-handling)
  - [Task 1 — For Loops (`for_loop.sh`, `count.sh`)](#task-1--for-loops-for_loopsh-countsh)
  - [Task 2 — While Loop (`countdown.sh`)](#task-2--while-loop-countdownsh)
  - [Task 3 — Command-Line Arguments (`greet.sh`, `args_demo.sh`)](#task-3--command-line-arguments-greetsh-args_demosh)
  - [Task 4 — Package Installation (`install_packages.sh`)](#task-4--package-installation-install_packagessh)
  - [Task 5 — Error Handling (`safe_script.sh`)](#task-5--error-handling-safe_scriptsh)
- [Day 18 — Functions & Intermediate Concepts](#day-18--functions--intermediate-concepts)
  - [Task 1 — Basic Functions (`functions.sh`)](#task-1--basic-functions-functionssh)
  - [Task 2 — Functions for System Checks (`disk_check.sh`)](#task-2--functions-for-system-checks-disk_checksh)
  - [Task 3 — Strict Mode (`strict_demo.sh`)](#task-3--strict-mode-strict_demosh)
  - [Task 4 — Local vs Global Variables (`local_demo.sh`)](#task-4--local-vs-global-variables-local_demosh)
  - [Task 5 — System Information Reporter (`system_info.sh`)](#task-5--system-information-reporter-system_infosh)
- [Day 19 — Project: Log Rotation, Backup & Crontab](#day-19--project-log-rotation-backup--crontab)
  - [Setup — Sandbox Environment](#setup--sandbox-environment)
  - [Task 1 — Log Rotation Script (`log_rotate.sh`)](#task-1--log-rotation-script-log_rotatesh)
  - [Task 2 — Server Backup Script (`backup.sh`)](#task-2--server-backup-script-backupsh)
  - [Task 3 — Crontab Understanding & Entries](#task-3--crontab-understanding--entries)
  - [Task 4 — Scheduled Maintenance Script (`maintenance.sh`)](#task-4--scheduled-maintenance-script-maintenancesh)
  - [Cron Entries Summary](#cron-entries-summary)
- [Day 20 — Challenge: Log Analyzer & Report Generator](#day-20--challenge-log-analyzer--report-generator)
  - [Setup — Sample Log File](#setup--sample-log-file)
  - [Exploration — Meet `grep`, `awk`, `sort`, `uniq`](#exploration--meet-grep-awk-sort-uniq)
  - [Task 1 — Input & Validation](#task-1--input--validation)
  - [Task 2 — Error Count](#task-2--error-count)
  - [Task 3 — Critical Events](#task-3--critical-events)
  - [Task 4 — Top 5 Error Messages](#task-4--top-5-error-messages)
  - [Task 5 — Summary Report](#task-5--summary-report)
  - [Task 6 — Archive Processed Log](#task-6--archive-processed-log)
- [Master Reference — Flags & Symbols](#master-reference--flags--symbols)

---

# Day 16 — Shell Scripting Basics

## Overview

Day 16 covered the foundations of Bash shell scripting:

- The shebang (`#!/bin/bash`)
- Variables and quoting rules
- Capturing user input with `read`
- Conditional logic (`if` / `elif` / `else` / `fi`)
- File existence checks
- Service status checks using `systemctl`

These are the building blocks every Linux administration and DevOps automation task relies on.

---

## Task 1 — Your First Script (`hello.sh`)

### Script

```bash
#!/bin/bash

echo "Hello, DevOps!"
```

### Run

```bash
chmod +x hello.sh
./hello.sh
```

### Line-by-line Explanation

| Element | Meaning |
|---------|---------|
| `#!/bin/bash` | **Shebang** — tells Linux to execute this file using the Bash interpreter. Without it, another shell may interpret the script incorrectly, causing portability issues. |
| `echo` | Prints text to the terminal (standard output). |

### Command Flags

| Flag | Meaning |
|------|---------|
| `chmod` | Change file permissions. |
| `+x` | Add execute permission. |
| `./` | Run the script from the *current* directory. `.` = current directory. |

### Screenshot

![Task 1 — hello.sh execution](screenshots/day16/task1-hello-script.png)

---

## Task 2 — Variables (`variables.sh`)

### Script

```bash
#!/bin/bash

NAME="Aftab"
ROLE="DevOps Engineer"

echo "Hello, I am $NAME and I am a $ROLE"
```

### Explanation

Variables in Bash store data. The golden rule: **no spaces around `=`**.

| Correct | Incorrect |
|---------|-----------|
| `NAME="Aftab"` | `NAME = "Aftab"` |

The `$` sigil is used to *expand* (read) a variable's value:

```bash
echo $NAME    # → Aftab
```

### Quoting Rules

| Quoting | Behavior | Example | Output |
|---------|----------|---------|--------|
| **Double quotes** `" "` | Variables are expanded | `echo "$NAME"` | `Aftab` |
| **Single quotes** `' '` | Everything is literal | `echo '$NAME'` | `$NAME` |

Use double quotes when you want variable substitution. Use single quotes when you want the text exactly as written.

### Screenshot

![Task 2 — variables.sh](screenshots/day16/task2-variables.png)

---

## Task 3 — User Input with `read` (`greet.sh`)

### Script

```bash
#!/bin/bash

read -p "Enter your name: " NAME
read -p "Enter your favourite tool: " TOOL

echo "Hello $NAME, your favourite tool is $TOOL"
```

### Explanation

`read` pauses the script and waits for the user to type something.

| Element | Meaning |
|---------|---------|
| `read` | Read user input into a variable. |
| `-p` | **Prompt flag** — displays a message *before* the input cursor. |
| `NAME` | The variable that stores whatever the user typed. |

### Screenshot

![Task 3 — greet.sh user input](screenshots/day16/task3-read-input.png)

---

## Task 4A — Number Checker (`check_number.sh`)

### Script

```bash
#!/bin/bash

read -p "Enter a number: " num

if [ "$num" -gt 0 ]; then
    echo "Positive Number"
elif [ "$num" -lt 0 ]; then
    echo "Negative Number"
else
    echo "Number is Zero"
fi
```

### Explanation

This is the classic `if / elif / else / fi` block.

| Keyword | Meaning |
|---------|---------|
| `if` | Start a conditional block. |
| `[ ... ]` | Test brackets. **Spaces inside the brackets are mandatory.** |
| `then` | Code to run if the condition is true. |
| `elif` | "Else if" — test another condition. |
| `else` | Default branch — runs if everything above failed. |
| `fi` | End the `if` block. (`fi` = `if` spelled backwards.) |

### Numeric Comparison Flags

| Flag | Meaning |
|------|---------|
| `-gt` | Greater than |
| `-lt` | Less than |
| `-eq` | Equal |
| `-ne` | Not equal |
| `-ge` | Greater than or equal |
| `-le` | Less than or equal |

### Common Mistake

```bash
# ❌ WRONG — no spaces inside brackets
if ["$num"-gt0]; then

# ✅ CORRECT
if [ "$num" -gt 0 ]; then
```

### Screenshot

![Task 4A — number checker](screenshots/day16/task4a-number-check.png)

---

## Task 4B — File Existence Check (`file_check.sh`)

### Script

```bash
#!/bin/bash

read -p "Enter file path: " filepath

if [ -f "$filepath" ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

### Explanation

`-f` checks whether a path points to a **regular file**.

### File Test Flags

| Flag | Checks if path is... |
|------|----------------------|
| `-f` | A regular file |
| `-d` | A directory |
| `-e` | Exists (any type) |
| `-r` | Readable |
| `-w` | Writable |
| `-x` | Executable |

### Screenshot

![Task 4B — file existence check](screenshots/day16/task4b-file-check.png)

---

## Task 5 — Service Status Check (`server_check.sh`)

### Script

```bash
#!/bin/bash

SERVICE="ssh"

read -p "Do you want to check the service status? (y/n): " answer

if [ "$answer" = "y" ]; then
    systemctl status $SERVICE

    if systemctl is-active --quiet $SERVICE; then
        echo "$SERVICE is active"
    else
        echo "$SERVICE is not active"
    fi
else
    echo "Skipped"
fi
```

### Explanation

This script combines user input, conditional logic, and Linux service management.

| Element | Meaning |
|---------|---------|
| `systemctl` | The systemd command for managing Linux services (ssh, nginx, docker, etc.). |
| `status` | Show the current status of a service. |
| `is-active` | Returns exit code `0` if the service is running, non-zero otherwise. |
| `--quiet` | Suppress all output — only the exit code matters. Useful inside `if` checks. |

### Exit Codes

Every Linux command returns an exit code when it finishes:

| Exit Code | Meaning |
|-----------|---------|
| `0` | Success |
| Non-zero | Failure |

That's why `if systemctl is-active --quiet ssh` works — Bash treats `0` as true, anything else as false.

### Screenshots

![Task 5 — service status check](screenshots/day16/task5-ssh-service-check.png)

![Task 5 — complete output](screenshots/day16/task5-complete.png)

---

## Day 16 — Files Created

| File | Purpose |
|------|---------|
| `hello.sh` | First Bash script |
| `variables.sh` | Variable practice |
| `greet.sh` | User input with `read` |
| `check_number.sh` | Numeric conditions |
| `file_check.sh` | File existence check |
| `server_check.sh` | Service status checker |

---

# Day 17 — Loops, Arguments & Error Handling

## Overview

Day 17 stepped up into automation territory:

- `for` loops over lists and numeric ranges
- `while` loops with arithmetic
- Command-line arguments (`$1`, `$#`, `$@`, `$0`)
- Package installation automation with root check
- Error handling with `set -e` and the `||` operator

These patterns appear everywhere in real CI/CD pipelines, deployment scripts, and infrastructure automation.

---

## Task 1 — For Loops (`for_loop.sh`, `count.sh`)

### Script 1 — Looping over a list

```bash
#!/bin/bash

for fruit in apple banana mango orange grapes
do
    echo "Fruit: $fruit"
done
```

### Explanation

| Element | Meaning |
|---------|---------|
| `for` | Start a loop. |
| `fruit` | Loop variable — on each iteration, holds the current item. |
| `in apple banana ...` | The list of values to iterate through. |
| `do` | Start the loop body. |
| `done` | End the loop body. |

### Output

```text
Fruit: apple
Fruit: banana
Fruit: mango
Fruit: orange
Fruit: grapes
```

### Screenshots

![For loop script](screenshots/day17/task1-for-loop-script.png)

![For loop output](screenshots/day17/task1-for-loop-output.png)

---

### Script 2 — Numeric range with brace expansion (`count.sh`)

```bash
#!/bin/bash

for number in {1..10}
do
    echo "$number"
done
```

### Explanation

`{1..10}` is **Bash sequence expansion**. Bash expands it into `1 2 3 4 5 6 7 8 9 10` before the loop runs.

You can also step it: `{1..10..2}` → `1 3 5 7 9`.

### Screenshots

![For loop with numbers — script](screenshots/day17/task1-for-loop-number-script.png)

![For loop with numbers — output](screenshots/day17/task1-for-loop-number-output.png)

---

## Task 2 — While Loop (`countdown.sh`)

### Script

```bash
#!/bin/bash

read -p "Enter starting number: " number

while [ "$number" -ge 0 ]
do
    echo "$number"
    number=$((number - 1))
done

echo "Done!"
```

### Explanation

| Element | Meaning |
|---------|---------|
| `while` | Repeat the loop body *while* the condition is true. |
| `[ "$number" -ge 0 ]` | Condition test — keep going while `$number ≥ 0`. |
| `-ge` | Greater than or equal. |
| `$(( ... ))` | **Arithmetic expansion** — evaluates math inside the parens. Without it, Bash treats values as strings, and `number - 1` would just be the literal text. |

### Example Run

```text
Enter starting number: 5
5
4
3
2
1
0
Done!
```

### Screenshots

![While loop script](screenshots/day17/task2-while-loop-script.png)

![While loop output](screenshots/day17/task2-while-loop-output.png)

---

## Task 3 — Command-Line Arguments (`greet.sh`, `args_demo.sh`)

### Script 1 — `greet.sh` (argument validation)

```bash
#!/bin/bash

if [ -z "$1" ]
then
    echo "Usage: ./greet.sh <name>"
    exit 1
fi

echo "Hello, $1!"
```

### Explanation

| Element | Meaning |
|---------|---------|
| `$1` | The **first command-line argument** passed to the script. |
| `-z` | Test if a string is **empty (zero-length)**. |
| `exit 1` | Exit the script with code `1` (failure). |

So this script says: "if no name was passed, print usage and exit with an error."

### Screenshots

![greet.sh argument check script](screenshots/day17/task3-greet-script.png)

![greet.sh full output](screenshots/day17/task3-greet-output.png)

---

### Script 2 — `args_demo.sh` (showing all argument variables)

```bash
#!/bin/bash

echo "Script Name: $0"
echo "Total Arguments: $#"
echo "All Arguments: $@"
```

### Special Argument Variables

| Variable | Meaning |
|----------|---------|
| `$0` | Script name itself |
| `$1`, `$2`, ... | First, second, etc. arguments |
| `$#` | **Total number** of arguments passed |
| `$@` | **All arguments** as a list |
| `$*` | All arguments as one string (rarely the right choice) |

### Example

```bash
./args_demo.sh hello world linux
```

Output:

```text
Script Name: ./args_demo.sh
Total Arguments: 3
All Arguments: hello world linux
```

### Screenshots

![arguments.sh script](screenshots/day17/task3-arguments-script.png)

![arguments.sh full output](screenshots/day17/task3-arguments-output.png)

---

## Task 4 — Package Installation (`install_packages.sh`)

### Script

```bash
#!/bin/bash

if [ "$EUID" -ne 0 ]
then
    echo "Please run as root"
    exit 1
fi

PACKAGES="nginx curl wget"

for package in $PACKAGES
do
    if dpkg -s "$package" &> /dev/null
    then
        echo "$package is already installed"
    else
        echo "Installing $package..."
        apt-get install -y "$package"
        echo "$package installation completed"
    fi
done
```

### Explanation

This is a real-world automation pattern: check root → loop through packages → install only what's missing.

| Element | Meaning |
|---------|---------|
| `$EUID` | **Effective User ID** of the current user. `0` = root. |
| `-ne` | Not equal. |
| `dpkg -s` | Show status of a Debian package. Returns `0` if installed, non-zero otherwise. |
| `&>` | Redirect **both** stdout and stderr to the same place. |
| `/dev/null` | The Linux "black hole" — anything written here is discarded. |
| `apt-get install -y` | Install a package; `-y` auto-answers "yes" to all prompts (so it doesn't hang in automation). |

### Why redirect to `/dev/null`?

We don't care about `dpkg -s`'s output — we only care about its exit code (installed or not). Sending the noise to `/dev/null` keeps the script's output clean.

### Screenshots

![Install packages — script](screenshots/day17/task4-install-script.png)

![Install packages — output](screenshots/day17/task4-install-output.png)

---

## Task 5 — Error Handling (`safe_script.sh`)

### Script

```bash
#!/bin/bash

set -e

mkdir /tmp/devops-test || echo "Directory already exists"
cd /tmp/devops-test || echo "Cannot enter directory"
touch testfile.txt || echo "Cannot create file"

echo "Script completed successfully"
```

### Explanation

| Element | Meaning |
|---------|---------|
| `set -e` | **Exit immediately** if any command returns non-zero (fails). Essential for production scripts — you don't want a deployment to keep going after a step blew up. |
| `mkdir` | Make a directory. |
| `cd` | Change directory. |
| `touch` | Create an empty file (or update the timestamp if it exists). |
| `\|\|` | **Logical OR** — runs the right side only if the left side *fails*. |
| `/tmp` | Linux's temporary directory — common scratch space for testing. |

### How `||` interacts with `set -e`

`set -e` says "exit on any failure." But `||` gives Bash an explicit fallback, so the failure is "handled" — `set -e` won't trip on it. This is the standard idiom for graceful degradation:

```bash
mkdir test || echo "failed"   # if mkdir fails, echo; script keeps running
```

### Screenshots

![Error handling script](screenshots/day17/task5-error-handling-script.png)

![Error handling output](screenshots/day17/task5-error-handling-output.png)

---

## Day 17 — Files Created

| File | Purpose |
|------|---------|
| `for_loop.sh` | Loop through a list |
| `count.sh` | Loop through numeric range |
| `countdown.sh` | While loop with arithmetic |
| `greet.sh` | Argument validation |
| `args_demo.sh` | Display all argument variables |
| `install_packages.sh` | Automated package installation |
| `safe_script.sh` | Error handling practice |

---

# Day 18 — Functions & Intermediate Concepts

## Overview

Day 18 was about writing **reusable, production-grade** Bash:

- Defining and calling functions
- Passing arguments into functions
- Local vs global variables
- Strict mode (`set -euo pipefail`)
- Building a real system monitoring tool by composing functions

This is where Bash scripting starts feeling like *real* programming.

---

## Task 1 — Basic Functions (`functions.sh`)

### Script

```bash
#!/bin/bash

greet() {
    echo "Hello, $1!"
}

add() {
    sum=$(( $1 + $2 ))
    echo "Sum: $sum"
}

greet "Aftab"
add 10 20
```

### Explanation

A **function** is a named, reusable block of code. Benefits:

- Avoid repeating yourself
- Organize scripts into logical pieces
- Improve readability
- Make scripts modular and testable

### Syntax

```bash
function_name() {
    commands
}
```

### Calling functions

```bash
greet "Aftab"     # calls greet with "Aftab" as $1
add 10 20         # calls add with $1=10, $2=20
```

### Function arguments

Inside a function, `$1`, `$2`, `$3`, ... refer to **arguments passed to that function** — *not* to the script's command-line arguments. They shadow the script-level ones.

| Call | Inside the function |
|------|---------------------|
| `add 10 20` | `$1 = 10`, `$2 = 20` |

### Arithmetic Expansion

```bash
sum=$(( $1 + $2 ))
```

Without `$(( ))`, Bash would concatenate strings: `"10" + "20"` becomes `"10 + 20"` literally. The double-parens force math.

### Screenshots

![Sum function — script](screenshots/day18/task1-sum-function-script.png)

![Sum function — output](screenshots/day18/task1-sum-function-output.png)

---

## Task 2 — Functions for System Checks (`disk_check.sh`)

### Script

```bash
#!/bin/bash

check_disk() {
    echo "===== DISK USAGE ====="
    df -h /
}

check_memory() {
    echo "===== MEMORY USAGE ====="
    free -h
}

check_disk
check_memory
```

### Explanation

Two single-purpose functions that wrap real Linux monitoring commands.

| Command | Meaning |
|---------|---------|
| `df` | **Disk Filesystem** — shows mounted filesystems and their disk usage. |
| `-h` | **Human-readable** — sizes in K, M, G, T instead of raw bytes. |
| `/` | The root filesystem. |
| `free` | Show memory usage (RAM, used, free, swap). |

### Screenshot

![Loop with function script](screenshots/day18/task2-loop-function-script.png)

---

## Task 3 — Strict Mode (`strict_demo.sh`)

### Script

```bash
#!/bin/bash

set -euo pipefail

echo "Testing strict mode"
echo "$UNDEFINED_VAR"
echo "This line will not execute"
```

### Explanation

`set -euo pipefail` is the **most important line in production Bash**. It combines three safety flags:

| Flag | What it does |
|------|--------------|
| `set -e` | **Exit immediately** if any command fails (non-zero exit). |
| `set -u` | **Treat undefined variables as errors.** Without this, `$TYPO` silently expands to empty string and bugs hide. |
| `set -o pipefail` | A pipeline (`cmd1 \| cmd2`) returns failure if **any** command in it fails — not just the last one. |

### Why pipefail matters

```bash
false | true
```

- **Without `pipefail`**: returns success (because `true` is the last command).
- **With `pipefail`**: returns failure (because `false` failed earlier in the pipe).

This matters huge for monitoring and CI/CD — you don't want a silent failure halfway through a pipeline to look like success.

### What happens with this script

When `set -u` is active and `$UNDEFINED_VAR` is referenced:

```text
strict_demo.sh: line 5: UNDEFINED_VAR: unbound variable
```

The script halts. The third `echo` never runs. That's the whole point.

### Screenshot

![Strict mode — unbound variable error](screenshots/day18/task3-strict-mode.png)

---

## Task 4 — Local vs Global Variables (`local_demo.sh`)

### Script

```bash
#!/bin/bash

global_var="I am global"

test_local() {
    local local_var="I am local"
    echo "$local_var"
}

test_global() {
    normal_var="I am normal"
}

test_local
echo "$local_var"     # blank — local_var doesn't exist out here

test_global
echo "$normal_var"    # works — normal_var leaked into global scope
```

### Explanation

By default, **every variable in Bash is global** — even ones created inside a function. That's a footgun.

The `local` keyword fixes that: a variable declared `local` exists *only* inside that function.

| Declaration | Scope |
|-------------|-------|
| `local x="foo"` | Function-only |
| `x="foo"` (inside or outside a function) | Global |

### Why this matters

- Avoid name collisions across functions
- Make functions reusable and self-contained
- Prevent surprise side effects in large scripts

### Output

```text
I am local

I am normal
```

The blank line is `$local_var` not existing in global scope.

### Screenshot

![Local and global variables](screenshots/day18/task4-local-global-variables.png)

---

## Task 5 — System Information Reporter (`system_info.sh`)

The capstone for Day 18 — a real monitoring tool built from composed functions running under strict mode.

### Script

```bash
#!/bin/bash

set -euo pipefail

print_header() {
    echo
    echo "=============================="
    echo "$1"
    echo "=============================="
}

system_info() {
    print_header "SYSTEM INFORMATION"
    hostname
    uname -a
}

uptime_info() {
    print_header "SYSTEM UPTIME"
    uptime
}

disk_usage() {
    print_header "TOP DISK USAGE"
    du -ah / 2>/dev/null | sort -rh | head -5
}

memory_usage() {
    print_header "MEMORY USAGE"
    free -h
}

cpu_processes() {
    print_header "TOP CPU PROCESSES"
    ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head -6
}

main() {
    system_info
    uptime_info
    disk_usage
    memory_usage
    cpu_processes
}

main
```

### Command Reference

| Command | What it shows |
|---------|---------------|
| `hostname` | The machine's hostname |
| `uname -a` | Kernel, hostname, architecture, OS info — all at once |
| `uptime` | How long the system has been running, load average, logged-in users |
| `du` | **Disk Usage** — size of files and directories |
| `free` | Memory usage (RAM + swap) |
| `ps` | Running processes |

### Flag Reference

| Flag / Symbol | Meaning |
|---------------|---------|
| `-a` | `du`: include all files (not just directories) |
| `-h` | Human-readable sizes (used by `du`, `free`, `df`) |
| `2>/dev/null` | Redirect **stderr** (file descriptor `2`) to the black hole — hides "Permission denied" noise from `du /` |
| `\|` | Pipe — send stdout of one command into stdin of the next |
| `sort -rh` | `-r` = reverse, `-h` = human-readable sort (so `1G > 999M`) |
| `head -5` | First 5 lines only |
| `ps -eo` | `-e` = every process, `-o` = custom output columns |
| `--sort=-%cpu` | Sort by `%cpu` descending (the leading `-` means descending) |

### The `main` pattern

Notice how the script defines a `main` function and calls it at the bottom. This is a borrowed-from-real-programming idiom that makes the script:

- Easier to read top-to-bottom (definitions first, execution last)
- Easier to test (you can source the script and call individual functions)
- Easier to extend (just add another function and a line in `main`)

### Screenshots

![System info — full script](screenshots/day18/task5-system-info-script.png)

![System info — final output](screenshots/day18/task5-system-info-output.png)

---

## Day 18 — Files Created

| File | Purpose |
|------|---------|
| `functions.sh` | Basic function syntax and arguments |
| `disk_check.sh` | Disk and memory monitoring via functions |
| `strict_demo.sh` | Strict mode demonstration |
| `local_demo.sh` | Local vs global variable scope |
| `system_info.sh` | Production-style system monitoring tool |

---

# Day 19 — Project: Log Rotation, Backup & Crontab

## Overview

Day 19 was the first **real-world mini-project** — combining everything from Days 16–18 into production-style scripts you'd actually find on a Linux server:

- A log rotation script that compresses old logs and deletes ancient ones
- A backup script that creates timestamped tar.gz archives and prunes old backups
- An orchestrator script that combines both with timestamped dual-channel logging
- Cron entries to schedule all of them

The shell concepts here aren't new — they're all from Days 16–18. The leap is **composition**: chaining together strict mode, argument validation, `find -mtime`, `tar -czf`, exit codes, `if/else`, functions, and `tee` into something that runs unattended in production.

---

## Setup — Sandbox Environment

Before writing any scripts, I built a sandbox so testing wouldn't touch real `/var/log` contents.

```bash
mkdir -p ~/2026/day-19
mkdir -p ~/test-logs
cd ~/test-logs

# Fresh files (today)
touch app.log error.log access.log

# Files we'll backdate to 10 days old (rotation target)
touch old1.log old2.log old3.log

# Files we'll backdate to 40 days old (deletion target)
touch ancient1.log.gz ancient2.log.gz

# Backdate using touch -d
touch -d "10 days ago" old1.log old2.log old3.log
touch -d "40 days ago" ancient1.log.gz ancient2.log.gz

ls -la --time-style=full-iso
```

### Key Trick: `touch -d`

| Command / Flag | Meaning |
|----------------|---------|
| `touch -d "10 days ago"` | The `-d` flag lets you set a file's modification time to any date. Accepts human strings like `"10 days ago"` or `"2025-01-15"`. This is how you test "older than N days" logic without waiting. |
| `--time-style=full-iso` | Show full ISO timestamps in `ls` so backdating can be verified. |

### Screenshot

![Sandbox setup with backdated files](screenshots/day19/setup-sandbox.png)

---

## Task 1 — Log Rotation Script (`log_rotate.sh`)

### Goal

1. Takes a log directory as an argument
2. Compresses `.log` files older than 7 days using `gzip`
3. Deletes `.gz` files older than 30 days
4. Prints how many files were compressed and deleted
5. Exits with an error if the directory doesn't exist

### Script

```bash
#!/bin/bash

# ===========================================
# Log Rotation Script
# Usage: ./log_rotate.sh /path/to/log/dir
# ===========================================

set -euo pipefail

# ---- Step 1: Check we got an argument ----
if [ -z "${1:-}" ]; then
    echo "Usage: $0 <log_directory>"
    exit 1
fi

LOG_DIR="$1"

# ---- Step 2: Check the directory actually exists ----
if [ ! -d "$LOG_DIR" ]; then
    echo "Error: Directory '$LOG_DIR' does not exist"
    exit 1
fi

echo "===== Log Rotation Starting ====="
echo "Target directory: $LOG_DIR"
echo

# ---- Step 3: Compress .log files older than 7 days ----
echo "Compressing .log files older than 7 days..."

compressed_count=$(find "$LOG_DIR" -type f -name "*.log" -mtime +7 | wc -l)

find "$LOG_DIR" -type f -name "*.log" -mtime +7 -exec gzip {} \;

echo "Compressed: $compressed_count file(s)"
echo

# ---- Step 4: Delete .gz files older than 30 days ----
echo "Deleting .gz files older than 30 days..."

deleted_count=$(find "$LOG_DIR" -type f -name "*.gz" -mtime +30 | wc -l)

find "$LOG_DIR" -type f -name "*.gz" -mtime +30 -exec rm -f {} \;

echo "Deleted: $deleted_count file(s)"
echo

echo "===== Log Rotation Complete ====="
```

### The `find` Command — Breakdown

```bash
find "$LOG_DIR" -type f -name "*.log" -mtime +7 -exec gzip {} \;
```

| Piece | Meaning |
|-------|---------|
| `find` | The Linux file search command (way more powerful than `ls`) |
| `"$LOG_DIR"` | Where to start searching |
| `-type f` | Match files only (`f`), not directories (`d`) |
| `-name "*.log"` | Match filenames ending in `.log` (`*` is wildcard) |
| `-mtime +7` | **M**odified **t**ime more than 7 days ago. `+N` = older than N. `-N` = newer than N. Plain `N` = exactly N. |
| `-exec gzip {} \;` | For each match, run `gzip` on it |
| `{}` | Placeholder — replaced with each matching filename |
| `\;` | Tells `find` "the command ends here." The backslash escapes the semicolon from the shell. |

### Counting With Command Substitution

```bash
compressed_count=$(find ... | wc -l)
```

- `$(...)` → command substitution, captures output into a variable
- `wc -l` → word count with `-l` for line count
- We count *before* the gzip because after gzipping, the files become `.log.gz` and wouldn't match the `*.log` pattern anymore

### `rm -f` Flag

| Flag | Meaning |
|------|---------|
| `rm` | Remove (delete) |
| `-f` | **F**orce. Don't prompt, don't error if missing. Critical in scripts so they don't hang. |

### Argument Defaulting Trick

```bash
if [ -z "${1:-}" ]; then
```

The `${1:-}` is "use `$1` if it exists, otherwise default to empty string." We need this because `set -u` would otherwise crash on an unset `$1` *before* we even reach the `-z` test. The `:-` is the **default-if-unset** operator.

### Screenshot

![Task 1 — log rotation execution with all test cases](screenshots/day19/task1-log-rotate-execution.png)

---

## Task 2 — Server Backup Script (`backup.sh`)

### Goal

1. Takes a source directory and backup destination as arguments
2. Creates a timestamped `.tar.gz` archive (e.g., `backup-2026-05-27.tar.gz`)
3. Verifies the archive was created
4. Prints archive name and size
5. Deletes backups older than 14 days
6. Handles errors — exits if source doesn't exist

### Script

```bash
#!/bin/bash

# ===========================================
# Server Backup Script
# Usage: ./backup.sh <source_dir> <destination_dir>
# ===========================================

set -euo pipefail

# ---- Step 1: Check we got both arguments ----
if [ $# -ne 2 ]; then
    echo "Usage: $0 <source_directory> <destination_directory>"
    exit 1
fi

SOURCE_DIR="$1"
DEST_DIR="$2"

# ---- Step 2: Verify source exists ----
if [ ! -d "$SOURCE_DIR" ]; then
    echo "Error: Source directory '$SOURCE_DIR' does not exist"
    exit 1
fi

# ---- Step 3: Make sure destination exists ----
mkdir -p "$DEST_DIR"

# ---- Step 4: Build a timestamped archive filename ----
TIMESTAMP=$(date +%Y-%m-%d)
ARCHIVE_NAME="backup-${TIMESTAMP}.tar.gz"
ARCHIVE_PATH="${DEST_DIR}/${ARCHIVE_NAME}"

echo "===== Backup Starting ====="
echo "Source:      $SOURCE_DIR"
echo "Destination: $DEST_DIR"
echo "Archive:     $ARCHIVE_NAME"
echo

# ---- Step 5: Create the archive ----
echo "Creating archive..."
tar -czf "$ARCHIVE_PATH" -C "$(dirname "$SOURCE_DIR")" "$(basename "$SOURCE_DIR")"

# ---- Step 6: Verify the archive was created ----
if [ ! -f "$ARCHIVE_PATH" ]; then
    echo "Error: Archive was not created"
    exit 1
fi

ARCHIVE_SIZE=$(du -h "$ARCHIVE_PATH" | cut -f1)
echo "Archive created successfully"
echo "Name: $ARCHIVE_NAME"
echo "Size: $ARCHIVE_SIZE"
echo

# ---- Step 7: Delete backups older than 14 days ----
echo "Cleaning up old backups (older than 14 days)..."
old_count=$(find "$DEST_DIR" -type f -name "backup-*.tar.gz" -mtime +14 | wc -l)
find "$DEST_DIR" -type f -name "backup-*.tar.gz" -mtime +14 -exec rm -f {} \;
echo "Deleted: $old_count old backup(s)"
echo

echo "===== Backup Complete ====="
```

### Building the Timestamp

```bash
TIMESTAMP=$(date +%Y-%m-%d)
```

| Format Code | Meaning | Example |
|-------------|---------|---------|
| `%Y` | 4-digit year | `2026` |
| `%m` | 2-digit month | `05` |
| `%d` | 2-digit day | `27` |
| `%H` | Hour (24-hour) | `14` |
| `%M` | Minutes | `30` |
| `%S` | Seconds | `45` |

The `+` at the start tells `date` "format string coming."

### The `tar` Command — Breakdown

```bash
tar -czf "$ARCHIVE_PATH" -C "$(dirname "$SOURCE_DIR")" "$(basename "$SOURCE_DIR")"
```

| Piece | Meaning |
|-------|---------|
| `tar` | **T**ape **ar**chive — bundles files into one archive |
| `-c` | **C**reate a new archive |
| `-z` | Compress with **gz**ip |
| `-f` | **F**ilename — next argument is the archive's output filename |
| `-C <dir>` | **C**hange into this directory before archiving |
| `dirname /root/test-source` | Returns `/root` (the parent path) |
| `basename /root/test-source` | Returns `test-source` (just the final name) |

**Why the `dirname`/`basename` dance:** A naive `tar -czf backup.tar.gz /root/test-source` produces an archive containing the *full path* `root/test-source/file1.txt`. Ugly, plus a security risk when extracting elsewhere. By `-C`-ing into the parent first and archiving just the folder name, you get clean **relative paths** like `test-source/file1.txt`. Standard professional practice.

### Reading File Size

```bash
ARCHIVE_SIZE=$(du -h "$ARCHIVE_PATH" | cut -f1)
```

| Piece | Meaning |
|-------|---------|
| `du -h <file>` | Disk usage of that file in human-readable form (e.g., `4.0K<TAB>/path/to/file`) |
| `\|` | Pipe — sends output to next command |
| `cut -f1` | Take field 1. By default `cut` splits on tabs, so we get just `4.0K` without the filename. |

### Verifying an Archive

```bash
tar -tzf ~/test-backups/backup-*.tar.gz
```

| Flag | Meaning |
|------|---------|
| `-t` | **T**est / list contents (don't extract) |
| `-z` | Archive is gzipped |
| `-f` | Specify the file |

### Screenshot

![Task 2 — backup script with all test cases and archive verification](screenshots/day19/task2-backup-execution.png)

The archive listing at the bottom shows clean relative paths (`test-source/file1.txt`, `test-source/subfolder/nested.txt`) — proof that the `-C` + `basename` trick worked.

---

## Task 3 — Crontab Understanding & Entries

### What Cron Is

`cron` is a Linux background service (daemon) that has one job: **wake up every minute, check a schedule, and run anything that's due**. It's been around since the 1970s and runs on every production Linux server.

Each user has their own **crontab** (cron table). You don't edit it directly — you use the `crontab` command:

| Command | Purpose |
|---------|---------|
| `crontab -l` | **L**ist current user's crontab |
| `crontab -e` | **E**dit current user's crontab |
| `crontab -r` | **R**emove current user's crontab |

### The 5 Time Fields

```
* * * * *  command_to_run
│ │ │ │ │
│ │ │ │ └── Day of week  (0-7, where 0 AND 7 = Sunday)
│ │ │ └──── Month        (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour         (0-23, 24-hour clock)
└────────── Minute       (0-59)
```

A `*` means "every value" in that field.

### Operators Available in Any Field

| Operator | Example | Meaning |
|----------|---------|---------|
| `*` | `* * * * *` | Every value |
| `,` | `0 9,12,18 * * *` | List: at 9 AM, 12 PM, and 6 PM |
| `-` | `0 9-17 * * *` | Range: every hour from 9 AM through 5 PM |
| `/` | `*/15 * * * *` | Step: every 15 minutes |

### Common Translations

| Cron Line | Plain English |
|-----------|---------------|
| `0 2 * * *` | Daily at 2:00 AM |
| `30 4 * * *` | Daily at 4:30 AM |
| `0 3 * * 0` | At 3:00 AM on Sunday only |
| `*/5 * * * *` | Every 5 minutes |
| `0 */2 * * *` | At minute 0 of every 2nd hour |
| `0 0 1 * *` | Midnight on day 1 of every month |
| `15 14 * * 1-5` | 2:15 PM on weekdays (Mon–Fri) |
| `30 1 * * 1-5` | 1:30 AM on weekdays only |

### The Three Drafted Entries

#### Entry 1 — Log Rotation Daily at 2 AM

```
0 2 * * *  log_rotate.sh
```

![Cron entry — log rotation](screenshots/day19/task3-cron-log-rotate.png)

#### Entry 2 — Backup Every Sunday at 3 AM

```
0 3 * * 0  backup.sh
```

![Cron entry — backup](screenshots/day19/task3-cron-backup.png)

#### Entry 3 — Health Check Every 5 Minutes

```
*/5 * * * *  health_check.sh
```

![Cron entry — health check](screenshots/day19/task3-cron-healthcheck.png)

### Critical Gotcha — Cron's Empty Environment

When you run `./log_rotate.sh` interactively, three things happen automatically:

1. Your current working directory is set
2. `~` expands to your home directory
3. `$PATH` includes common binary locations like `/usr/bin`

**Cron has NONE of this.** It runs your script in a near-empty environment with no terminal. If you put `log_rotate.sh` in cron, it'll silently fail because cron can't find it. The fix:

- Always use **absolute paths** in cron
- Always **redirect output to a log file** so you can debug

### Production-Ready Cron Entries

```cron
# Daily log rotation at 2 AM
0 2 * * *  /root/2026/day-19/log_rotate.sh /root/test-logs >> /root/2026/day-19/cron.log 2>&1

# Weekly backup on Sunday at 3 AM
0 3 * * 0  /root/2026/day-19/backup.sh /root/test-source /root/test-backups >> /root/2026/day-19/cron.log 2>&1

# Health check every 5 minutes
*/5 * * * *  /root/2026/day-19/health_check.sh >> /root/2026/day-19/cron.log 2>&1
```

The redirect `>> file 2>&1` means "append stdout to file, AND send stderr to wherever stdout is going." Order matters — flipping them breaks it.

---

## Task 4 — Scheduled Maintenance Script (`maintenance.sh`)

### Goal

Combine log rotation + backup into one orchestrator that:
1. Calls both scripts in sequence
2. Logs everything to `/var/log/maintenance.log` with timestamps
3. Handles failures gracefully (one failure doesn't stop the other)
4. Gets scheduled to run daily at 1 AM

### Script

```bash
#!/bin/bash

# ===========================================
# Scheduled Maintenance Script
# Runs log rotation + backup, logs everything
# ===========================================

set -euo pipefail

# ---- Configuration ----
SCRIPT_DIR="/root/2026/day-19"
LOG_DIR="/root/test-logs"
SOURCE_DIR="/root/test-source"
BACKUP_DIR="/root/test-backups"
MAINT_LOG="/var/log/maintenance.log"

# ---- Helper: log a message with a timestamp ----
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') | $1" | tee -a "$MAINT_LOG"
}

# ---- Make sure the maintenance log exists ----
touch "$MAINT_LOG"

log "===== Maintenance Run Started ====="

# ---- Run Log Rotation ----
log "--- Running log rotation ---"
if "$SCRIPT_DIR/log_rotate.sh" "$LOG_DIR" >> "$MAINT_LOG" 2>&1; then
    log "Log rotation: SUCCESS"
else
    log "Log rotation: FAILED"
fi

# ---- Run Backup ----
log "--- Running backup ---"
if "$SCRIPT_DIR/backup.sh" "$SOURCE_DIR" "$BACKUP_DIR" >> "$MAINT_LOG" 2>&1; then
    log "Backup: SUCCESS"
else
    log "Backup: FAILED"
fi

log "===== Maintenance Run Complete ====="
echo
```

### The `log()` Function — Dual-Channel Logging

```bash
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') | $1" | tee -a "$MAINT_LOG"
}
```

Breaking this single line into pieces:

| Piece | Meaning |
|-------|---------|
| `$(date '+%Y-%m-%d %H:%M:%S')` | Captures current timestamp like `2026-05-27 14:30:45` |
| `\| $1` *(inside quotes)* | Literal pipe character as visual separator, then the message passed in |
| `\|` *(outside quotes)* | Real shell pipe — sends echo's output to the next command |
| `tee` | **Splits an input stream into two destinations.** Named after a plumbing T-pipe. Output goes to **both** terminal AND file. |
| `-a` | **A**ppend (don't overwrite). Without it, every `log` call would erase the file. |

**One call like `log "Backup started"` produces a timestamped line that's visible on screen AND saved to the log file at the same time.** This dual-channel pattern is how real ops scripts work.

### The `if` Wrapper — Failure Isolation

```bash
if "$SCRIPT_DIR/log_rotate.sh" "$LOG_DIR" >> "$MAINT_LOG" 2>&1; then
    log "Log rotation: SUCCESS"
else
    log "Log rotation: FAILED"
fi
```

| Piece | Meaning |
|-------|---------|
| `if <command>; then` | Run the command; if exit code is 0, do `then`; otherwise do `else` |
| `>> "$MAINT_LOG"` | Append the sub-script's stdout to the log file |
| `2>&1` | Also send stderr to the same place |

**Why this overrides `set -e`:** Normally `set -e` would kill the script the instant `log_rotate.sh` failed. But because we wrapped it in an `if`, Bash treats the failure as "handled" — meaning we still attempt the backup even if log rotation broke. Intentional resilience.

### Cron Entry for Maintenance

```cron
# Run scheduled maintenance daily at 1 AM
0 1 * * *  /root/2026/day-19/maintenance.sh >> /root/2026/day-19/cron.log 2>&1
```

### Screenshot

![Task 4 — maintenance.sh execution and log file contents](screenshots/day19/task4-maintenance-run.png)

The screenshot shows the dual-channel logging clearly — top half is the clean timestamped terminal output, bottom half is the full detailed contents of `/var/log/maintenance.log` (with `log_rotate.sh` and `backup.sh`'s full output interleaved with the maintenance summary lines).

---

## Cron Entries Summary

All four cron entries from this project, in production-ready form:

```cron
# Daily log rotation at 2 AM
0 2 * * *  /root/2026/day-19/log_rotate.sh /root/test-logs >> /root/2026/day-19/cron.log 2>&1

# Weekly backup on Sunday at 3 AM
0 3 * * 0  /root/2026/day-19/backup.sh /root/test-source /root/test-backups >> /root/2026/day-19/cron.log 2>&1

# Health check every 5 minutes
*/5 * * * *  /root/2026/day-19/health_check.sh >> /root/2026/day-19/cron.log 2>&1

# Combined maintenance daily at 1 AM
0 1 * * *  /root/2026/day-19/maintenance.sh >> /root/2026/day-19/cron.log 2>&1
```

> **Note:** These entries were drafted and verified syntactically, but not applied to the live crontab. To apply them, run `crontab -e`, paste the entries, save, and they'd be live.

---

## Day 19 — Files Created

| File | Purpose |
|------|---------|
| `log_rotate.sh` | Compress old logs, delete ancient compressed logs |
| `backup.sh` | Create timestamped tar.gz backups, prune old ones |
| `maintenance.sh` | Orchestrator: runs the above two with timestamped logging |
| `day-19-project.md` | Standalone project documentation (also folded into this master README) |

---

# Day 20 — Challenge: Log Analyzer & Report Generator

## Overview

Day 20 is the **bootcamp finale** — a real-world log analyzer that counts errors, finds critical events, ranks the top issues, and emits a daily summary report. This is exactly what SREs do at 3 AM when production breaks.

The script uses the **classic Unix text-processing toolkit**: `grep`, `awk`, `sort`, `uniq`, `sed`, and `wc`. Once you know this pipeline, you can analyze any log on any Linux system anywhere — and the same pattern transfers directly to CloudWatch Logs Insights, Datadog, Splunk, and SQL aggregations.

---

## Setup — Sample Log File

To test the script I built a realistic sample log using a **heredoc**:

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

The sample has 40 lines total: 25 `ERROR`s, 3 `Failed`s, 3 `CRITICAL`s, plus `INFO`/`WARN` noise.

### Screenshot

![Setup — sample log creation](screenshots/day20/setup-sample-log.png)

---

## Exploration — Meet `grep`, `awk`, `sort`, `uniq`

Before writing the script, I explored each tool individually.

### `grep` — Filter lines by pattern

```bash
grep -c "ERROR" sample_log.log              # → 23
grep -c "Failed" sample_log.log             # → 3
grep -cE "ERROR|Failed" sample_log.log      # → 26
grep -n "CRITICAL" sample_log.log           # show with line numbers
```

| Flag | Meaning |
|------|---------|
| `-c` | **C**ount matching lines (don't print them) |
| `-n` | Show line **n**umbers alongside matches |
| `-E` | **E**xtended regex — lets you use `\|` for logical OR |
| `"ERROR\|Failed"` | The `\|` in regex means "this OR that" |

![grep exploration](screenshots/day20/grep-exploration.png)

### `awk` — Text processing language

`awk` splits each line into fields by whitespace. For:
```
2026-05-20 08:01:15 ERROR Connection timed out
```
`awk` sees `$1`=date, `$2`=time, `$3`=level, `$4`/`$5`/`$6`=message words, `$0`=whole line.

The expression `{$1=$2=$3=""; print}` means **"empty the first 3 fields, print the rest"** — strips date/time/level, leaves just the message.

### `sort` and `uniq` — The classic counting duo

- `sort` → sorts alphabetically (needed BEFORE `uniq` because `uniq` only collapses *adjacent* duplicates)
- `uniq -c` → collapse duplicates, prepend count
- `sort -rn` → `-r` reverse, `-n` numeric (so `10` > `9` instead of `10` < `9` alphabetically)
- `head -5` → first 5 lines only

### The Most Important Pipeline in Unix

```bash
grep "ERROR" sample_log.log | awk '{$1=$2=$3=""; print}' | sort | uniq -c | sort -rn | head -5
```

| Step | Purpose |
|------|---------|
| `grep "ERROR"` | **Filter** — keep only ERROR lines |
| `awk '{$1=$2=$3=""; print}'` | **Project** — drop date/time/level, keep message |
| `sort` | **Group** — adjacent identical lines |
| `uniq -c` | **Count** — collapse duplicates with counts |
| `sort -rn` | **Rank** — descending by count |
| `head -5` | **Limit** — top 5 only |

Filter → Project → Group → Count → Rank → Limit. **Master this once, use it forever.** Same recipe applies in CloudWatch Insights, Datadog, Splunk, and SQL (`WHERE` → `SELECT` → `GROUP BY` → `COUNT(*)` → `ORDER BY DESC` → `LIMIT 5`).

![awk-sort-uniq pipeline](screenshots/day20/awk-sort-uniq-pipeline.png)

---

## Task 1 — Input & Validation

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
| `${1:-}` | "Use `$1` if it exists, otherwise empty" — needed because `set -u` would crash on unset `$1` (Day 19 pattern) |
| `-z` | Empty string test |
| `! -f` | NOT a regular file |
| `exit 1` | Exit with failure code |

---

## Task 2 — Error Count

### Code

```bash
ERROR_COUNT=$(grep -cE "ERROR|Failed" "$LOG_FILE" || true)
```

### Why `|| true`?

`grep -c` returns exit code `1` when it finds **zero** matches. Combined with `set -e`, that crashes the script. The `|| true` says "if `grep` returned failure, pretend it succeeded."

**This is the standard pattern for any counting command inside strict-mode scripts.** Also applies to `find` with no matches, `awk` with conditional prints, any command where "zero results" is a valid outcome.

### Result on sample log

```
Total errors: 26   (23 ERRORs + 3 Faileds)
```

---

## Task 3 — Critical Events

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

`grep -n` outputs lines like `14:2026-05-20 08:10:00 ...`. We reformat to `Line 14: 2026-05-20 ...`.

```bash
sed 's/^\([0-9]*\):/Line \1: /'
```

| Piece | Meaning |
|-------|---------|
| `sed` | **S**tream **ed**itor — find & replace on streams |
| `s/pattern/replacement/` | Substitute syntax |
| `^` | Anchor: start of line |
| `\([0-9]*\)` | **Capture group** — saves matched digits as `\1` |
| `Line \1: ` | Replacement uses the captured digits |

### The Bug I Hit (and the Lesson)

My first attempt used `awk -F:` (split on colon) for this. It broke because the timestamp `08:10:00` also contains colons — `awk` miscounted fields and chopped `2026` down to `26`. The `sed` capture-group approach is bulletproof because it only consumes the **first** colon at the start of the line.

**Lesson:** when the delimiter character also appears in the data, use regex capture groups instead of field splitting. Applies everywhere — CSV files with commas in quoted strings, log lines with timestamps, anywhere "separator" is also "content."

### Result on sample log

```
Line 14: 2026-05-20 08:10:00 CRITICAL Disk space below threshold
Line 24: 2026-05-20 08:20:00 CRITICAL Database connection lost
Line 34: 2026-05-20 08:30:44 CRITICAL Service unresponsive
```

---

## Task 4 — Top 5 Error Messages

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
- Result: clean trimmed output

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

| Piece | Meaning |
|-------|---------|
| `{ ... }` | **Command group** — runs enclosed commands as a single block |
| `> "$REPORT_FILE"` | Redirect ALL output from the block to the file in one go |

Way cleaner than appending each line individually with `>>`.

We **reuse** the variables (`$TOTAL_LINES`, `$ERROR_COUNT`, `$TOP_ERRORS`, `$CRITICAL_LINES`) computed earlier — no need to run `grep` twice. **Compute once, use many times.**

### Screenshots

![Script run with all test cases](screenshots/day20/script-run-output.png)

![Report file and archive directory](screenshots/day20/report-and-archive.png)

![Final clean run with correct date formatting](screenshots/day20/final-clean-run.png)

---

## Task 6 — Archive Processed Log

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

## Day 20 — Files Created

| File | Purpose |
|------|---------|
| `log_analyzer.sh` | The analyzer script |
| `sample_log.log` | 40-line synthetic log for testing |
| `log_report_<date>.txt` | Auto-generated report (created by the script) |
| `archive/` | Auto-created directory holding processed logs |
| `day-20-solution.md` | Standalone project documentation |

---

# Master Reference — Flags & Symbols

A consolidated reference for everything covered across Days 16–18.

### Numeric Comparison

| Flag | Meaning |
|------|---------|
| `-gt` | Greater than |
| `-lt` | Less than |
| `-eq` | Equal |
| `-ne` | Not equal |
| `-ge` | Greater than or equal |
| `-le` | Less than or equal |

### String Tests

| Flag | Meaning |
|------|---------|
| `-z` | String is empty |
| `-n` | String is non-empty |
| `=` | Strings are equal |
| `!=` | Strings are not equal |

### File Tests

| Flag | Meaning |
|------|---------|
| `-f` | Regular file |
| `-d` | Directory |
| `-e` | Exists (any type) |
| `-r` | Readable |
| `-w` | Writable |
| `-x` | Executable |

### Special Variables

| Variable | Meaning |
|----------|---------|
| `$0` | Script name |
| `$1`, `$2`, ... | Positional arguments |
| `$#` | Number of arguments |
| `$@` | All arguments (as list) |
| `$*` | All arguments (as single string) |
| `$?` | Exit code of last command |
| `$$` | PID of current shell |
| `$EUID` | Effective User ID (0 = root) |

### Strict Mode

| Flag | Meaning |
|------|---------|
| `set -e` | Exit on any command failure |
| `set -u` | Error on undefined variable |
| `set -o pipefail` | Fail if any command in a pipeline fails |
| `set -euo pipefail` | All three combined (production standard) |

### Redirection

| Symbol | Meaning |
|--------|---------|
| `>` | Redirect stdout (overwrite) |
| `>>` | Redirect stdout (append) |
| `<` | Redirect stdin |
| `2>` | Redirect stderr |
| `&>` | Redirect both stdout and stderr |
| `2>/dev/null` | Discard stderr only |
| `&>/dev/null` | Discard everything |
| `\|` | Pipe stdout into next command |

### Logical Operators

| Operator | Meaning |
|----------|---------|
| `&&` | Run right side only if left side **succeeded** |
| `\|\|` | Run right side only if left side **failed** |
| `!` | Negate a condition |

### Common Command Flags

| Flag | Meaning |
|------|---------|
| `-h` | Human-readable (used by `df`, `du`, `free`, `ls -lh`) |
| `-a` | All (include hidden / include all entries) |
| `-r` | Reverse order (in `sort`) / recursive (in many others) |
| `-y` | Auto-confirm "yes" (used by `apt-get install -y`) |
| `-p` | Prompt (`read -p`) / parents (`mkdir -p`) — context-dependent |
| `--quiet` | Suppress output |
| `-d` | Set timestamp (`touch -d "10 days ago"`) / directory test (`[ -d path ]`) |
| `-f` | **F**orce (`rm -f`) / **F**ilename (`tar -f`) / regular file test (`[ -f path ]`) |

### `find` Command Flags

| Flag | Meaning |
|------|---------|
| `-type f` | Match files only |
| `-type d` | Match directories only |
| `-name "pattern"` | Match filename pattern (supports `*` wildcard) |
| `-mtime +N` | **M**odified **t**ime more than N days ago (older than N) |
| `-mtime -N` | Modified time less than N days ago (newer than N) |
| `-mtime N` | Modified exactly N days ago |
| `-exec cmd {} \;` | Run `cmd` on each match. `{}` = placeholder for the matched filename. `\;` ends the command. |

### `tar` Command Flags

| Flag | Meaning |
|------|---------|
| `-c` | **C**reate a new archive |
| `-x` | E**x**tract an archive |
| `-t` | Lis**t** archive contents (don't extract) |
| `-z` | Compress / decompress with **gz**ip |
| `-f <file>` | Specify the archive **f**ilename |
| `-C <dir>` | **C**hange into directory before archiving (gives clean relative paths) |
| `-v` | Verbose (print each file as it's processed) |

### Useful Standalone Commands

| Command | Purpose |
|---------|---------|
| `tee` | Split a stream — write to both terminal and file (`-a` to append) |
| `wc -l` | Count lines |
| `cut -f1` | Extract field 1 (default delimiter is TAB) |
| `dirname /path/to/file` | Returns `/path/to` (parent path) |
| `basename /path/to/file` | Returns `file` (final name) |
| `date '+%Y-%m-%d'` | Print current date in custom format |
| `touch -d "10 days ago" file` | Backdate a file's timestamp |
| `head -N` | First N lines |
| `tail -N` | Last N lines |

### `grep` Command Flags

| Flag | Meaning |
|------|---------|
| `-c` | **C**ount matching lines only |
| `-n` | Show line **n**umbers alongside matches |
| `-E` | **E**xtended regex (lets you use `\|` for OR) |
| `-i` | Case-**i**nsensitive |
| `-v` | In**v**ert match (lines that DON'T match) |
| `-r` | **R**ecursive (search through directories) |
| `-l` | List only filenames containing matches |
| `-o` | Print **o**nly the matched portion (not the whole line) |

### `awk` Patterns

| Pattern | Meaning |
|---------|---------|
| `'{print $1}'` | Print field 1 (default split on whitespace) |
| `'{print $NF}'` | Print last field (`NF` = number of fields) |
| `'{$1=$2=$3=""; print}'` | Empty first 3 fields, print the rest |
| `'{sub(/^ +/, ""); print}'` | Trim leading spaces |
| `'/pattern/ {action}'` | Run action only on lines matching pattern |
| `-F:` | Use `:` as field separator instead of whitespace |

### `sed` Substitution

```bash
sed 's/pattern/replacement/'    # replace first occurrence per line
sed 's/pattern/replacement/g'   # replace ALL occurrences per line
sed -i 's/old/new/g' file       # edit file in-place
sed 's/^\([0-9]*\):/Line \1: /' # capture group with \1
```

| Element | Meaning |
|---------|---------|
| `s/.../.../` | **S**ubstitute syntax |
| `^` | Anchor: start of line |
| `$` | Anchor: end of line |
| `\(...\)` | Capture group (referenced as `\1`, `\2`, ...) |
| `g` flag | **G**lobal (all occurrences, not just first) |
| `-i` | **I**n-place edit |

### `sort` and `uniq` Combo

```bash
sort | uniq -c | sort -rn | head -N
```

| Flag | Meaning |
|------|---------|
| `sort` | Sort alphabetically (default) |
| `sort -n` | **N**umeric sort |
| `sort -r` | **R**everse order |
| `sort -rn` | Numeric descending (top-N pattern) |
| `sort -u` | Unique (sort + dedupe in one step) |
| `uniq -c` | Collapse adjacent duplicates, prepend **c**ount |
| `uniq -d` | Show only **d**uplicates |
| `uniq -u` | Show only **u**nique lines |

### Heredoc Syntax

```bash
cat > file.txt << 'EOF'
some content
$variables NOT expanded (because EOF is quoted)
EOF

cat > file.txt << EOF
some content
$variables ARE expanded (because EOF is unquoted)
EOF
```

### `date` Format Codes

| Code | Meaning | Example |
|------|---------|---------|
| `%Y` | 4-digit year | `2026` |
| `%m` | 2-digit month | `05` |
| `%d` | 2-digit day | `27` |
| `%H` | Hour (24-hour) | `14` |
| `%M` | Minutes | `30` |
| `%S` | Seconds | `45` |

### Cron Syntax

```
* * * * *  command
│ │ │ │ │
│ │ │ │ └── Day of week  (0-7, where 0 AND 7 = Sunday)
│ │ │ └──── Month        (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour         (0-23)
└────────── Minute       (0-59)
```

| Operator | Example | Meaning |
|----------|---------|---------|
| `*` | `* * * * *` | Every value |
| `,` | `0 9,12,18 * * *` | List (at 9, 12, 18) |
| `-` | `0 9-17 * * *` | Range (9 through 17) |
| `/` | `*/15 * * * *` | Step (every 15) |

| Command | Purpose |
|---------|---------|
| `crontab -l` | List current user's crontab |
| `crontab -e` | Edit current user's crontab |
| `crontab -r` | Remove current user's crontab |

**Always in cron jobs:** absolute paths + redirect output to a log file (cron has no terminal, no `~`, and a near-empty `$PATH`).

### Exit Codes

| Code | Meaning |
|------|---------|
| `0` | Success |
| Non-zero | Failure |
| `1` | Generic error (common convention) |
| `2` | Misuse of shell builtins |
| `127` | Command not found |

---

## Repository Structure

```
.
├── README.md
├── scripts/
│   ├── day19/
│   │   ├── log_rotate.sh
│   │   ├── backup.sh
│   │   └── maintenance.sh
│   └── day20/
│       ├── log_analyzer.sh
│       └── sample_log.log
└── screenshots/
    ├── day16/
    │   ├── task1-hello-script.png
    │   ├── task2-variables.png
    │   ├── task3-read-input.png
    │   ├── task4a-number-check.png
    │   ├── task4b-file-check.png
    │   ├── task5-ssh-service-check.png
    │   └── task5-complete.png
    ├── day17/
    │   ├── task1-for-loop-script.png
    │   ├── task1-for-loop-output.png
    │   ├── task1-for-loop-number-script.png
    │   ├── task1-for-loop-number-output.png
    │   ├── task2-while-loop-script.png
    │   ├── task2-while-loop-output.png
    │   ├── task3-greet-script.png
    │   ├── task3-greet-output.png
    │   ├── task3-arguments-script.png
    │   ├── task3-arguments-output.png
    │   ├── task4-install-script.png
    │   ├── task4-install-output.png
    │   ├── task5-error-handling-script.png
    │   └── task5-error-handling-output.png
    ├── day18/
    │   ├── task1-sum-function-script.png
    │   ├── task1-sum-function-output.png
    │   ├── task2-loop-function-script.png
    │   ├── task3-strict-mode.png
    │   ├── task4-local-global-variables.png
    │   ├── task5-system-info-script.png
    │   └── task5-system-info-output.png
    ├── day19/
    │   ├── setup-sandbox.png
    │   ├── task1-log-rotate-execution.png
    │   ├── task2-backup-execution.png
    │   ├── task3-cron-log-rotate.png
    │   ├── task3-cron-backup.png
    │   ├── task3-cron-healthcheck.png
    │   └── task4-maintenance-run.png
    └── day20/
        ├── setup-sample-log.png
        ├── grep-exploration.png
        ├── awk-sort-uniq-pipeline.png
        ├── script-run-output.png
        ├── report-and-archive.png
        └── final-clean-run.png
```

---

## Progress Log

| Day | Topic | Status |
|-----|-------|--------|
| 16 | Shell Scripting Basics | ✅ Complete |
| 17 | Loops, Arguments & Error Handling | ✅ Complete |
| 18 | Functions & Intermediate Concepts | ✅ Complete |
| 19 | Project — Log Rotation, Backup & Crontab | ✅ Complete |
| 20 | Challenge — Log Analyzer & Report Generator | ✅ Complete |

**🎉 Bootcamp Complete — 5/5 days delivered with documentation, screenshots, and production-style scripts.**

---

## Key Takeaways

1. **Day 16** laid the foundation — shebang, variables, quoting, conditionals, file checks, services.
2. **Day 17** added automation primitives — loops, command-line arguments, package management, error handling.
3. **Day 18** brought structure — functions, strict mode, local scope, and composing real monitoring tools.
4. **Day 19** was about **composition** — taking everything from 16–18 and building production-style scripts (log rotation, backup, scheduled maintenance) that real Linux servers run, scheduled with cron, with timestamped dual-channel logging via `tee`.
5. **Day 20** introduced the **Unix text-processing toolkit** (`grep`, `awk`, `sort`, `uniq`, `sed`) and the canonical filter → project → group → count → rank → limit pipeline. The same mental model drives CloudWatch Logs Insights, Datadog log search, Splunk SPL, and SQL aggregations.

## Conclusion

Five days, five projects, one foundation. The leap from "what's a shell script" to "I can write a production-grade log analyzer with proper error handling, strict mode, dual-channel logging, and graceful zero-result handling" — that's the leap this bootcamp delivered.

More importantly, **the patterns transfer**. The Unix philosophy of small, focused, composable tools is the same philosophy underlying Kubernetes, Lambda functions, Terraform modules, and microservices. The `find | grep | awk | sort | uniq` pipeline is the same pattern as a SQL `SELECT ... WHERE ... GROUP BY ... ORDER BY ... LIMIT`. Strict-mode error handling is the same discipline you'll need writing CI/CD pipelines and Ansible playbooks.

From here, Bash becomes a tool in the toolbox — used when needed, drafted with AI when convenient, and read confidently when inspecting other people's scripts. The fluency budget moves to Terraform, Kubernetes, and Python. But the **patterns** built here? Those compound forever.

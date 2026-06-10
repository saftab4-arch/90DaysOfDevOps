# Day 22 - Git Fundamentals & First Repository

## Overview

This project marks the beginning of my Git learning journey as part of the 90 Days of DevOps challenge.

The goal of this lab was to understand the fundamentals of Git, create a local repository, track file changes, and build a clean commit history using core Git commands.

---

## Objectives

* Install and configure Git
* Create a local Git repository
* Understand the Git workflow
* Stage and commit changes
* Build commit history
* Learn how Git tracks file modifications

---

## Commands Practiced

### Git Configuration

```bash
git config --global user.name "Syed Aftab"
git config --global user.email "your-email@example.com"
```

Configure Git user identity for commits.

### Repository Initialization

```bash
git init
```

Create a new Git repository.

### Check Repository Status

```bash
git status
```

View tracked, untracked, and staged changes.

### Stage Changes

```bash
git add .
```

Add all modified files to the staging area.

### Commit Changes

```bash
git commit -m "Commit message"
```

Create a snapshot of staged changes.

### View Commit History

```bash
git log
```

Display detailed commit history.

```bash
git log --oneline
```

Display compact commit history.

---

## Git Workflow

```text
Working Directory
        ↓
     git add
        ↓
   Staging Area
        ↓
   git commit
        ↓
   Repository History
```

---

## Commit History

Created multiple commits to understand version tracking:

```text
Added git status command
Added git init command
Initial Git repository setup
```

---

## Skills Learned

* Git installation and configuration
* Repository initialization
* Tracking file changes
* Staging files
* Creating commits
* Viewing commit history
* Understanding the Git workflow

---

## Screenshots

* Git repository initialization
* Git status output
* Git add and staging process
* Commit history using git log --oneline

---

## Key Takeaway

Git is the foundation of modern DevOps workflows. Understanding how changes move from the working directory to the staging area and then into the repository is essential before moving on to branching, merging, and collaboration workflows.

# Day 23 - Git Branching & GitHub Integration

## Overview

This lab focused on Git branching and connecting a local Git repository to GitHub.

The objective was to understand how branches allow developers to work independently without affecting the main codebase and to learn how to push repositories and branches to GitHub.

---

## Objectives

* Create and manage Git branches
* Switch between branches
* Create branch-specific commits
* Delete unused branches
* Connect a local repository to GitHub
* Push branches to a remote repository
* Understand the difference between local and remote repositories

---

## Commands Practiced

### View Branches

```bash
git branch
```

Display all local branches.

### Create Branch

```bash
git branch feature-1
```

Create a new branch.

### Switch Branch

```bash
git checkout feature-1
```

Move to an existing branch.

### Create and Switch Branch

```bash
git checkout -b feature-2
```

Create and switch to a branch in one command.

### Modern Branch Switching

```bash
git switch main
```

Switch branches using the newer Git command.

### Delete Branch

```bash
git branch -d feature-2
```

Delete a branch that is no longer needed.

### Configure Remote Repository

```bash
git remote add origin <repository-url>
```

Connect a local repository to GitHub.

### View Remote Configuration

```bash
git remote -v
```

Display configured remote repositories.

### Push Repository

```bash
git push -u origin main
```

Push the local branch to GitHub.

### Push Feature Branch

```bash
git push -u origin feature-1
```

Push a feature branch to GitHub.

---

## Branching Workflow

```text
main
 │
 ├── feature-1
 │     └── Added feature1.txt
 │
 └── feature-2
       └── Test branch (deleted)
```

---

## Skills Learned

* Git branch creation and management
* Switching between branches
* Branch isolation
* GitHub remote configuration
* Pushing code to GitHub
* Working with multiple branches
* Understanding local vs remote repositories

---

## Key Takeaway

Branches allow developers to safely work on new features and experiments without affecting the main codebase. GitHub provides a centralized location for collaboration, code sharing, and version control management.

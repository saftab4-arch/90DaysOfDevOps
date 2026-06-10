# Day 25 – Git Reset vs Revert & Branching Strategies

## Objective

Learn how to safely undo changes in Git using reset and revert, understand recovery with reflog, and explore common branching strategies used by engineering teams.

---

# Git Reset

Git reset moves the branch pointer (HEAD) to an earlier commit.

## git reset --soft

### Command

```bash
git reset --soft HEAD~1
```

### What it does

* Removes commit from history
* Keeps changes staged
* Keeps file changes

### Lab Observation

I created Commit A, Commit B, and Commit C.

After running:

```bash
git reset --soft HEAD~1
```

Commit C disappeared from git log, but the changes remained staged and ready to commit again.

### Use Case

Useful when I want to rewrite a commit message or combine commits without losing work.

---

## git reset --mixed

### Command

```bash
git reset --mixed HEAD~1
```

### What it does

* Removes commit from history
* Removes changes from staging area
* Keeps file changes

### Lab Observation

The commit disappeared, but Git showed modified files that were no longer staged.

### Use Case

Useful when I accidentally committed too early and want to review changes before committing again.

---

## git reset --hard

### Command

```bash
git reset --hard HEAD~1
```

### What it does

* Removes commit from history
* Removes staged changes
* Removes file changes

### Lab Observation

The commit disappeared and file contents reverted back to the previous commit.

### Warning

This is destructive because uncommitted changes are lost.

---

# Git Reflog

### Command

```bash
git reflog
```

### What it does

Shows all locations HEAD has pointed to, including commits removed by reset.

### Lab Observation

After using git reset --hard, my commit disappeared from git log but was still visible in git reflog.

Example:

```text
d1066de Commit C Again
```

Git still remembered the commit.

### Recovery Methods

Restore entire branch:

```bash
git reset --hard <commit-id>
```

Restore only one commit:

```bash
git cherry-pick <commit-id>
```

### Key Lesson

git reflog is Git's recovery mechanism and can save work after accidental resets.

---

# Git Revert

### Command

```bash
git revert <commit-id>
```

### What it does

Creates a new commit that undoes a previous commit.

### Important

Git does NOT delete the original commit.

---

## Reverting Commit Z

History:

```text
Commit Z
Commit Y
Commit X
```

Command:

```bash
git revert 17da2e7
```

Result:

```text
Revert "Commit Z"
Commit Z
Commit Y
Commit X
```

File content changed from:

```text
X
Y
Z
```

to:

```text
X
Y
```

The original commit remained in history.

### Key Lesson

git revert preserves history while undoing changes.

---

## Reverting Commit Y (Middle Commit)

Command:

```bash
git revert dd3ae80
```

Result:

```text
Merge Conflict
```

### Why?

Commit Z depended on changes introduced by Commit Y.

Git could not safely determine how to remove Y without affecting later commits.

### Key Lesson

Reverting older commits is more likely to create conflicts than reverting the most recent commit.

---

# Git Cherry Pick Troubleshooting

### Scenario

I attempted to cherry-pick Fix 2 without first bringing Fix 1.

Git returned a conflict.

### Why?

Fix 2 modified a file that did not exist on the target branch.

The commit depended on earlier work.

### Solution

Cherry-pick commits in dependency order:

```text
Fix 1
Fix 2
Fix 3
```

or cherry-pick the original commit that created the file first.

### Key Lesson

Cherry-pick copies commits, but dependencies still matter.

---

# Reset vs Revert

| Feature                     | git reset     | git revert          |
| --------------------------- | ------------- | ------------------- |
| Removes commit from history | Yes           | No                  |
| Creates new commit          | No            | Yes                 |
| Safe for shared branches    | No            | Yes                 |
| Rewrites history            | Yes           | No                  |
| Common use                  | Local cleanup | Production rollback |

---

# Branching Strategies

## GitFlow

Branches:

```text
main
develop
feature/*
release/*
hotfix/*
```

Used by:

* Enterprise environments
* Large teams
* Scheduled releases

### Pros

* Structured workflow
* Clear release process

### Cons

* More complex

---

## GitHub Flow

Branches:

```text
main
feature branch
pull request
merge
```

Used by:

* Startups
* Most GitHub projects

### Pros

* Simple
* Fast

### Cons

* Less structured

---

## Trunk-Based Development

Branches:

```text
main
short-lived branches
merge quickly
```

Used by:

* Google
* Netflix
* Modern CI/CD teams

### Pros

* Fast delivery
* Continuous integration

### Cons

* Requires strong automation

---

# Final Takeaways

* git reset rewrites history.
* git revert preserves history.
* git reflog can recover lost commits.
* git cherry-pick requires understanding commit dependencies.
* Reverting a middle commit may create conflicts.
* Reverting the latest commit is usually easier.
* GitHub Flow is commonly used for fast-moving teams.
* GitFlow is common in enterprise environments.

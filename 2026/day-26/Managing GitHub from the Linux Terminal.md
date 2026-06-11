# GitHub CLI Lab – Managing GitHub from the Linux Terminal

## Project Overview

This lab focused on managing GitHub directly from a Linux terminal using GitHub CLI (`gh`). The objective was to learn how real Cloud and DevOps engineers interact with GitHub repositories, issues, pull requests, and workflows without relying on the GitHub web interface.

The lab was completed inside an Ubuntu Docker container, simulating a Linux-based development environment similar to EC2 instances, cloud jump hosts, CI/CD runners, and DevOps workstations.

---

## Lab Objectives

* Authenticate GitHub CLI
* Manage repositories from the terminal
* Create and manage GitHub Issues
* Create feature branches
* Push changes to GitHub
* Create Pull Requests from the terminal
* Review Pull Requests
* Merge Pull Requests
* Understand merge strategies
* View GitHub Actions workflow runs

---

## Environment

| Component             | Value                            |
| --------------------- | -------------------------------- |
| Platform              | Docker Container                 |
| Operating System      | Ubuntu                           |
| Git Version           | 2.53.0                           |
| GitHub CLI            | 2.46.0                           |
| Authentication Method | GitHub CLI Device Authentication |
| Git Protocol          | SSH                              |

---

# Authentication Workflow

Verified GitHub CLI installation:

```bash
gh --version
```

Authenticated GitHub CLI:

```bash
gh auth login
```

Verified authentication status:

```bash
gh auth status
```

### What We Learned

* GitHub CLI uses OAuth authentication.
* SSH was selected as the Git protocol.
* Browser-based device authentication works even when running inside Docker containers.
* `gh auth status` is the primary troubleshooting command for GitHub CLI authentication.

---

# Repository Creation

Created a new GitHub repository directly from the terminal:

```bash
gh repo create
```

Repository Details:

```text
Repository Name: github-cli-la
Visibility: Public
README: Yes
.gitignore: No
License: No
```

Cloned the repository locally during creation.

Verified repository information:

```bash
gh repo view
```

### What We Learned

* Repositories can be created without opening GitHub.
* GitHub CLI automatically communicates with the GitHub API.
* Repository metadata can be viewed directly from the terminal.

---

# GitHub Issues

Listed existing issues:

```bash
gh issue list
```

Created a new issue:

```bash
gh issue create
```

Issue Details:

```text
Title:
Add project documentation

Body:
Create a professional README for the GitHub CLI lab.
```

Viewed issue information:

```bash
gh issue view 1
```

Docker-friendly alternative:

```bash
gh issue view 1 --json url
```

### What We Learned

* Issues represent work items or tasks.
* GitHub CLI allows issue creation without a browser.
* JSON output is useful when working on Linux servers and Docker containers.
* `--web` commands may fail inside containers due to lack of a graphical browser.

---

# Feature Branch Workflow

Created a feature branch using the modern Git command:

```bash
git switch -c feature-readme
```

Verified active branch:

```bash
git branch
```

### What We Learned

* `git switch` is the preferred modern replacement for many `git checkout` operations.
* Feature branches isolate work from the main branch.
* Pull Requests are created from feature branches into main.

---

# Updating Repository Content

Viewed repository contents:

```bash
ls -la
```

Viewed README:

```bash
cat README.md
```

Updated README documentation.

Checked repository status:

```bash
git status
```

Staged changes:

```bash
git add README.md
```

Committed changes:

```bash
git commit -m "Add GitHub CLI lab documentation"
```

Pushed branch to GitHub:

```bash
git push -u origin feature-readme
```

### What We Learned

* Git tracks modified files before staging.
* Staging prepares changes for commit.
* Push operations publish local changes to GitHub.

---

# Pull Request Workflow

Created Pull Request:

```bash
gh pr create
```

Listed open Pull Requests:

```bash
gh pr list
```

Viewed Pull Request:

```bash
gh pr view 2
```

Docker-friendly alternative:

```bash
gh pr view 2 --json title,state,url,headRefName,baseRefName
```

### Pull Request Information

```text
Source Branch:
feature-readme

Target Branch:
main

State:
OPEN
```

### What We Learned

* Pull Requests are created while on the feature branch.
* GitHub CLI automatically detects source and destination branches.
* JSON output is often more reliable inside Linux environments.

---

# Merge Strategies

Merged Pull Request:

```bash
gh pr merge 2
```

Available merge strategies:

### Merge Commit

```text
Preserves complete branch history.
Creates an additional merge commit.
```

### Rebase and Merge

```text
Creates a linear history.
Removes merge commits.
```

### Squash and Merge

```text
Combines all feature branch commits into a single commit.
Keeps history clean and readable.
```

Selected:

```text
Squash and Merge
```

Deleted feature branch after merge.

### What We Learned

* Squash merging is commonly used for documentation and small feature changes.
* Branches should typically be deleted after successful merges.
* GitHub CLI can perform the entire review and merge process without a browser.

---

# GitHub Actions

Viewed workflow runs:

```bash
gh run list
```

Output:

```text
No runs found
```

### Why?

The repository contained no GitHub Actions workflow files:

```text
.github/
└── workflows/
```

Without workflow definitions, GitHub has no pipelines to execute.

### What We Learned

* `gh run list` displays GitHub Actions workflow executions.
* This command is frequently used by DevOps engineers when troubleshooting CI/CD pipelines.
* Workflow runs only exist when Actions workflows are configured.

---

# Complete Command Reference

| Command                                                       | Purpose                                  |
| ------------------------------------------------------------- | ---------------------------------------- |
| `gh auth login`                                               | Authenticate GitHub CLI                  |
| `gh auth status`                                              | Verify authentication status             |
| `gh repo create`                                              | Create a new repository                  |
| `gh repo view`                                                | View repository information              |
| `gh issue list`                                               | List repository issues                   |
| `gh issue create`                                             | Create a new issue                       |
| `gh issue view 1`                                             | View issue details                       |
| `gh issue view 1 --web`                                       | Open issue in browser                    |
| `gh issue view 1 --json url`                                  | View issue URL (Docker/Linux friendly)   |
| `git switch -c feature-readme`                                | Create and switch to a feature branch    |
| `gh pr create`                                                | Create a Pull Request                    |
| `gh pr list`                                                  | List Pull Requests                       |
| `gh pr view 2`                                                | View Pull Request details                |
| `gh pr view 2 --json title,state,url,headRefName,baseRefName` | View Pull Request details in JSON format |
| `gh run list`                                                 | Display GitHub Actions workflow runs     |

---

# Skills Demonstrated

* GitHub CLI Authentication
* SSH-Based Git Operations
* Repository Creation and Management
* GitHub Issue Management
* Feature Branch Workflow
* Pull Request Lifecycle
* Merge Strategy Selection
* GitHub Actions Visibility
* Linux-Based GitHub Administration
* Docker-Based Development Workflow

---

# Key Takeaways

This lab demonstrated how modern Cloud and DevOps engineers can manage GitHub repositories entirely from the Linux command line. Using GitHub CLI, common tasks such as repository creation, issue tracking, branch management, pull request creation, and merge operations can be performed without opening the GitHub website.

The workflow practiced in this lab closely mirrors real-world engineering environments where developers and DevOps engineers work from Linux servers, cloud instances, remote development environments, and CI/CD systems.

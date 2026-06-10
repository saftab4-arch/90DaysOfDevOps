Day 24 - Advanced Git: Merge, Rebase, Stash & Cherry Pick
Overview

Today I practiced advanced Git workflows used in real development environments.

The goal was to understand how branches are combined, how history can be rewritten, how to temporarily save work, and how to move individual commits between branches.

Topics covered:

Git Merge
Git Rebase
Git Squash Merge
Git Stash
Git Cherry-Pick
Cherry-Pick Conflict Troubleshooting
Git Merge
Definition

Git Merge combines changes from one branch into another.

Most commonly:

git switch main
git merge feature-login

Git takes all commits from the feature branch and integrates them into the target branch.

Real Example

Feature Branch:

Added login page
Added login validation

Main Branch:

Main branch update

Merge Result:

main
 ├── Main branch update
 ├── Added login page
 └── Added login validation
Why Merge Is Common

Merge preserves branch history and automatically includes all dependent commits.

This makes it safer for normal feature development.

Git Rebase
Definition

Git Rebase moves commits from one branch and replays them on top of another branch.

Example:

git switch feature-dashboard
git rebase main
Before Rebase
main
 └── Main branch update

feature-dashboard
 └── Added dashboard
After Rebase
main
 └── Main branch update
      └── Added dashboard

Git rewrites commit history to create a cleaner timeline.

Important Rule

Never rebase commits that have already been pushed and shared with a team.

Rebase rewrites commit history and can cause synchronization problems for other developers.

Squash Merge
Definition

Squash Merge combines multiple commits into a single commit.

Example:

git merge --squash feature-profile
Example

Feature Branch:

Fixed typo
Updated formatting
Added profile image
Updated CSS

After Squash:

Added profile feature

One clean commit appears on main.

Benefits
Cleaner history
Easier pull requests
Removes unnecessary intermediate commits
Git Stash
Definition

Git Stash temporarily saves uncommitted work.

Example:

git stash
Common Workflow

Working on Feature A:

echo "testing" >> file.txt

Urgent production issue arrives.

Save work:

git stash

Switch branches:

git switch production-fix

After completing work:

git switch feature-a
git stash pop

Work returns exactly where it was left.

Useful Commands

Save stash:

git stash

Save with message:

git stash push -m "dashboard work"

List stashes:

git stash list

Restore latest stash:

git stash pop

Restore specific stash:

git stash apply stash@{1}
Stash Pop vs Apply
git stash pop

Restores stash and removes it.

git stash apply

Restores stash but keeps it in the stash list.

Git Cherry-Pick
Definition

Git Cherry-Pick copies a specific commit from one branch and applies it to another branch.

Example:

git cherry-pick <commit-hash>
Real Example

Feature Branch:

Fix 1
Fix 2
Fix 3

Only Fix 2 is needed in production.

git cherry-pick 96803bf

Git attempts to replay only that commit.

What Cherry-Pick Actually Does

Cherry-pick does NOT copy a branch.

Cherry-pick copies:

One specific commit

from one branch and applies it to the current branch.

Think of it as replaying a commit.

Cherry-Pick Troubleshooting (Real Lab Experience)

During the lab I attempted:

git cherry-pick 96803bf

Git returned:

CONFLICT (modify/delete)
Why It Happened

The commit I selected depended on a file created in an earlier commit.

Branch History:

Fix 1 -> Create fixes.txt
Fix 2 -> Modify fixes.txt
Fix 3 -> Modify fixes.txt

I attempted:

git cherry-pick Fix2

without bringing:

Fix1

first.

Git could not modify:

fixes.txt

because that file did not exist on the target branch.

Lesson Learned

Cherry-pick works best when the commit is independent.

Before cherry-picking:

Ask:

Does this commit depend on earlier commits?

If yes:

Bring prerequisite commits first.

Recovery Commands

Abort failed cherry-pick:

git cherry-pick --abort

Continue after resolving conflict:

git cherry-pick --continue

Skip problematic commit:

git cherry-pick --skip
Commands Practiced
Merge
git merge feature-login
Rebase
git rebase main
Squash Merge
git merge --squash feature-profile
Stash
git stash
git stash list
git stash pop
git stash apply stash@{0}
Cherry-Pick
git cherry-pick <commit>
git cherry-pick --abort
git cherry-pick --continue
git cherry-pick --skip
Key Takeaways
Merge is the safest and most common way to combine branches.
Rebase creates a cleaner commit history.
Squash merge combines multiple commits into one.
Stash allows temporary storage of unfinished work.
Cherry-pick copies a specific commit, not an entire branch.
Cherry-pick can fail when commits depend on files or changes that do not exist on the target branch.
Understanding commit dependencies is critical before using cherry-pick in production environments.

Skills Practiced:

Git Merge • Git Rebase • Git Stash • Git Cherry-Pick • Conflict Resolution • Branch Management • Commit History Analysis • Troubleshooting Git Operations

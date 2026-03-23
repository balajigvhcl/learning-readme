# Git & Version Control - Hands-On Lab Guide

## Table of Contents
1. [Getting Started with Git](#getting-started-with-git)
2. [Basic Git Workflow](#basic-git-workflow)
3. [Managing Git History](#managing-git-history)
4. [Working with Branches](#working-with-branches)
5. [Merging Branches](#merging-branches)
6. [Remote Repositories](#remote-repositories)
7. [Handling Merge Conflicts](#handling-merge-conflicts)
8. [Advanced Merge Strategies](#advanced-merge-strategies)

---

## Getting Started with Git

### Initial Setup

Before working with Git, configure your global user information:

```bash
git config --global user.name 'Balaji GV'
git config --global user.email 'balajigv.me@gmail.com'
```

### Create a Working Directory

Create a new directory and initialize an empty Git repository:

```bash
mkdir working
cd working
```

Create sample files:

```bash
touch FileA.txt FileB.txt
```

Initialize Git repository:

```bash
$ git init
Initialized empty Git repository in C:/Users/balaj/working/.git/
```

---

## Basic Git Workflow

### Understanding the Three Areas

Git workflow consists of three main areas:

```
Working Directory → Staging Area → Git Repository (.git)
```

### Adding Files to Git

#### Step 1: Stage Files

```bash
$ git add .
```

#### Step 2: Check Status

```bash
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   FileA.txt
        new file:   FileB.txt
        new file:   Readme.md
```

**What's Happening:** Files have changed status from `U` (Untracked) to `A` (Added)

#### Step 3: Commit Changes

```bash
$ git commit -m 'this is the initial commit'
[master (root-commit) 3fed556] this is the initial commit
 3 files changed, 31 insertions(+)
 create mode 100644 FileA.txt
 create mode 100644 FileB.txt
 create mode 100644 Readme.md
```

#### Step 4: Verify Clean Working Tree

```bash
$ git status
On branch master
nothing to commit, working tree clean
```

### Making Changes and Committing

Edit FileA.txt and add new content:

```bash
$ cat FileA.txt
This is file a
this is change in file A
```

Stage and commit the changes:

```bash
$ git add FileA.txt
$ git commit -m 'this is to commit fileA'
[master 1ae559a] this is to commit fileA
 1 file changed, 2 insertions(+), 1 deletion(-)
```

**Note:** When a file is modified, its status changes to `M` (Modified)

---

## Managing Git History

### Viewing Commit History

View all commits:

```bash
$ git log
commit 362569d262421ffaaffd7e9aea75e93917cd8442 (HEAD -> master)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 14 13:12:09 2026 +0530

    this is latest commit on a

commit 1ae559aa614a2cb46894e3035c4896328ab7d53c
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 14 13:05:14 2026 +0530

    this is to commit fileA
```

### Reverting to Previous Commits

#### Checkout a Specific Commit

```bash
$ git checkout 1ae559aa614a2cb46894e3035c4896328ab7d53c
Note: switching to '1ae559aa614a2cb46894e3035c4896328ab7d53c'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

HEAD is now at 1ae559a this is to commit fileA
```

**Important:** You're now in **Detached HEAD** state - not on any branch

#### View File at Previous Commit

```bash
$ cat FileA.txt
This is file a
this is change in file A
```

#### View All Commits

```bash
$ git log --all
commit 362569d262421ffaaffd7e9aea75e93917cd8442 (master)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 14 13:12:09 2026 +0530

    this is latest commit on a

commit 1ae559aa614a2cb46894e3035c4896328ab7d53c (HEAD)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 14 13:05:14 2026 +0530

    this is to commit fileA
```

#### Return to Latest Commit

```bash
$ git checkout 362569d262421ffaaffd7e9aea75e93917cd8442
Previous HEAD position was 1ae559a this is to commit fileA
HEAD is now at 362569d this is latest commit on a
```

---

## Unstaging Files

### Remove File from Staging Area

If you accidentally staged a file you don't want to commit:

```bash
$ git add .
$ git status
HEAD detached at 362569d
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   FileA.txt
```

### Unstage the File

```bash
$ git rm --cached FileA.txt
rm 'FileA.txt'

$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

Untracked files:
  (use "git add <file>..." to include what will be committed)
        FileA.txt
```

**Result:** File is now marked as `U` (Untracked) instead of `M` (Modified)

---

## Working with Branches

### Understanding Branches

Branches allow you to work on features independently:

```
Main Code → Feature Branch (isolated copy)
         → Test the feature
         → Merge back to main when ready
```

### Creating a Branch

Create and switch to a new branch:

```bash
$ git checkout -b featureA
Switched to a new branch 'featureA'

$ git status
On branch featureA
nothing to commit, working tree clean
```

### Making Changes on a Branch

Add a new file on featureA:

```bash
$ git add FileC.txt
$ git commit -m 'added file c'
[featureA a3436df] added file c
 1 file changed, 1 insertion(+)
 create mode 100644 FileC.txt
```

### View Branch-Specific History

```bash
$ git log
commit a3436dfcb949732da359a48760468c75af55d4e8 (HEAD -> featureA)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 14 14:01:39 2026 +0530

    added file c

commit 4d06a672955de38494f8b89816ca02e39206d218 (master)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 14 13:29:40 2026 +0530

    only 2 lines
```

### Verify File Isolation Between Branches

On featureA (file exists):

```bash
$ git checkout featureA
Switched to branch 'featureA'

$ ls
FileA.txt  FileB.txt  FileC.txt
```

Switch to master (FileC.txt not here):

```bash
$ git checkout master
Switched to branch 'master'

$ ls
FileA.txt  FileB.txt
```

**Key Point:** Each branch has its own isolated copy of files

---

## Merging Branches

### Fast-Forward Merge

When feature branch is directly ahead of master:

```bash
$ git checkout master
Switched to branch 'master'

$ git merge featureA
Updating 4d06a67..a3436df
Fast-forward
 FileC.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 FileC.txt
```

After merge, master includes all changes:

```bash
$ ls
FileA.txt  FileB.txt  FileC.txt

$ git log
commit a3436dfcb949732da359a48760468c75af55d4e8 (HEAD -> master, featureA)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 14 14:01:39 2026 +0530

    added file c
```

### Three-Way Merge

When both branches have made different changes:

**Setup:** Make changes on both branches before merging

On master:

```bash
$ git add FileB.txt
$ git commit -m 'changeb again master'
[master 6317023] changeb again master
 1 file changed, 3 insertions(+), 1 deletion(-)
```

On featureA:

```bash
$ git checkout featureA
$ git add FileC.txt
$ git commit -m 'modifyc'
[featureA eadfff3] modifyc
 1 file changed, 1 insertion(+), 1 deletion(-)
```

**Merge with three-way strategy:**

```bash
$ git checkout master
$ git merge featureA
Merge made by the 'ort' strategy.
 FileC.txt | 6 ++----
 1 file changed, 2 insertions(+), 4 deletions(-)
```

### View Merge Graph

Visualize the merge history:

```bash
$ git log --graph
*   commit d15e5eb49029bc803d22ef342b4d9bc97a70bbed (HEAD -> master)
|\  Merge: dd15295 a64b40e
| | Author: Balaji GV <balajigv.me@gmail.com>
| | Date:   Sat Mar 21 08:56:26 2026 +0530
| |
| |     Merge branch 'featureA'
| |
| * commit a64b40e289ac9db6409357bbd831e8af3de94aeb (featureA)
| | Author: Balaji GV <balajigv.me@gmail.com>
| | Date:   Sat Mar 21 08:55:34 2026 +0530
| |
| |     c before
| |
* | commit dd15295141eb22bd8733f3c71d153648e9d9d819
| | Author: Balaji GV <balajigv.me@gmail.com>
| | Date:   Sat Mar 21 08:56:12 2026 +0530
| |
| |     master b before merge
| |
* | commit aa05e358f706d2e7f8b74c71017752797a97c170
```

The merge creates a **commit node** with two parents, showing the complete history

---

## Remote Repositories

### Adding a Remote Repository

Connect your local repo to GitHub:

```bash
$ git remote add origin https://github.com/balajigvhcl/application.git
```

### Publishing to Remote

Push your local commits to GitHub:

```bash
$ git push -u origin master
Enumerating objects: 22, done.
Counting objects: 100% (22/22), done.
Delta compression using up to 8 threads
Compressing objects: 100% (16/16), done.
Writing objects: 100% (22/22), 2.30 KiB | 336.00 KiB/s, done.
Total 22 (delta 4), reused 0 (delta 0), pack-from 0 (from 0)
remote: Resolving deltas: 100% (4/4), done.
To https://github.com/balajigvhcl/application.git
 * [new branch]      master -> master
branch 'master' set up to track 'origin/master'.
```

### Pushing Updates

After making new commits locally:

```bash
$ git push -u origin master
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 339 bytes | 339.00 KiB/s, done.
To https://github.com/balajigvhcl/application.git
   a3436df..38060a5  master -> master
```

### Publishing Feature Branches

Push your feature branch to remote:

```bash
$ git push -u origin featureA
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
remote: 
remote: Create a pull request for 'featureA' on GitHub by visiting:
remote:      https://github.com/balajigvhcl/application/pull/new/featureA
```

---

## Handling Merge Conflicts

### What Causes Conflicts

When two branches modify the same file in different ways, Git can't automatically merge them.

### Scenario: Conflicting Changes

User 1 modifies FileB.txt and pushes to remote:

```bash
[Remote] FileB.txt updated by another user
```

You make a different change to FileB.txt locally and try to push:

```bash
$ git push
To https://github.com/balajigvhcl/application.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/balajigvhcl/application.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
```

### Resolving Conflicts - Pull First

Pull the remote changes:

```bash
$ git pull
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (3/3), 1.02 KiB | 174.00 KiB/s, done.
From https://github.com/balajigvhcl/application
   38060a5..06d99e9  master     -> origin/master
Auto-merging FileB.txt
CONFLICT (content): Merge conflict in FileB.txt
Automatic merge failed; fix conflicts and then commit the result.

$ git status
On branch master
You have unmerged paths.
  (use "git add/rm <file>..." as appropriate to mark resolution)
```

### Resolving the Conflict

VS Code will show conflict markers in the file. You have three options:

- **Accept Current Change** (keep your version)
- **Accept Incoming Change** (take remote version)
- **Accept Both Changes** (keep both)

Select the appropriate option and save.

### Complete the Merge

After resolving conflicts:

```bash
$ git add FileB.txt
$ git commit -m 'Merge branch master of https://github.com/balajigvhcl/application'
[master 2ed8abb] Merge branch 'master' of https://github.com/balajigvhcl/application
 2 parents 407ee71 06d99e9
 1 file changed, 4 insertions(+)
```

### Push Resolved Changes

```bash
$ git push
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 8 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 938 bytes | 938.00 KiB/s, done.
remote: Resolving deltas: 100% (3/3), completed with 1 local object.
To https://github.com/balajigvhcl/application.git
   0339834..be274cd  master -> master
```

---

## Advanced Merge Strategies

### Squash Merge

Combine multiple commits into a single commit when merging:

```bash
$ git merge --squash featureA
Squashing is useful for keeping commit history clean
```

This creates a single merge commit instead of multiple commits.

---

## Summary of Key Git Commands

| Command | Purpose |
|---------|---------|
| `git init` | Initialize a new Git repository |
| `git add <file>` | Stage files for commit |
| `git commit -m "message"` | Commit staged changes |
| `git log` | View commit history |
| `git checkout -b <branch>` | Create and switch to new branch |
| `git checkout <branch>` | Switch to existing branch |
| `git merge <branch>` | Merge branch into current branch |
| `git push` | Push commits to remote |
| `git pull` | Fetch and merge remote changes |
| `git status` | Show working tree status |
| `git remote add origin <url>` | Add remote repository |

---

## Best Practices

✅ **DO:**
- Commit frequently with meaningful messages
- Create feature branches for new work
- Pull before pushing to avoid conflicts
- Review changes before committing
- Use `.gitignore` to exclude files

❌ **DON'T:**
- Push directly to main branch
- Commit large binary files
- Mix multiple features in one commit
- Leave merge conflicts unresolved

---

## Resources

- [Git Official Documentation](https://git-scm.com/docs)
- [GitHub Learning Resources](https://github.com/skills)
- Common commands: `git --help`


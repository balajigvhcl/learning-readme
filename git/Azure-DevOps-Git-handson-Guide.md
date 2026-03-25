# Azure DevOps & Git Integration - Hands-On Lab Guide

## Table of Contents
1. [Local Repository Setup](#local-repository-setup)
2. [Pushing to Azure DevOps](#pushing-to-azure-devops)
3. [Working with Feature Branches](#working-with-feature-branches)
4. [Creating Pull Requests](#creating-pull-requests)
5. [Importing External Repositories](#importing-external-repositories)
6. [Work Items & Pull Request Requirements](#work-items--pull-request-requirements)
7. [Branch Security Policies](#branch-security-policies)
8. [Git Ignore Configuration](#git-ignore-configuration)

---

## Local Repository Setup

### Prerequisites

Before starting, ensure you have:
- ✅ Git installed and configured
- ✅ Azure DevOps project created
- ✅ Azure DevOps repository URL

### Create Local Repository Directory

Create a new folder for your project:

```bash
$ mkdir repos
$ cd repos
```

### Initialize Git Repository

```bash
$ git init
Initialized empty Git repository in C:/Users/balaj/repos/.git/
```

### Create Sample Files

Create your initial project files:

```bash
$ touch filea.txt fileb.txt
```

### Stage and Commit Files

Add files to staging area:

```bash
$ git add .
```

Create your first commit:

```bash
$ git commit -m 'initial'
[master (root-commit) dbfd102] initial
 2 files changed, 2 insertions(+)
 create mode 100644 filea.txt
 create mode 100644 fileb.txt
```

### Verify Repository Status

```bash
$ dir
filea.txt  fileb.txt
```

---

## Pushing to Azure DevOps

### Add Remote Repository

Connect your local repository to Azure DevOps:

```bash
$ git remote add origin https://balajigv@dev.azure.com/balajigv/App-Project/_git/App-Project
```

### Troubleshooting: Remote Name Typo

**Problem:** If you accidentally typed the remote name incorrectly:

```bash
$ git remote add ogirin https://balajigv@dev.azure.com/balajigv/App-Project/_git/App-Project
```

**Solution:** Rename the remote to the correct name:

```bash
$ git remote rename ogirin origin
```

### Push to Azure DevOps

Push your local commits to the remote repository:

```bash
$ git push -u origin --all
```

**Parameters:**
- `-u` (upstream): Sets the remote as the default for future pushes
- `--all`: Pushes all branches

### Verify on Azure DevOps

✅ **Expected Result:** Files now visible in your Azure DevOps repository

```
Your repository should show:
├── filea.txt
├── fileb.txt
└── main (or master - depending on default branch)
```

---

## View Repository Branches

### List All Branches

View all local and remote branches:

```bash
$ git branch -a
  featureB
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/featureB
  remotes/origin/main
```

**Branch Status Explanation:**
- `*` = Currently checked out branch
- `main` = Default branch (tracked from remote)
- `featureB` = Feature branch for development
- `remotes/origin/HEAD` = Remote repository's default branch pointer

### Configure Default Branch

Set the default branch for new repositories:

```bash
$ git config --global init.defaultBranch main
```

Verify configuration:

```bash
$ git config --global init.defaultBranch
main
```

---

## Working with Feature Branches

### Switch to Feature Branch

Checkout the featureB branch:

```bash
$ git checkout featureB
Switched to branch 'featureB'
Your branch is up to date with 'origin/featureB'.
```

### Make Changes on Feature Branch

Edit fileb.txt with your changes:

```bash
$ git add fileb.txt
```

### Commit Changes

```bash
$ git commit -m 'fb fileb'
[featureB d9856c6] fb fileb
 1 file changed, 1 insertion(+), 1 deletion(-)
```

### Push Feature Branch to Remote

```bash
$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 316 bytes | 316.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Analyzing objects... (3/3) (3 ms)
remote: Validating commits... (1/1) done (0 ms)
remote: Storing packfile... done (37 ms)
remote: Storing index... done (35 ms)
remote: Updating refs... done (121 ms)
To https://dev.azure.com/balajigv/App-Project/_git/App-Project
   068c8a2..d9856c6  featureB -> featureB
```

**Success Indicators:**
- ✅ Objects enumerated and compressed
- ✅ Packfile stored on remote
- ✅ Branch reference updated

---

## Creating Pull Requests

### Step-by-Step Pull Request Creation

When you push a feature branch to Azure DevOps, a notification appears to create a pull request.

#### 1. Select Feature Branch

Navigate to **Branches** in Azure DevOps:

```
Repositories → Branches → featureB
```

#### 2. Create New Pull Request

Click **Create a pull request** button

#### 3. Update Pull Request Description

Add details about your changes:

```
Title: fb fileb
Description: Updated fileb.txt with new content

Changes:
- Added updates to fileb.txt
- Tested locally
- Ready for review
```

#### 4. Submit Pull Request

Click **Create** to open the PR for review

### Pull Request Status

After creation, view the PR status:

```
Status: Pending review
Merge strategy available:
  - Rebase and fast-forward
  - Squash merge
  - Three-way merge
```

### Complete/Approve Pull Request

After review and approval:

1. Click **Complete** button
2. Select merge strategy (e.g., **Rebase and fast-forward**)
3. Confirm merge

**Expected Output:**

```
✅ Balaji GV completed this pull request 4m ago
   
Merge Details:
├── Commit: 261657bc
├── Strategy: Rebase and fast-forward
├── Source branch: deleted automatically
└── Associated work items: completed
```

### Branch Auto-Deletion

**Important:** After merging, the source branch (featureB) is automatically deleted from the remote.

```
The featureB remote branch was deleted post this merge activity
```

---

## Importing External Repositories

### Import from GitHub to Azure DevOps

#### Prerequisites

- ✅ GitHub repository URL
- ✅ Authentication credentials (if private repository)

#### Step-by-Step Import Process

##### 1. Navigate to Repository Import

In Azure DevOps project:

```
Repositories → Import Repository
```

##### 2. Provide Source Repository

```
Source type: GitHub
Repository URL: https://github.com/username/repository.git
Authentication: [Enter credentials if required]
```

##### 3. Configure Target Repository

```
Name: Dev-Project
Description: Imported from GitHub
```

##### 4. Start Import

Click **Import** button

**Import Progress:**

```
⏳ Importing repository...
✅ Repository imported successfully
```

### Verify Imported Repository

Access your imported repository:

```
https://dev.azure.com/balajigv/Dev-Project/_git/Dev-Project
```

**Imported Content:**

```
Dev-Project Repository
├── All branches from GitHub
├── Full commit history
├── All tags
└── Pull request history (if configured)
```

---

## Work Items & Pull Request Requirements

### Adding Work Item Link as Mandatory

To enforce linking work items to pull requests:

#### 1. Access Branch Policies

In your repository:

```
Repositories → Branches → [Select Branch] → Branch Policies
```

#### 2. Configure Requirements

Enable the requirement:

```
✅ Require linked work items
   └─ Work items are mandatory for PR merge
```

#### 3. Save Policy

Click **Save changes**

### Create Feature Branch with Work Item

Create a new feature branch:

```bash
$ git checkout -b featureB
Switched to a new branch 'featureB'
```

### Make Changes and Create PR

#### Edit Files

```bash
$ echo "Updated content" >> fileb.txt
$ git add fileb.txt
$ git commit -m 'update fileb'
[featureB abc1234] update fileb
 1 file changed, 1 insertion(+)
```

#### Create Pull Request

Navigate to **Create pull request** in Azure DevOps

### Link Work Item to PR

**Error Without Work Item:**

```
❌ Pull Request blocked from merge
   Error: Missing required work item link
   Action: Add work item to proceed
```

#### Add Work Item

In the PR details:

1. Click **+ Work item** button
2. Select work item from your backlog
3. Link to PR

**Example:**

```
Linked Work Items:
├── Task: Feature-123 - Update login form
├── Status: In Progress
└── Priority: High
```

#### Complete Merge

After adding work item:

```bash
✅ All requirements satisfied
   Ready to complete pull request
```

Click **Complete** to merge

---

## Branch Security Policies

### Access Branch Security Settings

Configure security and governance for branches:

#### 1. Navigate to Branch Security

```
Repositories → Branches → [Select Branch] → Security
```

#### 2. Available Security Options

**Selection of policies available:**

```
Policy Options:
├── Require a minimum number of reviewers
├── Allow requestors to approve their own changes
├── Honor comment resolutions (Git)
├── Require an associated work item
├── Require status checks to pass
├── Security policies (prevent unauthorized access)
└── Enforce linked work items
```

### Repository-Level Policies

For organization-wide governance:

```
Repositories → Repository Settings → Policies
```

**Popular Policies:**

- ✅ **Enforce linked work items**: All commits must reference work items
- ✅ **Require reviewers**: PRs need approval before merge
- ✅ **Prevent force pushes**: Protect history integrity
- ✅ **Require status checks**: Automated builds must pass
- ✅ **Restrict branch creation**: Only authorized users can create branches
- ✅ **Restrict branch deletion**: Prevent accidental branch removal
- ✅ **Enforce squash commits**: Keep history clean

### Example Policy Configuration

```
Master Branch Protection:
├── Require 2 reviewers minimum
├── Block direct pushes (PR required)
├── Require passing build
├── Linked work item mandatory
└── Auto-delete feature branches on merge
```

---

## Git Ignore Configuration

### Purpose of .gitignore

Exclude files and directories from version control:

```
Purpose: Prevent committing unnecessary files
├── Build artifacts
├── Temporary files
├── Secrets and credentials
├── IDE configuration files
├── Dependencies (node_modules, packages, etc.)
└── OS-specific files
```

### Create .gitignore File

In your repository root:

```bash
$ touch .gitignore
```

### Common .gitignore Patterns

```gitignore
# Visual Studio Code
.vscode/
*.code-workspace

# Python
__pycache__/
*.py[cod]
*$py.class
.Python
venv/
env/

# Node.js
node_modules/
npm-debug.log
yarn-error.log

# .NET
bin/
obj/
*.dll
*.exe

# IDE
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Secrets
.env
.env.local
secrets.json

# Build output
dist/
build/
coverage/
```

### Verify Git Ignores Files

After adding .gitignore:

```bash
$ git status
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore

Files listed in .gitignore will NOT appear in git status
```

### Commit .gitignore

```bash
$ git add .gitignore
$ git commit -m 'add git ignore'
[main abc1234] add git ignore
 1 file changed, 25 insertions(+)
```

### Check if File is Ignored

Verify a file is properly ignored:

```bash
$ echo "secret=key123" > .env
$ git status
# .env will NOT appear in status output - successfully ignored
```

---

## Quick Reference: Azure DevOps Workflow

### Complete Development Workflow

```
1️⃣  Create Feature Branch
    $ git checkout -b featureX

2️⃣  Make Changes
    $ git add .
    $ git commit -m 'feature description'

3️⃣  Push to Remote
    $ git push -u origin featureX

4️⃣  Create Pull Request
    Repositories → Create Pull Request

5️⃣  Link Work Item
    PR Details → + Work item → Select item

6️⃣  Request Review
    Add reviewers → Request review

7️⃣  Address Feedback
    Make changes → Commit → Push

8️⃣  Approve & Complete
    Reviewers approve → Click Complete

9️⃣  Auto-cleanup
    ✅ Source branch auto-deleted
    ✅ Work item status updated
```

---

## Best Practices Summary

### ✅ DO

- **Create feature branches** for each feature/fix
- **Link work items** to track progress
- **Write descriptive PR descriptions** for context
- **Request peer reviews** before merging
- **Test locally** before pushing
- **Use .gitignore** to exclude unnecessary files
- **Enforce branch policies** for code quality
- **Delete merged branches** to keep repo clean

### ❌ DON'T

- **Push directly to main** without PR
- **Ignore merge conflicts** - resolve them properly
- **Commit secrets** or sensitive data
- **Force push** to shared branches
- **Skip code review** process
- **Leave unlinked PRs** (if policy requires)
- **Mix multiple features** in one PR
- **Commit large binary files**

---

## Troubleshooting Guide

### Problem: Remote Name Typo

```
Error: fatal: 'origin' does not appear to be a git repository
```

**Solution:**

```bash
$ git remote -v  # Check configured remotes
$ git remote rename [old-name] origin
```

### Problem: Branch Already Exists

```
Error: Update ref failed for ref 'refs/heads/featureB'
```

**Solution:**

```bash
$ git branch -a  # List all branches
$ git branch -d featureB  # Delete local if needed
```

### Problem: Work Item Link Missing

```
Error: Pull request blocked - missing required work item
```

**Solution:**

1. Go to PR details
2. Click **+ Work item**
3. Select work item from backlog
4. Merge will then be allowed

---

## Additional Resources

- 📚 [Azure DevOps Documentation](https://docs.microsoft.com/en-us/azure/devops/)
- 📚 [Git Best Practices](https://git-scm.com/docs)
- 📚 [Azure Repos Guide](https://docs.microsoft.com/en-us/azure/devops/repos/)
- 💬 Help: `git --help` or `git [command] --help`


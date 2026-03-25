# Advanced Git Features & Branching Strategies - Hands-On Lab

## Table of Contents
1. [Creating Branches via GUI](#creating-branches-via-gui)
2. [Pull Request Workflow](#pull-request-workflow)
3. [Branch Protection Rules](#branch-protection-rules)
4. [Git Tagging](#git-tagging)
5. [Branching Strategies](#branching-strategies)
6. [Best Practices](#best-practices)

---

## Creating Branches via GUI

### Creating a Feature Branch

In your repository management interface (GitHub/Azure DevOps):

#### Step 1: Create New Branch

Click **+ New branch** or **Create branch**

```
Repository → Branches → New Branch
```

#### Step 2: Branch Configuration

Configure the new branch:

```
Branch Name: featureB
Source Branch: main (or master)
Description: Feature development branch
```

#### Step 3: Branch Created

**Result:** New branch inherits all content from source branch

```
featureB branch created from main
├── All files from main are now in featureB
├── Independent development space
└── Ready for feature work
```

### Understanding Branch Content

When you create a new branch from `main`:

✅ **Branch Content:**
- All files from parent branch are copied
- Complete commit history is inherited
- Independent working space created
- Changes on featureB don't affect main

**Visual Representation:**

```
         featureB (isolated copy)
         /
main ----●---- (latest main code)
         
featureB can be modified independently
```

---

## Pull Request Workflow

### Merging via GUI

#### Step 1: Navigate to Pull Requests

In your repository:

```
Repositories → Pull Requests → New Pull Request
```

#### Step 2: Configure PR Details

```
Source Branch: featureB
Target Branch: main
Title: Feature implementation
Description: What was changed and why
```

#### Step 3: Select Merge Strategy

Choose how to merge:

```
Merge Strategy Options:
├── ✅ Merge Commit (Recommended for most cases)
├── ✅ Squash Merge (Combine multiple commits)
├── ✅ Rebase and Fast-forward (Linear history)
└── ✅ Three-way Merge (Preserve branch history)
```

#### Step 4: Complete Pull Request

Click **Complete merge** or **Merge pull request**

### Pull Request Status

**During Merging:**

```
Status: Merging...
├── Checks running
├── Validating changes
└── Updating branches
```

**After Merge:**

```
✅ Pull request successfully merged and closed

Merge Details:
├── Merge Commit: abc1234def5678
├── Merge Strategy: Merge commit
├── Branch Status: Merged
└── Source Branch: Can be safely deleted
```

### Delete Merged Branch

After successful merge:

```
Repositories → Branches → [Select featureB] → Delete
```

**Result:**

✅ Feature branch deleted after merge
✅ Code updates now in main branch
✅ Latest version available in main

### View Updated Main Branch

After merge, switch to main and verify changes:

```bash
$ git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.

$ git log
commit abc1234 (HEAD -> main, origin/main)
Merge: 123abcd 456efgh
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 25 10:30:00 2026 +0530

    Merge branch 'featureB' into main
```

---

## Creating PR from VS Code

### Workflow in Visual Studio Code

#### Step 1: Create Feature Branch

```bash
$ git checkout -b featureC
Switched to a new branch 'featureC'
```

#### Step 2: Make Changes

Edit files in your project:

```bash
$ echo "new feature code" >> FileC.txt
$ git add FileC.txt
$ git commit -m 'add feature c implementation'
[featureC abc1234] add feature c implementation
 1 file changed, 1 insertion(+)
```

#### Step 3: Push Branch to Remote

```bash
$ git push -u origin featureC
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
remote: 
remote: Create a pull request for 'featureC' on GitHub by visiting:
remote:      https://github.com/username/repo/pull/new/featureC
```

#### Step 4: Verify in Main Branch

After PR merge, verify changes in main:

```bash
$ git checkout main
Switched to branch 'main'

$ git log
commit abc1234 (HEAD -> main)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 25 10:35:00 2026 +0530

    add feature c implementation
```

**Check Version:**

```bash
$ cat version.txt
2.1
```

---

## Branch Protection Rules

### Purpose of Branch Protection

Enforce code quality and governance:

- 🔒 **Prevent direct pushes** to critical branches
- 🔒 **Require pull requests** for all changes
- 🔒 **Mandate code reviews** before merge
- 🔒 **Block force pushes** to maintain history integrity
- 🔒 **Enforce status checks** (builds, tests, etc.)

### Setting Up Branch Protection

#### Step 1: Access Settings

Navigate to repository settings:

```
Settings → Branches → Add Rule
```

or for GitHub:

```
Settings → Branches → Branch Protection Rules → New Rule
```

#### Step 2: Create Protection Rule

```
Branch Name: main (or master)
Protect matching branches: ✅ Enabled

Protection Options:
├── ✅ Require a pull request before merging
├── ✅ Require reviews before merging (minimum: 1)
├── ✅ Require status checks to pass
├── ✅ Require branches to be up-to-date
├── ✅ Restrict who can push
├── ✅ Allow force pushes: ❌ Disabled
└── ✅ Allow deletions: ❌ Disabled
```

#### Step 3: Save Rule

Click **Create** to activate protection

### Testing Branch Protection

**Scenario:** Attempt to commit directly to protected branch

#### Step 1: Make Changes on Main

```bash
$ git checkout main
$ echo "direct change" >> FileC.txt
$ git add FileC.txt
$ git commit -m 'direct commit to main'
[main abc1234] direct commit to main
 1 file changed, 1 insertion(+)
```

#### Step 2: Attempt Direct Push

```bash
$ git push origin main

❌ Error: You are not authorized to push to this branch

remote: error: protected branch rejection
remote: error: cannot push directly to 'main'
Hint: Force pushing is disabled on this branch

[rejected] main -> main (protected branch hook declined)
error: failed to push some refs to 'https://github.com/username/repo'
```

### Create PR on Protected Branch

**Solution:** Create a separate branch and PR

#### Step 1: Create Feature Branch

```bash
$ git checkout -b balajigvpatch1
Switched to a new branch 'balajigvpatch1'
```

#### Step 2: Commit Changes

```bash
$ git add FileC.txt
$ git commit -m 'update FileC'
[balajigvpatch1 def5678] update FileC
 1 file changed, 1 insertion(+)
```

#### Step 3: Push Feature Branch

```bash
$ git push -u origin balajigvpatch1
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
remote: 
remote: Create a pull request for 'balajigvpatch1' on GitHub by visiting:
remote:      https://github.com/username/repo/pull/new/balajigvpatch1
```

#### Step 4: Create PR

Navigate to the PR creation URL and:

```
Source: balajigvpatch1
Target: main
Title: Fix: Update FileC content
Description: This PR updates FileC with new changes

✅ Create Pull Request
```

#### Step 5: Merge via PR

After review and approval:

```
✅ All checks passed
✅ Reviews approved
✅ Ready to merge

Click: Merge Pull Request
```

---

## Git Tagging

### Understanding Git Tags

**Tags** mark important points in your repository history:

```
Purpose:
├── Mark release versions (v1.0, v2.0)
├── Create checkpoint snapshots
├── Tag stable builds
└── Reference specific commits for deployment
```

### Tag Types

```
Git Tags:
├── Lightweight Tags (simple reference)
└── Annotated Tags (full objects with metadata) ← Recommended
```

### Creating Annotated Tags

Create a tag for the current commit:

```bash
$ git tag -a "v1.0" -m "version 1"
```

**Parameters:**
- `-a` = Create annotated tag
- `"v1.0"` = Tag name/version
- `-m` = Tag message

**Verification:**

```bash
$ git tag
v1.0
```

### Tag Information

View detailed tag information:

```bash
$ git show v1.0
tag v1.0
Tagger: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 21 11:58:55 2026 +0530

version 1

commit a0b0d2c19121b63c1611b99685ee40ee577c12b0 (tag: v1.0)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 21 09:32:18 2026 +0530

    squash merge fileb

diff --git a/FileB.txt b/FileB.txt
index 9f14be5..473dc43 100644 
--- a/FileB.txt
+++ b/FileB.txt
@@ -6,4 +6,5 @@ this change is done from visual code.

 this is changed on master branch

-master branch changed before merge
\ No newline at end of file
+master branch changed before merge
+this is squash change from master 
\ No newline at end of file
```

### Committing After Tag

**Important:** You can still commit after creating a tag

```bash
$ git log
commit cd319ba91f9b5973012f8df57343192daf9c1cf2 (HEAD -> master)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 21 11:59:43 2026 +0530

    made a change filec after tagging

commit a0b0d2c19121b63c1611b99685ee40ee577c12b0 (tag: v1.0)
Author: Balaji GV <balajigv.me@gmail.com>
Date:   Sat Mar 21 09:32:18 2026 +0530

    squash merge fileb
```

**Understanding the Structure:**

- ✅ `v1.0` tag points to commit `a0b0d2c`
- ✅ Latest commit `cd319ba` is after the tag
- ✅ `HEAD` points to latest commit
- ✅ Tag is a permanent reference to a specific point in history

### Use Cases for Tags

```
Release Management:
├── v1.0 - Initial release (production)
├── v1.1 - Bug fixes
├── v2.0 - Major feature release
└── v2.1 - Current stable version

Tagging Strategy:
├── Tag after QA approval
├── Tag before production deployment
├── Keep production tags separate
└── Use semantic versioning (major.minor.patch)
```

---

## Branching Strategies

### Git Flow / Feature Branch Strategy

Maintain code quality and organized development:

#### Core Principles

1. ✅ **Always maintain high-quality main branch**
   - Main contains production-ready code
   - Only stable, tested commits merged to main

2. ✅ **Main branch = production code**
   - Current production release
   - Hotfix source
   - Stable baseline

3. ✅ **Create feature branches for development**
   - Each feature gets isolated branch
   - No interference with other work
   - Easy to manage and review

4. ✅ **Use pull requests for merging**
   - Code review before merge
   - CI/CD validation
   - Approval workflow
   - Audit trail

5. ✅ **Keep feature branches short-lived**
   - Complete feature quickly (days, not weeks)
   - Reduce merge conflicts
   - Faster delivery
   - Easier reviews

6. ✅ **Maintain release and main branches**
   - Merge feature to both release and main
   - Coordinate across branches
   - Release prep on release branch

### Multi-Branch Strategy

**Recommended workflow with multiple branches:**

```
Feature Branch 0 ─────────\
                           \
Release Branch  0───0───0───0───● (Release candidate)
                           /
Main Branch    ─0───0────0────● (Production)


Flow:
1. Feature branch created from main
2. Work done on feature branch
3. Feature merged to release branch (testing)
4. After validation, merged to main (production)
5. Release branch serves as staging environment
```

### Branch Hierarchy

```
PRIORITY (Stability):
1. Main Branch ⭐⭐⭐⭐⭐ (Production)
   └─ Only merged, tested code
   
2. Release Branch ⭐⭐⭐⭐ (Staging)
   └─ Candidate for production
   
3. Feature Branch ⭐⭐⭐ (Development)
   └─ Work in progress
```

### Implementation Example

#### Feature to Main Branch

```
1. Create feature branch from main
   $ git checkout -b feature/login-page

2. Complete feature work
   $ git add .
   $ git commit -m 'add login page'

3. Push feature branch
   $ git push -u origin feature/login-page

4. Create pull request to main
   [Via GitHub/Azure DevOps UI]

5. Code review and merge
   ✅ Merged to main
   ✅ Feature branch deleted
```

#### Feature to Release Branch

```
1. Merge same feature to release
   $ git checkout release/v2.0
   $ git merge feature/login-page

2. Test on release branch
   [Run QA tests]

3. When ready for production
   $ git checkout main
   $ git merge release/v2.0

4. Tag the release
   $ git tag -a "v2.0" -m "Release v2.0"
   $ git push origin v2.0
```

### Benefits of Multi-Branch Strategy

| Aspect | Benefit |
|--------|---------|
| **Quality Control** | Main always production-ready |
| **Parallel Development** | Multiple features simultaneously |
| **Easy Rollback** | Release branch for quick fixes |
| **Testing Separation** | Release branch for staging |
| **Audit Trail** | Clear merge history and PRs |
| **Release Management** | Organized release process |

---

## Quick Reference: Tag Commands

```bash
# Create annotated tag
git tag -a "v1.0" -m "Release version 1.0"

# Create lightweight tag
git tag "v1.0"

# List all tags
git tag

# Show tag details
git show v1.0

# Push tag to remote
git push origin v1.0

# Push all tags
git push origin --tags

# Delete local tag
git tag -d v1.0

# Delete remote tag
git push origin --delete v1.0

# Checkout specific tag
git checkout v1.0

# Create branch from tag
git checkout -b new-branch v1.0
```

---

## Best Practices Summary

### ✅ DO

- **Create frequent, small feature branches** for easier management
- **Use descriptive branch names** (feature/login, bugfix/navbar)
- **Keep branches short-lived** (complete within days)
- **Always use pull requests** for merging to main
- **Tag releases** for easy reference and deployment
- **Protect main branch** with rules and reviews
- **Delete merged branches** to keep repository clean
- **Review code** before merging to main
- **Test thoroughly** before tagging releases
- **Document breaking changes** in release notes

### ❌ DON'T

- **Push directly to main** without PR
- **Keep branches long-lived** (more than a week)
- **Ignore merge conflicts** - resolve them properly
- **Mix multiple features** in one branch/PR
- **Force push** to shared branches
- **Commit to main** during feature development
- **Create tags** on incomplete or untested code
- **Skip code reviews** before merge
- **Forget to delete** merged branches
- **Use vague commit messages** in releases

---

## Troubleshooting

### Problem: Branch Creation Failed

```bash
Error: Reference already exists
```

**Solution:**

```bash
$ git branch -a  # Check existing branches
$ git branch -d old-name  # Delete if no longer needed
$ git checkout -b new-name  # Create new branch
```

### Problem: Tag Already Exists

```bash
Error: Tag 'v1.0' already exists
```

**Solution:**

```bash
$ git tag -d v1.0  # Delete old tag locally
$ git push origin --delete v1.0  # Delete on remote
$ git tag -a "v1.0" -m "new message"  # Recreate
```

### Problem: Cannot Merge Due to Conflicts

```bash
Error: Automatic merge failed; fix conflicts then commit
```

**Solution:**

1. Resolve conflicts in files
2. Stage resolved files: `git add .`
3. Commit merge: `git commit -m 'Merge with conflict resolution'`
4. Push: `git push`

---

## Additional Resources

- 📚 [Git Docs - Tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging)
- 📚 [Git Flow Cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/)
- 📚 [GitHub Branch Protection](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository)
- 📚 [Azure DevOps Branching Strategy](https://docs.microsoft.com/en-us/azure/devops/repos/git/git-branching-strategy)


# Git CLI Cheat Sheet: Beginner to Expert Developer

A practical reference for learning Git, configuring a new development system, managing source code, collaborating with teams, resolving conflicts, and recovering from mistakes.

**Audience:** Beginners to expert developers  
**Platforms:** Windows, Linux, macOS

---

## 1. Install Git on Your Local System

Before running Git commands, install Git and configure it on your computer.

### Windows

**Option 1: Download the official installer**

1. Open https://git-scm.com/install/
2. Download Git for Windows.
3. Run the installer and follow the setup wizard.
4. Keep the recommended defaults unless you have a specific preference.
5. Open Git Bash, PowerShell, or Command Prompt.

**Option 2: Install using Windows Package Manager**

Open PowerShell or Command Prompt:

```powershell
winget install --id Git.Git -e
```

Close and reopen your terminal after installation.

### Ubuntu / Debian Linux

```bash
sudo apt update
sudo apt install git -y
```

### Fedora Linux

```bash
sudo dnf install git
```

### macOS

Using Homebrew:

```bash
brew install git
```

Alternatively, macOS may offer Apple's command-line developer tools:

```bash
xcode-select --install
```

### Verify installation

```bash
git --version
```

Example output:

```text
git version 2.x.x
```

The exact version depends on your operating system and installed release.

---

## 2. Configure Git for the First Time

Git should know your name and email before you create commits.

### Configure your global identity

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Example:

```bash
git config --global user.name "Mohit Tiwari"
git config --global user.email "mohit@example.com"
```

Use your actual preferred name and the email associated with your Git hosting account when appropriate.

**What does `--global` mean?**

- `--system` — configuration for all users on the computer; often requires administrator privileges.
- `--global` — configuration for your operating-system user, across repositories.
- `--local` — configuration for the current repository only.

Repository-local settings override global settings, and global settings override system settings.

### Verify your identity

```bash
git config --global user.name
git config --global user.email
```

### View all effective configuration

```bash
git config --list
git config --show-origin --list
```

The second command also shows where each configuration value came from. Be careful when sharing its output because configuration may include private paths or URLs.

### Set the default branch name

```bash
git config --global init.defaultBranch main
```

New repositories created using `git init` will normally start with a branch named `main`.

### Set a default text editor

For Visual Studio Code:

```bash
git config --global core.editor "code --wait"
```

For Vim:

```bash
git config --global core.editor "vim"
```

The editor must be installed and available through your terminal's `PATH`.

### Configure line endings on Windows

A common Windows configuration is:

```bash
git config --global core.autocrlf true
```

On macOS and Linux, a common choice is:

```bash
git config --global core.autocrlf input
```

Choose a policy consistent with your team's repository and `.gitattributes` file. Teams can use `.gitattributes` to enforce consistent line endings across operating systems.

### Useful optional configuration

```bash
git config --global fetch.prune true
git config --global pull.ff only
```

- `fetch.prune true` removes stale remote-tracking branch references when fetching.
- `pull.ff only` permits a pull only when it can fast-forward; it does not automatically merge divergent histories.

### Remove an incorrect configuration value

```bash
git config --global --unset user.email
```

Then set the correct value again:

```bash
git config --global user.email "correct-email@example.com"
```

---

## 3. Authenticate with GitHub or Another Git Host

Git configuration identifies the author of commits. **It does not sign you in to GitHub or authorize repository access.** Authentication is a separate step.

You can typically use HTTPS with a credential manager or SSH with a key pair.

### Option A: HTTPS authentication

Clone a repository:

```bash
git clone https://github.com/username/project.git
```

When authentication is required, use the credential manager or authentication flow supported by your Git host. For GitHub, use a supported browser sign-in or personal access token when requested; ordinary account passwords are not accepted for Git operations over HTTPS.

Never place a token directly in a command or remote URL where it could be saved in shell history or Git configuration.

### Option B: Set up SSH authentication

#### Step 1: Check for existing SSH keys

```bash
ssh -V
ssh-add -l
```

`ssh-add -l` may report that no identities are loaded. That does not necessarily mean you have no key files.

Check your SSH directory:

```bash
ls -la ~/.ssh
```

On Windows, run this in Git Bash, or inspect your user profile's `.ssh` directory in PowerShell.

#### Step 2: Generate an SSH key

```bash
ssh-keygen -t ed25519 -C "you@example.com"
```

Follow the prompts and set a passphrase when possible. If you already have a key, avoid overwriting it unless you intend to replace it.

#### Step 3: Start the SSH agent and add the key

On Linux or macOS, a common shell setup is:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

On Windows, use Git Bash or the Windows OpenSSH agent, depending on your setup. In PowerShell, the Windows agent can be enabled if permitted:

```powershell
Get-Service ssh-agent
Set-Service -Name ssh-agent -StartupType Automatic
Start-Service ssh-agent
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

Starting or configuring the Windows service may require an elevated PowerShell session.

#### Step 4: Copy the public key

Linux/macOS/Git Bash:

```bash
cat ~/.ssh/id_ed25519.pub
```

PowerShell:

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

Copy the complete public key and add it to your Git hosting account's SSH key settings. **Never upload or share the private key** (`id_ed25519` without `.pub`).

#### Step 5: Test SSH access

For GitHub:

```bash
ssh -T git@github.com
```

The first connection may ask you to verify the server's host key. Verify the fingerprint against the hosting provider's official documentation before accepting it.

#### Step 6: Clone using SSH

```bash
git clone git@github.com:username/project.git
```

If you already cloned using HTTPS and want to switch to SSH:

```bash
git remote set-url origin git@github.com:username/project.git
git remote -v
```

---

## 4. Create or Download Your First Repository

### Initialize Git in an existing project

```bash
cd my-project
git init
git status
```

### Create a new project from scratch

```bash
mkdir my-project
cd my-project
git init
```

Create a file, stage it, and make the first commit:

```bash
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"
```

### Clone an existing repository

```bash
git clone https://github.com/username/project.git
cd project
```

### Clone a specific branch

```bash
git clone --branch develop https://github.com/username/project.git
```

### Clone with limited history

```bash
git clone --depth 1 https://github.com/username/project.git
```

A shallow clone downloads only limited commit history.

---

## 5. Git Fundamentals

| Term | Explanation |
|---|---|
| Repository | A project tracked by Git |
| Working directory | The files you are currently editing |
| Staging area | Changes selected for the next commit |
| Commit | A saved snapshot of project changes |
| Branch | An independent line of development |
| HEAD | The commit or branch currently checked out |
| Remote | Another copy of the repository, often hosted online |
| Merge | Combines histories from different branches |
| Rebase | Replays commits onto a different base |
| Cherry-pick | Applies selected commits to the current branch |
| Tag | A named reference often used for releases |
| Conflict | A change Git cannot combine automatically |
| `origin` | Conventional name for the default remote |
| `main` | Common name for the primary branch |

### Basic Git workflow

```text
Edit files
   |
   v
git add
   |
   v
Staging area
   |
   v
git commit
   |
   v
Local repository
   |
   v
git push
   |
   v
Remote repository
```

- `git add` prepares changes.
- `git commit` saves them locally.
- `git push` sends commits to a remote repository.

---

## 6. Check Repository Status and Changes

```bash
git status
git status --short
git diff
git diff --staged
git diff --check
```

- `git status` shows changed, staged, and untracked files.
- `git diff` shows unstaged changes.
- `git diff --staged` shows changes prepared for commit.
- `git diff --check` checks for common whitespace errors.

Compare branches:

```bash
git diff main..feature/login
git diff --name-only
```

---

## 7. Stage and Commit Changes

```bash
git add app.js
git add app.js README.md
git add .
git add -u
```

Commit staged changes:

```bash
git commit -m "Add login validation"
```

Commit tracked changes directly:

```bash
git commit -am "Fix validation error"
```

`-a` does not include new untracked files.

Amend the latest commit:

```bash
git commit --amend -m "Correct login validation message"
```

Amending rewrites the latest commit. Avoid amending commits that others already depend on unless your team agrees.

---

## 8. Review Commit History

```bash
git log
git log --oneline
git log --oneline --graph --decorate --all
git log -5
git show HEAD
git show <commit-id>
git log --oneline -- src/app.js
git blame src/app.js
```

`git blame` shows which commit last changed each line; it is a history investigation tool, not proof of responsibility for a defect.

---

## 9. Branch Management

```bash
git branch
git branch -a
git branch feature/login
git switch feature/login
git switch -c feature/login
```

Rename the current branch:

```bash
git branch -m feature/new-login
```

Delete a merged local branch:

```bash
git branch -d feature/login
```

Force-delete a local branch:

```bash
git branch -D feature/login
```

Delete a remote branch:

```bash
git push origin --delete feature/login
```

Create a branch from a specific commit:

```bash
git log --oneline --all
git switch -c hotfix/payment <commit-id>
```

Create a branch from the latest commit before a date:

```bash
git fetch origin
git rev-list -1 --before="2025-01-15 23:59:59" origin/main
git switch -c maintenance/jan-15 <commit-id>
```

Git branches point to commits, not dates. Inspect the selected commit before using it.

---

## 10. Remote Repository Commands

```bash
git remote -v
git remote add origin https://github.com/username/project.git
git remote set-url origin https://github.com/username/new-project.git
git fetch origin
git fetch --prune origin
git pull
git pull --ff-only origin main
git push
git push -u origin feature/login
git branch -vv
```

- `fetch` downloads remote information without integrating it.
- `pull` fetches and integrates changes.
- `push` sends local commits to the remote.
- `-u` configures the upstream branch for future pushes and pulls.

---

## 11. Merge Branches

Merge a current main branch into your feature branch:

```bash
git fetch origin
git switch feature/login
git merge origin/main
```

Merge a feature branch into main:

```bash
git switch main
git merge --no-ff feature/login
```

In team environments, follow your pull-request and branch-protection process.

Abort an in-progress merge:

```bash
git merge --abort
```

---

## 12. Rebase — Clean Up Commit History

Replay your feature commits onto the latest main:

```bash
git fetch origin
git switch feature/login
git rebase origin/main
```

Resolve conflicts, then continue:

```bash
git add <resolved-file>
git rebase --continue
```

Abort the rebase:

```bash
git rebase --abort
```

Interactive rebase:

```bash
git rebase -i HEAD~3
```

Common interactive actions:

- `pick` — keep the commit.
- `reword` — change the commit message.
- `edit` — pause to amend the commit.
- `squash` — combine with the previous commit and edit the message.
- `fixup` — combine with the previous commit, usually discarding the message.
- `drop` — remove the commit from rewritten history.

Create a fix-up commit and autosquash it:

```bash
git commit --fixup <target-commit-id>
git rebase -i --autosquash <base-commit-id>
```

**Golden rule:** Do not rebase shared commits that other developers may already depend on unless the team agrees to rewrite the history.

---

## 13. Cherry-Pick — Apply Specific Commits

Apply one commit:

```bash
git switch release/1.2
git cherry-pick <commit-id>
```

Apply several commits:

```bash
git cherry-pick <commit-id-1> <commit-id-2>
```

Apply a range:

```bash
git cherry-pick A..C
```

On a straightforward linear history, `A..C` selects commits reachable from `C` but not from `A`, so `A` itself is excluded. To include `A` as well:

```bash
git cherry-pick A^..C
```

Apply changes without immediately creating commits:

```bash
git cherry-pick --no-commit <commit-id>
```

Resolve conflicts:

```bash
git add <resolved-file>
git cherry-pick --continue
```

Abort:

```bash
git cherry-pick --abort
```

Use cherry-pick for targeted fixes, such as applying a bug fix to a release branch without merging unrelated changes.

---

## 14. Undo Changes Safely

Discard unstaged changes to a file:

```bash
git restore app.js
```

Unstage a file but keep its edits:

```bash
git restore --staged app.js
```

Restore a file from a specific commit:

```bash
git restore --source=<commit-id> -- app.js
```

Create a new commit that reverses a previous commit:

```bash
git revert <commit-id>
```

Undo the latest commit while keeping changes staged:

```bash
git reset --soft HEAD~1
```

Undo the latest commit while keeping changes unstaged:

```bash
git reset --mixed HEAD~1
```

Reset the current branch and tracked working tree:

```bash
git reset --hard <commit-id>
```

**Warning:** `reset --hard` discards tracked working-tree and index changes. Use it only after verifying that you do not need those changes.

Preview untracked-file cleanup:

```bash
git clean -n
```

Delete untracked files:

```bash
git clean -f
```

To include untracked directories, use `git clean -fd`. This can permanently delete untracked work.

---

## 15. Recover with Reflog

```bash
git reflog
git show HEAD@{1}
git branch recovery/backup <commit-id>
```

The reflog records recent local reference movements and can help recover from accidental resets or branch changes. Its entries expire, so do not treat it as a permanent backup.

---

## 16. Resolve Conflicts

Check the current operation and conflicted files:

```bash
git status
```

Open each conflicted file and remove the conflict markers after choosing the correct final content:

```text
<<<<<<< HEAD
Current-side changes
=======
Incoming changes
>>>>>>> other-branch
```

Stage the resolved file:

```bash
git add <resolved-file>
```

Continue the appropriate operation:

```bash
git commit
git rebase --continue
git cherry-pick --continue
```

Use the command corresponding to the operation currently in progress.

Abort if necessary:

```bash
git merge --abort
git rebase --abort
git cherry-pick --abort
```

---

## 17. Stash Temporary Changes

```bash
git stash
git stash -u
git stash push -m "WIP: payment validation"
git stash list
git stash apply
git stash pop
git stash apply stash@{2}
git stash drop stash@{0}
git stash clear
```

- `stash` temporarily saves tracked changes.
- `stash -u` also includes untracked files.
- `apply` restores changes while retaining the stash.
- `pop` restores changes and removes the stash if successful.
- `clear` deletes all stash entries and is destructive.

---

## 18. Tags and Releases

```bash
git tag
git tag v1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"
git tag -a v1.0.1 <commit-id> -m "Release version 1.0.1"
git push origin v1.0.0
git push origin --tags
git tag -d v1.0.0
git push origin --delete v1.0.0
```

Annotated tags are generally preferable for release records because they store tag metadata and a message.

---

## 19. Search and Investigate History

```bash
git log --grep="login"
git log --all --oneline -- app.js
git grep "connectionString"
git grep "connectionString" <commit-id>
git log -S"connectionString" --all --oneline
git show --stat <commit-id>
git show --format=fuller <commit-id>
```

`git log -S` finds commits where the number of occurrences of a string changed. Use `git log -G` for regular-expression searches in diffs.

---

## 20. Advanced Debugging with Bisect

```bash
git bisect start
git bisect bad
git bisect good <known-good-commit>
```

Test the candidate commit and mark it:

```bash
git bisect good
```

Or:

```bash
git bisect bad
```

Repeat until Git identifies the first bad commit in the searched history.

Finish:

```bash
git bisect reset
```

Automate with a suitable test script:

```bash
git bisect start
git bisect bad
git bisect good <known-good-commit>
git bisect run ./run-tests.sh
git bisect reset
```

---

## 21. Advanced Worktrees

Worktrees let you work on multiple branches in separate directories without cloning the repository again.

```bash
git worktree add -b feature/reporting ../reporting feature/base
git worktree add ../hotfix hotfix/payment
git worktree list
git worktree remove ../hotfix
```

The first command creates a new branch from `feature/base`. The second checks out an existing branch in another directory.

---

## 22. Submodules

```bash
git submodule add https://github.com/example/library.git external/library
git clone --recurse-submodules https://github.com/example/project.git
git submodule update --init --recursive
git submodule update --remote
```

A submodule references another repository at a particular commit. Review and commit pointer changes in the parent repository when appropriate.

---

## 23. `.gitignore` and Repository Hygiene

Example `.gitignore`:

```gitignore
.env
.env.*
!.env.example
node_modules/
bin/
obj/
dist/
build/
.vscode/
.idea/
*.log
```

Remove a file from Git tracking but keep it locally:

```bash
git rm --cached .env
```

Then add the file to `.gitignore` and commit the change.

**Security warning:** Removing a secret from the latest commit does not erase it from previous commits. If a secret was exposed, revoke or rotate it immediately and follow your organization's history-cleanup procedure.

Check repository files and integrity:

```bash
git ls-files
git status --short
git fsck
```

---

## 24. Advanced Push and Pull

Push normally:

```bash
git push origin feature/login
```

Push after rewriting a private feature branch:

```bash
git push --force-with-lease origin feature/login
```

`--force-with-lease` is safer than a plain force push, but still rewrites remote history. Avoid force-pushing shared or protected branches without explicit team authorization.

Pull with rebase:

```bash
git pull --rebase origin main
```

Pull only if fast-forwarding is possible:

```bash
git pull --ff-only origin main
```

Use your team's agreed integration strategy.

---

## 25. Common Errors and Fixes

**`fatal: not a git repository`**

```bash
cd path/to/project
git status
```

**`nothing to commit`**

```bash
git status
git diff
git diff --staged
```

**`rejected (non-fast-forward)`**

```bash
git fetch origin
git log --oneline --graph --decorate --all
```

Review the divergence and integrate changes using your team's merge or rebase strategy.

**`src refspec main does not match any`**

```bash
git branch
git status
```

Check the branch name and whether an initial commit exists.

**Authentication failed**

Verify the remote URL and use a supported credential manager, browser authentication flow, or SSH key.

---

## 26. Workflow: Feature Development

```bash
# Update remote-tracking information
git fetch origin

# Start from the latest main
git switch main
git pull --ff-only origin main

# Create a feature branch
git switch -c feature/user-profile

# Make changes and inspect them
git status
git diff

# Stage and commit
git add .
git diff --staged
git commit -m "Add user profile page"

# Rebase on the latest main
git fetch origin
git rebase origin/main

# Push the branch
git push -u origin feature/user-profile
```

Then open a pull request and follow your team's review and merge process.

---

## 27. Workflow: Production Hotfix

```bash
git fetch origin
git switch release/1.2
git pull --ff-only origin release/1.2

git switch -c hotfix/payment-error

# Make and test the fix
git add src/payment.js
git commit -m "Fix payment validation error"

git push -u origin hotfix/payment-error
```

After review and release, ensure the fix also reaches the main development line. Depending on the team's workflow, merge the hotfix branch or cherry-pick the fix commit.

---

## 28. Quick Reference

| Task | Command |
|---|---|
| Check Git version | `git --version` |
| Set name | `git config --global user.name "Your Name"` |
| Set email | `git config --global user.email "you@example.com"` |
| List configuration | `git config --list` |
| Initialize repository | `git init` |
| Clone repository | `git clone <url>` |
| Check status | `git status` |
| Stage changes | `git add .` |
| Commit | `git commit -m "message"` |
| Show history | `git log --oneline --graph --all` |
| Create branch | `git switch -c branch-name` |
| Switch branch | `git switch branch-name` |
| Fetch updates | `git fetch origin` |
| Push changes | `git push` |
| Merge branch | `git merge branch-name` |
| Rebase branch | `git rebase origin/main` |
| Cherry-pick commit | `git cherry-pick <commit-id>` |
| Reverse a commit | `git revert <commit-id>` |
| Unstage a file | `git restore --staged file` |
| Save temporary changes | `git stash -u` |
| Recover reference history | `git reflog` |
| Find bug-introducing commit | `git bisect` |
| List worktrees | `git worktree list` |

---

## 29. Recommended Learning Path

- **Beginner:** Installation, identity configuration, `init`, `clone`, `status`, `add`, `commit`, `diff`, `log`.
- **Intermediate:** Branches, remotes, merge, pull, push, stash, `.gitignore`, conflict resolution.
- **Advanced:** Rebase, cherry-pick, reset modes, revert, reflog, tags, upstream tracking.
- **Expert:** Bisect, worktrees, submodules, hooks, signed commits, repository maintenance, release strategies, CI/CD integration.

### Official documentation

- Installation: https://git-scm.com/install/
- Git reference: https://git-scm.com/docs
- Git Book: https://git-scm.com/book/en/v2
- Rebase: https://git-scm.com/docs/git-rebase
- Cherry-pick: https://git-scm.com/docs/git-cherry-pick
- Reset: https://git-scm.com/docs/git-reset
- Restore: https://git-scm.com/docs/git-restore
- Reflog: https://git-scm.com/docs/git-reflog

**Final tip:** Configure Git once per system, verify your identity, authenticate separately with your hosting provider, and practice destructive commands in a disposable test repository before using them on real work.
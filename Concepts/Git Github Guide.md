___

# Obsidian → Git → GitHub: Complete Workflow
#### 1. The big picture
| Layer             | What it is                              | Purpose                                     |
| ----------------- | --------------------------------------- | ------------------------------------------- |
| Obsidian          | Your notes/vault                        | Where you actually write                    |
| Git               | Version-control system on your computer | Keeps a history of your notes               |
| SSH               | Secure authentication method            | Lets your computer authenticate with GitHub |
| GitHub            | Online Git repository                   | Stores your Git history remotely            |
| GitHub repository | `obsidian-notes` (or your chosen name)  | Online backup of your vault                 |
```
                 YOUR COMPUTER
┌───────────────────────────────────────┐
│                                       │
│        Obsidian Vault                 │
│        ├── Notes                      │
│        ├── Attachments                │
│        └── Folders                    │
│                │                      │
│                ↓                      │
│              Git                      │
│                │                      │
│          Local commits                │
│                │                      │
└────────────────┼──────────────────────┘
                 │
                 │ SSH
                 ↓
          ┌───────────────┐
          │    GitHub     │
          │               │
          │ obsidian-notes│
          └───────────────┘
```

### Step 1 — Initial Git setup
| # | Command | What it does | Why |
|---|---|---|---|
| 1 | `git --version` | Checks whether Git is installed | Make sure Git is available |
| 2 | `git config --global user.name "Your Name"` | Sets your Git name | Git records who makes commits |
| 3 | `git config --global user.email "your@email.com"` | Sets your Git email | Identifies you in commits |
| 4 | `git config --global user.name` | Displays configured name | Verify configuration |
| 5 | `git config --global user.email` | Displays configured email | Verify configuration |

### Step 2 — Locate your Obsidian vault
| # | Command/action | Purpose |
|---|---|---|
| 1 | Open Obsidian | Identify your active vault |
| 2 | Locate the vault folder using Obsidian/file manager | Find where the notes physically live |
| 3 | Open that folder in Terminal | Make Terminal operate inside the vault |
| 4 | `pwd` | Shows your current directory |
| 5 | `ls` | Shows files/folders in the vault |
| 6 | `ls -la` | Shows normal + hidden files |
| 7 | `find . -maxdepth 2 -type d` | Shows folders up to two levels deep |
| 8 | `find . -maxdepth 2 -type f` | Shows files up to two levels deep |

### Step 3 — Create `.gitignore`
| # | Command | Purpose |
|---|---|---|
| 1 | `touch .gitignore` | Creates `.gitignore` |
| 2 | `nano .gitignore` | Opens it for editing |
| 3 | Add rules such as `Private/` | Tells Git to ignore those folders |
| 4 | `Ctrl + O` | Save in nano |
| 5 | `Enter` | Confirm filename |
| 6 | `Ctrl + X` | Exit nano |
| 7 | `cat .gitignore` | Check its contents |

### Srep 4 — Initialize Git
| # | Command | Meaning |
|---|---|---|
| 1 | `pwd` | Confirm you're in the vault |
| 2 | `ls -la` | Confirm `.gitignore` and vault files are present |
| 3 | `git init -b main` | Creates a Git repository using `main` |
| 4 | `git status` | Shows Git's current state |
| 5 | `ls -la .git` | Shows Git's internal directory |
The important command is:
```bash
git init -b main
```
This creates:
```text
Obsidian Vault/
├── .git/
├── .gitignore
├── Notes/
├── Projects/
└── ...
```
###### Important distinction
`git init` **does not upload anything**.
At this point:
```text
Your notes
    ↓
Local Git repository
    ↓
Your computer only
```

### Step 5 — Git's three-stage workflow
| Stage | What happens | Command |
|---|---|---|
| Working directory | You edit your Obsidian notes | Obsidian |
| Staging area | You select changes for the next snapshot | `git add` |
| Repository/history | You permanently record a snapshot | `git commit` |
| Remote/GitHub | You send commits online | `git push` |

### Srep 6 — First staging process
| Command | Purpose |
|---|---|
| `git status` | See what Git sees |
| `git add .gitignore` | Stage only `.gitignore` |
| `git status` | Confirm `.gitignore` is staged |
| `git diff --cached` | Inspect staged changes |
| `git add .` | Stage all non-ignored files |
| `git status` | Inspect what is staged |
| `git diff --cached --name-only` | Show only staged filenames |

### Step 7 — Create your first commit

Once the staged files are safe:

```bash
git commit -m "Initial Obsidian notes"
```

##### The flow:
```text
Obsidian files
      ↓
git add .
      ↓
Staging area
      ↓
git commit
      ↓
📸 Local snapshot
```
###### Verify

Run:
```bash
git status
```

Ideally:
```
nothing to commit, working tree clean
```

Then:
```bash
git log --oneline
```
You should see something similar to:
```bash
abc1234 (HEAD -> main) Initial Obsidian notes
```

### Step 8 — SSH setup
Now we move to authentication.
The goal is:
```text
Your computer
     │
     │ SSH
     ↓
   GitHub
```
#### 1. SSH Key Generation Step
| #   | Command/action                                      | Purpose                              |
| --- | --------------------------------------------------- | ------------------------------------ |
| 1   | `ls -la ~/.ssh`                                     | Check whether SSH keys already exist |
| 2   | `ssh-keygen -t ed25519 -C "your_email@example.com"` | Create Ed25519 SSH key pair          |
| 3   | Press Enter at key-file location                    | Use default location                 |
| 4   | Enter passphrase                                    | Protect private key                  |
| 5   | Enter passphrase again                              | Confirm                              |
| 6   | `ls -l ~/.ssh/id_ed25519*`                          | Confirm keys exist                   |

This creates:
```
~/.ssh/
├── id_ed25519
└── id_ed25519.pub
```

| File | Meaning | What to do |
|---|---|---|
| `id_ed25519` | Private key 🔐 | Keep secret |
| `id_ed25519.pub` | Public key 🔓 | Give to GitHub |
#### 2. SSH agent Step
`ssh-agent` is a **small background program that manages your SSH private keys for you**.

The easiest way to think about it is:
> **SSH key = your identity/credential**  
> **`ssh-agent` = a helper that keeps your unlocked key available so SSH can use it**

| #   | Command                     | Purpose                     |
| --- | --------------------------- | --------------------------- |
| 1   | `eval "$(ssh-agent -s)"`    | Start SSH agent             |
| 2   | `ssh-add ~/.ssh/id_ed25519` | Load private key into agent |
| 3   | `ssh-add -l`                | Check loaded keys           |

Expected:
```
Agent pid 12345
```

Then:
```
Identity added: /home/yourname/.ssh/id_ed25519
```

And:
```bash
ssh-add -l
```
should show your Ed25519 key.

###### The Complete Picture
         YOUR COMPUTER

        ~/.ssh/id_ed25519
          Private key 🔐
                │
                │ ssh-add
                ↓
          ┌─────────────┐
          │ ssh-agent   │
          │             │
          │ Key loaded  │
          └──────┬──────┘
                 │
                 │ SSH
                 ↓
             GitHub
                 │
                 ↓
       Your public key 🔓
       is registered here

### Step 9 — Add public key to GitHub
| Step | Action           | What to do                                           |
| ---: | ---------------- | ---------------------------------------------------- |
|    1 | Open GitHub      | Open GitHub in your browser                          |
|    2 | Sign in          | Sign in to the GitHub account you want to use        |
|    3 | Profile picture  | Click your profile picture in the top-right          |
|    4 | Settings         | Click **Settings**                                   |
|    5 | SSH and GPG keys | Select **SSH and GPG keys**                          |
|    6 | New SSH key      | Click **New SSH key**                                |
|    7 | Title            | Give it a recognizable title, e.g. `My Linux Laptop` |
|    8 | Key type         | Choose **Authentication Key**                        |
|    9 | Key              | Paste the contents of `cat ~/.ssh/id_ed25519.pub`    |
|   10 | Add SSH key      | Click **Add SSH key**                                |

###### Never paste this into GitHub:
```
id_ed25519
```
Only:
```
id_ed25519.pub
```

### Step 10 — Test SSH authentication

Run:
```bash
ssh -T git@github.com
```

The first time, SSH may ask whether you trust GitHub's host.
After verification, type:
```text
yes
```

A successful response looks roughly like:
```
Hi YOUR_USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
```
That is **success**, not an error.

### Step 11 — Create the GitHub repository

On GitHub:

| Setting | What we chose |
|---|---|
| Repository name | e.g. `obsidian-notes` |
| Visibility | Private |
| README | ❌ Don't add |
| `.gitignore` | ❌ Don't add |
| License | ❌ Don't add |
### Step 12 — Connect local Git to GitHub
```bash
git remote add origin git@github.com:YOUR_USERNAME/obsidian-notes.git
```

Then verify:
```bash
git remote -v
```

You want:
```bash
origin  git@github.com:YOUR_USERNAME/obsidian-notes.git (fetch)
origin  git@github.com:YOUR_USERNAME/obsidian-notes.git (push)
```
### Step 13 — Test the repository connection
Run:
```bash
git ls-remote origin
```

This asks GitHub:
> "Can I access this particular repository?"

It does **not** upload your notes.

There are two levels of testing:

| Command | Tests |
|---|---|
| `ssh -T git@github.com` | Can GitHub recognize your SSH key? |
| `git ls-remote origin` | Can you access this specific repository? |

### Step 14 — First push
Run:
```bash
git push -u origin main
```
###### Meaning
| Part | Meaning |
|---|---|
| `git` | Use Git |
| `push` | Send local commits to remote |
| `-u` | Remember the upstream relationship |
| `origin` | Your GitHub repository |
| `main` | Your main branch |

This is the command that finally does:
```
YOUR COMPUTER
      │
      │ SSH
      ↓
    GITHUB
```
Your notes should then appear in your GitHub repository.

## Complete Picture
```
STEP 1
git --version
        ↓
STEP 2
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
        ↓
STEP 3
cd "YOUR OBSIDIAN VAULT"
        ↓
STEP 4
touch .gitignore
nano .gitignore
        ↓
STEP 5
git init -b main
        ↓
STEP 6
git status
        ↓
STEP 7
git add .
git status
git diff --cached --name-only
        ↓
STEP 8
git commit -m "Initial Obsidian notes"
        ↓
STEP 9
ls -la ~/.ssh
ssh-keygen -t ed25519 -C "your@email.com"
        ↓
STEP 10
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
        ↓
STEP 11
cat ~/.ssh/id_ed25519.pub
        ↓
STEP 12
Add PUBLIC key to GitHub
        ↓
STEP 13
ssh -T git@github.com
        ↓
STEP 14
Create PRIVATE GitHub repository
        ↓
STEP 15
git remote set-url origin git@github.com:USERNAME/REPO.git
        ↓
STEP 16
git remote -v
git ls-remote origin
        ↓
STEP 17
git push -u origin main
        ↓
🎉 NOTES ON GITHUB
```

```
┌───────────────────────┐
│   Your Linux computer │
│                       │
│   Obsidian Vault      │
│        +              │
│   Git history         │
└───────────┬───────────┘
            │
            │ SSH + push
            ↓
┌───────────────────────┐
│        GitHub         │
│                       │
│   obsidian-notes      │
│   + Git history       │
└───────────────────────┘
```

___
## Normal Workflow from This Point Onward
| Order | Command/action | What it means |
|---|---|---|
| 1 | Edit notes in Obsidian | Make your changes |
| 2 | `git status` | See what changed |
| 3 | `git add .` | Stage changes |
| 4 | `git status` | Check what will be committed |
| 5 | `git diff --cached` | Inspect the actual staged changes |
| 6 | `git commit -m "Describe changes"` | Create a local snapshot |
| 7 | `git push` | Upload commits to GitHub |

___
## What if a file is not supposed to be on Git/Github, but it is there?
So if the file is **already on GitHub**, you need to do two things:
1. Tell Git to stop tracking it.
2. Add it to `.gitignore` so it doesn't get tracked again.

##### Example
Suppose this file is currently on GitHub:
```text
Private/Secret.md
```

You now want:
```text
Private/
```
to be ignored.

###### Step 1 — Add it to `.gitignore`
Open `.gitignore`:
```bash
nano .gitignore
```

Add:
```text
Private/
```

Save with:
```text
Ctrl + O
Enter
Ctrl + X
```
###### Step 2 — Tell Git to stop tracking the existing file
This is the crucial command:
```bash
git rm --cached -r Private/
```

The `--cached` part is **very important**.
It means:
> Remove this from Git's tracking, but **keep the actual files on my computer**.

So:
```text
Your computer                    Git/GitHub

Private/Secret.md  ───────────X──> tracked
       │
       └── stays on computer ✅
```
It does **not** delete your local `Private/` folder.

###### Step 3 — Check what Git plans to do
Run:
```bash
git status
```

You should see something like:
```text
Changes to be committed:

    deleted: Private/Secret.md
```
Don't panic when you see `deleted`.
It means:
> "Git is going to stop storing this file in the repository."

It does **not** mean the local file has been deleted, because you used `--cached`.

You can verify:
```bash
ls Private/
```
Your file should still be there.

###### Step 4 — Check `.gitignore`
Run:
```bash
git status --ignored
```
You should eventually see your private folder under ignored files.

###### Step 5 — Commit the change
Once you've confirmed the local file still exists:
```bash
git commit -m "Stop tracking private notes"
```
This creates a commit saying:
> Remove these files from the current repository while keeping them locally.
###### Step 6 — Push to GitHub
Now:
```bash
git push
```
GitHub will update.
The file will **disappear from the current GitHub version** of the repository.
Your local copy remains.

### ⚠️ One very important warning
If the file contained a **password, API key, private key, or other secret**, simply removing it from the current GitHub version is **not enough**.

Why?
Because Git keeps history.
For example:
```text
Commit 1
   ↓
Secret.md exists 🔴
   ↓
Commit 2
   ↓
Secret.md removed
```

Someone with access to the repository may still be able to find the old version in Git history.

If an actual password/API key/private key was exposed, **change/revoke the credential immediately** and then we can deal with cleaning the Git history separately.

#### The Key Distinction
```
.gitignore
    ↓
"Don't track this in the future."

git rm --cached
    ↓
"Stop tracking this thing that you're
already tracking."
```
You need **both** when the file was already tracked.

___
## Clearing History of Git/Github

**“Clear the history of complete Git of a folder”** mean:

> “I want to keep the current files in my Obsidian folder, but completely erase the Git history for this repository and start Git again from zero.”

**Important:** this destroys the existing local Git history. If you've already pushed that history to GitHub, you also need to replace the history on GitHub. 

#### Local Git History Deletion and Making New Git Repo
| Step | Command                                    | Purpose                       |
| ---- | ------------------------------------------ | ----------------------------- |
| 1    | `cd "Your Vault"`                          | Go to your Obsidian vault     |
| 2    | `cp -a "Your Vault" "Your Vault - Backup"` | Create a safety backup        |
| 3    | `rm -rf .git`                              | Delete the local Git history  |
| 4    | `git init -b main`                         | Create a fresh Git repository |
| 5    | `cat .gitignore`                           | Check your `.gitignore`       |
| 6    | `git status`                               | Check what will be tracked    |
| 7    | `git add .`                                | Stage the files               |
| 8    | `git commit -m "Initial commit"`           | Create the new first commit   |
#### Replace GitHub History (Option 1)

| Step | Command                                                        | Purpose                                                   |
| ---- | -------------------------------------------------------------- | --------------------------------------------------------- |
| 1    | `git remote add origin git@github.com:USERNAME/REPOSITORY.git` | Connect your new Git repository to GitHub                 |
| 2    | `git remote -v`                                                | Verify the GitHub connection                              |
| 3    | `git push --force-with-lease -u origin main`                   | Replace GitHub's old `main` history with your new history |
#### GitHub — Delete Old & Create New (Option 2)
| Step | What to do                                                                            | Purpose                                          |
| ---- | ------------------------------------------------------------------------------------- | ------------------------------------------------ |
| 1    | Open your old repository on GitHub                                                    | Go to the repository                             |
| 2    | Settings → Danger Zone → Delete this repository                                       | Delete the old repository and its GitHub history |
| 3    | Create a new repository                                                               | Start with a clean GitHub repository             |
| 4    | Do NOT initialize it with README, `.gitignore`, or license                            | Keep the remote repository empty                 |
| 5    | Copy the new repository's SSH address                                                 | You'll use it to connect your local Git          |
| 6    | In your vault, run `git remote add origin git@github.com:USERNAME/NEW-REPOSITORY.git` | Connect local Git to the new GitHub repository   |
| 7    | Run `git remote -v`                                                                   | Verify the connection                            |
| 8    | Run `git push -u origin main`                                                         | Upload your fresh Git history                    |

___
## Git History & Recovery Cheat Sheet
| What you want to do | Command | What it does |
|---|---|---|
| See all commits | `git log --oneline` | Shows your Git history |
| See details of a commit | `git show COMMIT_ID` | Shows what changed in that commit |
| See an old version of one file | `git show COMMIT_ID:path/to/file.md` | Displays the exact old file |
| Save an old file separately | `git show COMMIT_ID:path/to/file.md > old-note.md` | Extracts the old file |
| Find commits affecting a file | `git log --all --oneline -- path/to/file.md` | Shows that file's history |
| Compare old vs current file | `git diff COMMIT_ID -- path/to/file.md` | Shows what changed |
| Restore an old version of one file | `git restore --source=COMMIT_ID -- path/to/file.md` | Replaces current file with old version |
| View entire old snapshot | `git switch --detach COMMIT_ID` | Changes your working folder to that snapshot |
| Safely copy entire old snapshot | `git worktree add ../old-vault COMMIT_ID` | Creates a separate folder containing the old snapshot |
| See current Git state | `git status` | Shows staged/unstaged changes |

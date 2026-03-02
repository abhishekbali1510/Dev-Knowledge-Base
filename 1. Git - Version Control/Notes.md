## 1️⃣ Git Architecture

### Core Idea

Git is a content-addressable distributed database.

Everything is stored inside: `.git/`

### Important directories

- `.git/objects/` → actual data (blobs, trees, commits)
- `.git/refs/heads/` → branch pointers
- `.git/HEAD` → current branch pointer
- `.git/index` → staging area
- `.git/logs/` → reflog
- `.git/refs/stash` → stash pointer

## 2️⃣ Git Object Model

### Blob

Represents file content

### Tree

- Represents directory
- Maps filenames → blob hashes

### Commit

Contains:
- Root tree hash
- Parent commit(s)
- Author / committer info
- Timestamp
- Commit message

⚠️ Commit hash depends on:
- Content
- Parent
- Metadata
- Tree hash
- If parent changes → hash changes


## 3️⃣ Branches

Branch = just a pointer.

### Example

`.git/refs/heads/feature` contains: `<commit-hash>`

### Key Points

- Branches do NOT store commits
- They only point to commits

## 4️⃣ HEAD

### Normal state

`HEAD → refs/heads/main → commit`

### Detached HEAD

`HEAD → commit directly`

### Key Points

- Detached commits are not deleted immediately
- They become unreachable and may be garbage-collected later

## 5️⃣ The Three Areas

- Working Directory
- Index (Staging Area)
- Repository (Commit Graph)

### Flow

`Working Dir → git add → Index → git commit → Commit object`

### Key Points

- Index is NOT part of history

## 6️⃣ Reset

Always moves branch pointer.

### --soft
- Move branch
- Keep index
- Keep working dir

### --mixed (default)
- Move branch
- Reset index
- Keep working dir

### --hard
- Move branch
- Reset index
- Reset working dir

## 7️⃣ Reflog

### Stored in

`.git/logs/`

### Tracks movement of

- HEAD
- Branch refs

### Allows recovery of

- Lost commits
- Hard reset mistakes
- Deleted branches

### Commits are recoverable until

- Reflog expires
- GC prunes unreachable objects

## 8️⃣ Merge

Creates a new commit when histories diverge.

### Merge commit
- Has 2 parents

### Fast-forward
- No new commit
- Just moves branch pointer
- Happens when current branch has no new commits.

### Key Points

- Merge preserves history shape

## 9️⃣ Rebase

### Rule

`git rebase X`: Take commits from current branch and replay them on top of X

### Rebase

- Rewrites commit history
- Changes parent
- Creates new commit hashes

### Rebase does NOT

- Replay X's commits
- Merge branches

### 🔥 Public Rebase Danger

Rebasing shared branch:
- Changes commit hashes
- Breaks distributed consistency
- Creates duplicate histories
- Forces teammates to reset
- Can cause data loss if mishandled

### Best Practice

- Rebase private branches
- Never rebase shared/public branches

## 🔟 Cherry-pick

Takes one specific commit and replays it.

### Use when

- Backporting fixes
- Moving a single change

### Key Points

- Creates new commit with new hash

## 11️⃣ Stash

Stash creates commit(s).

### Stored at

`.git/refs/stash`

### Key Points

- Not visible in normal `git log` because it's outside branch history

## 12️⃣ Bisect

Binary search over commit graph.

### Purpose

Finds bug-introducing commit.

### Complexity

Time complexity: $O(\log n)$

## 13️⃣ Garbage Collection

### What `git gc` does

- Packs objects
- Removes unreachable objects not referenced by any branch, tag, or reflog (after expiry).

### Key Points

- Unreachable ≠ immediately deleted

## 🎯 Core Mental Models

- **Merge** = "Bring them to me."
- **Rebase** = "Move me to them."
- **Branch** = pointer
- **Commit hash** = identity
- **Changing parent** = changing identity
---
description: >-
  Came across this amazing command which allows downloading a single from the
  remote git repo
---

# Download single file from Git

Many times, we just want a single file from the git repo, and dont want to clone the huge repo for it.

Below command takes advantage of **`git achieve`** command.  We first archive single file by providing ssh url and branch, then extract the archive with tar command and then stdOut to local machine.

```bash
git archive --remote=ssh://git@gitlab.com/user/mi-repo.git \
BranchName /path-to-file-git-repo/file_name \
| tar -xO /path-to-file-git-repo/file_name \
> /path-to-save-the-file/file_name
```


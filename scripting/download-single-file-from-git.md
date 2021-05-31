---
description: >-
  Came across this amazing command which allows downloading a single from the
  remote git repo
---

# Download single file from Git

```bash
git archive --remote=ssh://git@gitlab.com/user/mi-repo.git \
BranchName /path-to-file-git-repo/file_name \
| tar -xO /path-to-file-git-repo/file_name \
> /path-to-save-the-file/file_name
```


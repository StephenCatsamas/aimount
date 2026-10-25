# ai-mount

Share a project at `/srv/ai-workspaces/NAME`, with existing `.git` entries read-only.

Create the user and group once (skip if they already exist):

```bash
sudo groupadd ai-edit
sudo useradd -m -U -s /bin/bash -G ai-edit ai
```

Install and use:

```bash
sudo install -d -o root -g ai-edit -m 750 /srv/ai-workspaces
sudo install -o root -g root -m 755 ai-mount ai-unmount ai-shell /usr/local/sbin/

sudo ai-mount "$HOME/code/project" project
sudo ai-unmount project
```

For sibling worktrees, unmount affected shares, run `git worktree repair --relative-paths ../worktree` in the original main checkout, then mount both checkouts using their original directory names.

Open an AI login shell with `no_new_privs` enabled, then launch your agent from the shared workspace:

```bash
ai-shell
```

Exit that shell and stop its jobs before unmounting. Do not give `ai` sudo or privileged service access.
Before adding a repository, unmount every share that will contain it.
Add it through the original path, then run `ai-mount` again before restarting the AI session.

# Share a directory with the `ai` user (Ubuntu)

One directory, two users, **the same files**. `ai-mount` exposes any folder at `/srv/ai-workspaces/NAME`. If it contains an ordinary `.git/` directory, that directory is read-only **through the AI mount**. No cloning or synchronisation.

## 1. Install once (manually)

```bash
sudo apt install acl
sudo groupadd ai-edit
sudo useradd -m -s /bin/bash ai
sudo usermod -aG ai-edit ai
sudo usermod -aG ai-edit "$USER"
chmod 700 "$HOME"
sudo chmod 700 /home/ai
sudo install -d -o root -g ai-edit -m 750 /srv/ai-workspaces
sudo install -m 755 ai-mount ai-unmount /usr/local/sbin/
```

Skip `groupadd` / `useradd` if the group / account already exists. **Log out and back in** to activate your group membership. Making your home `700` prevents the AI from reaching the original path, but may affect other local users or services. Don't give `ai` sudo access.

## 2. Mount any directory

```bash
sudo ai-mount "$HOME/projects/website" website
```

The second argument is an optional workspace name; otherwise the directory's basename is used. The script changes **group ownership, group permissions and default ACLs recursively** in the original directory. These changes **remain after unmounting**. Only share specific directories you intend both accounts to edit—**not your entire home directory**.

## 3. Work in the AI account

```bash
sudo -iu ai
cd /srv/ai-workspaces/website
# Run your editor or AI coding agent here.
```

Changes appear immediately in your original directory. Files created by `ai` belong to `ai`, but default ACLs normally make them editable by both users. Explicitly restrictive file permissions or moving files in from elsewhere can require manual fixes.

For Git repositories, Git may require this **once per workspace**, run as `ai`:

```bash
git config --global --add safe.directory /srv/ai-workspaces/website
```

`.git/` is readable but not writable through the AI path; you stage and commit from your own account. This protection assumes `ai` cannot also reach the original path. A Git *worktree* with a `.git` file instead of a directory is not covered by the Git protection.

## 4. Unmount

Exit any shells or tools using the mounted directory, then from your account:

```bash
sudo ai-unmount website
```

Mounts are temporary; remount after a reboot. **Unmounting does not reverse permission or ACL changes.** This is directory sharing, not a complete sandbox: the AI can execute code as itself and change any files you share with it.

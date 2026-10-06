# Lesson 1: Linux Filesystem and Navigation

## Key idea
Linux has one tree starting at `/`. No drive letters. Devices and processes appear as files too.

## Important directories
| Path | Purpose |
|---|---|
| /home | User home directories (~ is a shortcut) |
| /etc | System configuration files |
| /var | Changing data: logs, web content, databases |
| /usr | Installed programs and libraries |
| /tmp | Temporary files |

## Commands I practiced
- Navigation: pwd, ls -la, cd, cd -, cd ~
- Files: mkdir -p, touch, cp, mv (also renames), rm -i
- Viewing: cat, less, head, tail
- Help: man, --help
- Disk and processes: df -h, du, free -h, ps aux

## What went wrong and what I learned
- Typed `lab/backups/~` by mistake, so cp created a file literally named `~`. Quotes stop the shell from expanding `~`.
- Pasted a placeholder (`<that-ppid>`) literally; `<` is redirection in the shell.
- `docker system prune` also removed old stopped containers I hadn't checked first. Run `docker ps -a` before pruning.
- Matching sizes don't prove files are identical. `sha256sum` does.

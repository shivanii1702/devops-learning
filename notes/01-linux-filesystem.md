# Lesson 1: Linux Basics, Filesystem and Navigation

> Practiced on Ubuntu 24.04 LTS (bare metal laptop). Everything here was typed and tested in a real terminal.

## 1. Terminal, shell and prompt

- **Terminal**: the window where you type commands.
- **Shell** (bash): the program that reads your command and runs it.
- **Prompt** example: `shivani@shivani1tiwari:~/devops-projects$`

| Part | Meaning |
|---|---|
| `shivani` | username |
| `shivani1tiwari` | hostname (machine name) |
| `~/devops-projects` | current directory (`~` = home) |
| `$` | normal user (root shows `#`) |

**Command anatomy:** `command  options  arguments`
Example: `ls -l ~/lab` -> command `ls`, option `-l`, argument `~/lab`.

**Useful system info commands**

```bash
lsb_release -a    # distro name and version
uname -r          # kernel version
whoami            # current user
hostname          # machine name
uptime            # time since boot + load average
df -h             # disk space (human readable)
free -h           # memory usage
```

## 2. The filesystem tree

Linux has ONE tree starting at `/` (the "root directory", not the root user). No drive letters.

| Path | Purpose |
|---|---|
| `/home` | user home directories (`~` = `/home/shivani`) |
| `/etc` | system configuration files |
| `/var` | changing data: logs (`/var/log`), databases, web content |
| `/usr` | installed programs and libraries |
| `/bin`, `/sbin` | essential commands |
| `/tmp` | temporary files |
| `/root` | the root user's home |
| `/dev`, `/proc`, `/sys` | devices and kernel/process info exposed as files |
| `/boot` | kernel and bootloader files |

**Everything is a file** in Linux, including devices and processes.

## 3. Paths

- **Absolute path**: starts with `/`, works from anywhere. `/home/shivani/lab`
- **Relative path**: starts from where you are. `lab/logs`

| Symbol | Meaning |
|---|---|
| `~` | home directory |
| `.` | current directory |
| `..` | parent directory (one level up) |
| `cd -` | previous directory |

`~` only expands when it stands alone at the start of a word. `lab/backups/~` is NOT home, it is a literal name.

## 4. Navigation

```bash
pwd            # print working directory
ls             # list files
ls -l          # long format: permissions, owner, size, date
ls -a          # include hidden files (names starting with .)
ls -la         # both
ls -lS         # sort by size, largest first
ls -lSr        # reverse the sort (smallest first)
ls -ltr        # oldest first, newest last (great for logs)
cd /var/log    # absolute path
cd ..          # up one level
cd ~           # home (plain `cd` does the same)
cd -           # back to previous directory
```

### Reading `ls -l`

```
-rw-rw-r-- 1 shivani shivani 458488529 Jun  4 23:22 video.mp4
drwxr-xr-x 2 shivani shivani      4096 Nov 17  2025 Documents
```

| Field | Meaning |
|---|---|
| first char | `-` file, `d` directory, `l` link |
| next 9 chars | permissions: owner / group / others (rwx each) |
| `1` | link count |
| `shivani shivani` | owner and group |
| `458488529` | size in bytes |
| date | last modified |

## 5. Create, copy, move, delete

```bash
mkdir notes                      # make a directory
mkdir -p lab/{logs,scripts,backups}   # nested, several at once (brace expansion)
touch hello.txt                  # create empty file / update timestamp
cp hello.txt copy.txt            # copy file
cp -r lab lab2                   # copy directory (-r = recursive)
mv copy.txt renamed.txt          # rename (rename = move to new name)
mv renamed.txt lab/              # move into a directory
rm hello.txt                     # delete file (NO recycle bin)
rm -r lab2                       # delete directory and contents
rm -i file                       # ask before deleting
```

**Brace expansion:** `lab/{a,b,c}` becomes `lab/a lab/b lab/c` before the command runs.

**Safety rules for `rm`**
1. No undo. Read the path twice.
2. Use `ls` on the same path first.
3. Prefer `rm -i` when unsure.
4. Never run `rm -rf` with a path you did not verify.
5. Quote names with spaces: `rm "lv_0 (1).mp4"`.

## 6. Viewing files

```bash
cat file          # print whole file
less file         # scroll (arrows, /word to search, n next match, q quit)
head -n 3 file    # first 3 lines
tail -n 3 file    # last 3 lines
tail -f file      # follow a file live (Ctrl+C to stop)
```

## 7. Getting help

```bash
man ls            # full manual (q quit, /word search)
ls --help         # short summary
type cd           # builtin or program?
```

## 8. Hidden files

Names starting with `.` are hidden. Examples seen in my home folder:

| File | Purpose |
|---|---|
| `.bashrc` | shell settings, runs at each new terminal |
| `.bash_history` | log of typed commands |
| `.ssh/` | SSH keys (permissions `drwx------`) |
| `.gitconfig` | Git name and email |
| `.config`, `.cache`, `.local` | app settings and data |

## 9. `/etc/passwd` format

```
shivani:x:1000:1000:Shivani:/home/shivani:/bin/bash
```

| # | Field |
|---|---|
| 1 | username |
| 2 | password placeholder (`x`, hashes in `/etc/shadow`) |
| 3 | UID |
| 4 | primary GID |
| 5 | description |
| 6 | home directory |
| 7 | login shell |

- UID 0 = root. UIDs below 1000 = system accounts. Regular users start at 1000.
- Shell `nologin` or `/bin/false` = account cannot log in (least privilege for services).
- On my machine only `root` and `shivani` have `/bin/bash`; about 39 accounts use `nologin`.

```bash
cut -d: -f1,7 /etc/passwd       # username and shell
grep -c 'nologin' /etc/passwd   # count non-login accounts
grep '/bin/bash' /etc/passwd | cut -d: -f1   # who can log in
```

## 10. Processes and memory (first look)

```bash
ps aux --sort=-%mem | head -n 6          # top memory users
ps -eo pid,%mem,rss,comm --sort=-%mem | head
```

| Column | Meaning |
|---|---|
| PID | process ID |
| %MEM | share of RAM |
| VSZ | virtual size (ignore) |
| RSS | real RAM in KB |
| STAT | S sleeping, R running, l multithreaded |

- Summing RSS over-counts shared memory. Use PSS (`/proc/PID/smaps_rollup`) or the `available` column of `free -h`.
- Chrome renderer processes were the biggest RAM users.

## 11. Disk cleanup workflow I used

```bash
df -h /                                   # overall usage
du -h --max-depth=1 ~ | sort -rh | head   # biggest folders
ls -lhS ~/Downloads | head                # biggest files
sha256sum a b                             # identical content? same hash
rm -i duplicate                           # delete with confirmation
sudo apt clean
sudo journalctl --vacuum-time=7d
snap list --all | awk '/disabled/{print $1, $3}'   # old snap revisions
```

Result: 48 GB used -> 30 GB used (about 18 GB freed).

**Key idea:** `du | sort -rh | head` = pipe pattern (output of one command feeds the next).

## 12. Docker lesson learned

- Containers are disposable; **volumes** hold data. Deleting containers kept my 2 volumes.
- `kind delete cluster --name <name>` removes a kind cluster cleanly.
- `docker system prune` also deletes ALL stopped containers. Run `docker ps -a` first.

## 13. Mistakes and what they taught me

| Mistake | Lesson |
|---|---|
| `cp ... lab/backups/~` created a file named `~` | One stray character changes meaning. Quote odd names: `mv 'lab/backups/~' ...` |
| Pasted `<that-ppid>` literally | `<` `>` are redirection in the shell. Replace placeholders with real values. |
| `grep ... file` -> "No such file" | `file` was a placeholder, not a real filename |
| `docker system prune` removed old containers | Check what will be affected before cleanup commands |
| Ran `git config` with "Your Name" | Placeholders must be replaced before running |
| `rm -i` with Enter only | Only answers starting with `y` delete |

## 14. Habits

- Tab completes names. Up arrow recalls commands. `Ctrl+C` stops a command. `Ctrl+L` clears screen.
- Silence usually means success.
- Read errors: they tell you what is wrong.
- Take snapshots before risky changes (VMs).

## 15. Exercises completed

1. Created `~/lab/{logs,scripts,backups}` with one `mkdir -p` command.
2. Created `hello.txt`, copied to backups, renamed to `hello.bak`.
3. Found hidden files with `ls -la ~`.
4. `head`/`tail` on `/etc/passwd`; `ls -S` sorts by size, `-r` reverses.
5. Deleted `~/lab` with `rm -r` and recreated it.

## Self-check questions

1. Create `~/practice/a/b` in one command? -> `mkdir -p ~/practice/a/b`
2. `cd ..` vs `cd -`? -> `..` goes to parent, `-` goes to previous directory.
3. Rename `old.txt` to `new.txt`? -> `mv old.txt new.txt`
4. What does `ls -la` add? -> hidden files and details (permissions, owner, size, date).
5. Delete a folder and contents? -> `rm -r folder`; risky because there is no undo.

# Linux Command Line Cheatsheet

> Practical, copy‑paste‑ready commands for modern Linux. Defaults assume **Debian/Ubuntu**; cross‑distro alternatives are noted. Icons: 🔒=requires sudo/root, 💣=danger/irreversible.

---

## Conventions

- Prompt markers: `$` = normal user, `#` = root. Use `sudo` to escalate.
- Placeholders: `<like-this>` (replace, don’t include the `<>`).
- Get help: `man <cmd>` or `<cmd> --help`.
- Safer deletes: prefer `trash-cli` or `-i` prompts; be careful with `rm -rf` 💣.

---

## 0) New machine quick bootstrap (Ubuntu/Debian)

```bash
# refresh & base toolchain
sudo apt update && sudo apt -y upgrade
sudo apt install -y build-essential curl wget git vim nano htop tmux tree unzip zip \
  ripgrep jq silversearcher-ag fd-find bat fzf net-tools dnsutils traceroute \
  ca-certificates gnupg lsb-release software-properties-common

# On Ubuntu the fd/bat binaries are named differently; add convenient aliases:
command -v fdfind >/dev/null && sudo ln -sf $(command -v fdfind) /usr/local/bin/fd
command -v batcat >/dev/null && sudo ln -sf $(command -v batcat) /usr/local/bin/bat
```

**RHEL/Fedora**: `sudo dnf install ...`  •  **Arch**: `sudo pacman -S ...`

---

## 1) Navigation & Files

```bash
pwd                         # print working directory
ls -lah                     # list (long, all, human sizes)
cd -                        # jump back to previous dir
mkdir -p <path>/to/dir      # create nested directories
cp -ai <src> <dst>          # copy (a=archive, i=prompt before overwrite)
mv -i <src> <dst>           # move/rename (prompt before overwrite)
rm -ri <path>               # interactive delete (r=recursive) 💣
ln -s <target> <link>       # create symlink
stat <file>                 # detailed metadata & timestamps
file <path>                 # detect file type
```

---

## 2) Find & Search

```bash
grep -RIn "<text>" .                  # search text recursively with line numbers
rg -n "<text>" .                      # (ripgrep) faster recursive search
find . -type f -name "*.log"          # find files by name
find . -type f -mtime +7 -delete       # delete files older than 7 days 💣
find . -size +500M -print0 | xargs -0 ls -lh   # list files >500MB
locate <name>                          # query mlocate DB (run `updatedb` 🔒 first)
```

---

## 3) Inspect & Text Processing

```bash
less <file>                  # page through a file (q to quit, / to search)
head -n 50 <file>            # first 50 lines
tail -n 100 -f <file>        # last 100 lines and follow
wc -l <file>                 # line count
sort | uniq -c | sort -nr    # frequency count of lines
cut -d, -f2-3 <file.csv>     # select CSV fields 2..3
awk -F, '{sum+=$3} END {print sum}' file.csv   # simple column sum
sed -n '1,120p' <file>       # print lines 1..120
jq '.items[] | {id, name}' file.json   # JSON query
```

---

## 4) Permissions & Ownership

```bash
chmod u+rwx,g+rx,o-r <path>      # symbolic mode
chmod 640 <file>                 # octal mode (rw- r-- ---)
chown <user>:<group> <path> 🔒   # change owner/group
umask                            # show default permissions mask
getfacl <file>; setfacl -m u:<user>:rw <file>  # ACLs (install: acl)
```

---

## 5) Processes, Jobs & Monitoring

```bash
ps aux | grep <name>                 # list processes
pgrep -fl <name>                     # find PIDs by name
kill -15 <pid>; kill -9 <pid>        # terminate (SIGTERM then SIGKILL) 💣
htop                                 # interactive monitor (install: htop)
nohup <cmd> & disown                 # run command immune to hangups
jobs; fg %1; bg %1                   # manage shell jobs
nice -n 10 <cmd>; renice +10 -p <pid>
strace -p <pid> 🔒                   # trace syscalls (debugging)
lsof -p <pid> 🔒                     # open files for a PID
```

---

## 6) Networking

```bash
ip a                                 # IP addresses
ip r                                 # routing table
ss -tulpen                           # listening sockets (tcp/udp)
ping -c 4 <host>                     # reachability
traceroute <host>                    # route path (install: traceroute)
dig +short <name>                    # DNS lookup (package: dnsutils / bind-utils)
curl -I https://example.com          # HTTP HEAD
wget -qO- https://example.com        # fetch to stdout
nc -vz <host> <port>                 # test TCP connectivity (netcat)
scp <file> user@host:/path/          # copy over SSH
rsync -avzP <src> <user>@<host>:/dst # efficient sync
```

**Firewall**

```bash
sudo ufw status                      # Ubuntu/Debian (UFW)
sudo ufw allow 22/tcp                # open SSH
# RHEL/Fedora: firewall-cmd --add-service=ssh --permanent && firewall-cmd --reload
```

---

## 7) System, Services & Logs

```bash
uname -a                             # kernel & arch
lsb_release -a || cat /etc/os-release# distribution info
uptime; who                          # system uptime, logged-in users
df -h; free -h                       # disk & memory
lscpu; lsblk                         # CPU & block devices
dmesg | tail -n 50                   # kernel ring buffer
journalctl -xe                       # recent errors (systemd)
journalctl -u <service>.service      # logs for one unit
systemctl status <service>           # service status
sudo systemctl restart <service> 🔒  # restart a service
sudo systemctl enable <service> 🔒   # start on boot
```

Older systems (SysVinit): `service <name> status` / `/etc/init.d/<name> start`

---

## 8) Users & Groups

```bash
id; groups <user>                    # identity & group membership
sudo adduser <user> 🔒               # Debian/Ubuntu helper (or: useradd)
sudo usermod -aG <group> <user> 🔒   # add user to group
sudo passwd <user> 🔒                # set/change password
sudo visudo 🔒                       # edit sudoers safely
```

---

## 9) Packages

**Debian/Ubuntu (apt)**

```bash
sudo apt update && sudo apt -y upgrade
apt search <pkg>; apt show <pkg>
sudo apt install -y <pkg1> <pkg2>
sudo apt remove <pkg>; sudo apt purge <pkg>
sudo apt autoremove --purge
```

**RHEL/Fedora (dnf/yum)**

```bash
sudo dnf search <pkg>; sudo dnf install <pkg>; sudo dnf remove <pkg>
```

**Arch (pacman)**

```bash
sudo pacman -Syu; sudo pacman -S <pkg>; sudo pacman -Rns <pkg>
```

---

## 10) Disks, Filesystems & Space

```bash
df -h                               # mounted filesystem usage
du -sh *                            # size of items in current dir
du -shx . | sort -h                 # summarize current FS only, human sort
lsblk -f                            # devices, filesystems & UUIDs
mount | column -t                   # current mounts
sudo mount <dev> <mnt> 🔒; sudo umount <mnt> 🔒
sudo nano /etc/fstab 🔒             # persistent mounts (be careful)
```

Find space hogs:

```bash
sudo du -xhd1 / | sort -h           # top-level usage on root FS
sudo du -xhd1 /var | sort -h        # check logs, caches, etc.
```

---

## 11) Archives & Compression

```bash
# tar
 tar -czf <name>.tar.gz <dir>       # create gz archive
 tar -xzf <name>.tar.gz             # extract gz archive
 tar -cJf <name>.tar.xz <dir>       # xz (smaller, slower)

# zip
 zip -r <name>.zip <dir>
 unzip <name>.zip
```

---

## 12) Scheduling

```bash
crontab -e                           # edit user crontab
crontab -l                           # list entries
# Example: run at 02:30 daily
30 2 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1

# systemd timers (preferred for new services)
systemctl list-timers --all
```

---

## 13) Shell, Env & Redirection

```bash
export PATH="$HOME/bin:$PATH"       # prepend to PATH
alias ll='ls -alF'                   # quick alias
set -euo pipefail                    # safer bash options in scripts

# pipes & redirection
cmd1 | cmd2                          # pipe
cmd > out.txt                        # stdout to file (overwrite)
cmd >> out.txt                       # append
cmd 2> err.log                       # stderr to file
cmd > all.log 2>&1                   # merge stdout+stderr

# here-doc
cat <<'EOF' > script.sh
#!/usr/bin/env bash
set -euo pipefail
EOF
chmod +x script.sh

# xargs examples
printf '%s\n' *.log | xargs -I{} gzip {}
```

---

## 14) SSH & Keys

```bash
ssh <user>@<host>                    # SSH login
ssh -i ~/.ssh/id_ed25519 <user>@<host>
ssh-keygen -t ed25519 -C "<email>"  # generate key
ssh-copy-id <user>@<host>            # copy key (OpenSSH server required)

# ~/.ssh/config (simplify hosts)
Host mybox
  HostName 203.0.113.10
  User ubuntu
  IdentityFile ~/.ssh/id_ed25519
```

---

## 15) Containers (Docker)

```bash
sudo systemctl status docker 🔒
docker ps -a; docker images; docker volume ls

# run & exec
docker run --rm -it ubuntu:24.04 bash
docker exec -it <container> bash

# logs & cleanup
docker logs -f <container>
docker system df; docker system prune -af  # remove unused 💣

# build & compose
docker build -t <name>:<tag> .
docker compose up -d; docker compose logs -f; docker compose down
```

_Podman_ is a rootless alternative: `podman run ...`

---

## 16) Quick troubleshooting playbook

1. **What changed?** `last -x | head`, `journalctl -xe`, `/var/log/`.
2. **Is it up?** `systemctl status <svc>`, `ps aux | grep <svc>`.
3. **Is it listening?** `ss -tulpen | grep <port>`.
4. **Can I reach it?** `ping`, `curl -v`, `traceroute`.
5. **Is it disk/memory?** `df -h`, `free -h`, `dmesg | tail`.
6. **Permissions?** `ls -l`, `id`, `namei -l <path>`.

---

## References

- `man` pages (authoritative), `tldr <cmd>` (install: `tldr`)
- Bash manual: `help` builtin, `man bash`
- systemd: `man systemctl`, `man journalctl`

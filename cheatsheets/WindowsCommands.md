# Windows Command Line (CMD) & PowerShell Cheatsheet

> **Heads-up:** This file is about **Windows**, not Linux. Consider renaming it to `WindowsCommands.md` or splitting Windows and Linux into separate files (e.g., `WindowsCommands.md` and `LinuxCommands.md`).

**Official docs:** <https://learn.microsoft.com/windows-server/administration/windows-commands/windows-commands>

---

## Conventions

- `CMD` examples use `%VARS%` and backslashes. PowerShell (PS) uses `$env:VARS` and forward slashes are fine.
- Commands marked with `🔒` typically require **elevated/Administrator** shell.
- Add `/?` to most commands for inline help.

---

## System & Power

- **Computer name**: `hostname`
- **Windows version**: `ver`, `systeminfo`
- **Restart now**: `shutdown /r /t 0`
- **Shutdown now**: `shutdown /s /t 0`
- **Cancel pending shutdown**: `shutdown /a`

---

## Users & Sessions

- **Current user**: `whoami` or `echo %USERNAME%`
- **List logged-on users (RDS/Terminal Services)**: `query user`
- **List sessions**: `query session`
- **Log off (by ID or name)**: `logoff <SESSION_ID>` or `logoff <SESSION_NAME>`
- **Local users**: `net user`
- **Local groups**: `net localgroup`
- **Run as different user**: `runas /user:DOMAIN\User cmd`

---

## Navigation & Filesystem

- **Change directory**: `cd` or `chdir`
- **List directory**: `dir`
- **Tree view**: `tree /f`
- **Print file**: `type <file>` | page through: `more <file>`
- **Search text**: `findstr /i /n "pattern" <file>`
- **Where is a program**: `where <name>` (PowerShell: `Get-Command <name> | Select-Object -Expand Source`)

---

## Files & Directories

- **Make/Delete directory**: `mkdir <dir>`, `rmdir /s /q <dir>`
- **Copy files**: `copy <src> <dst>`
- **Robust copy**: `robocopy <src> <dst> /e` (mirror: `/mir`) 🔒
- **Legacy recursive copy**: `xcopy <src> <dst> /e /h /i`
- **Move/Rename**: `move <src> <dst>`, `ren <old> <new>`
- **Delete files**: `del /f /s /q <pattern>`
- **Attributes**: `attrib +/-r +/-h +/-s <file>`
- **Symbolic links**: `mklink <link> <target>` (file) | `mklink /d <link> <target>` (dir) 🔒

---

## Processes

- **List processes**: `tasklist`
- **List with details**: `tasklist /v`
- **Console-session only**: `tasklist /fi "SESSIONNAME eq Console"`
- **Kill by name**: `taskkill /f /im <process>.exe`
- **Kill by PID**: `taskkill /f /pid <PID>`

> **PowerShell equivalents:** `Get-Process`, `Stop-Process -Id <PID> -Force`

---

## Services

- **List running services**: `net start`
- **Query service**: `sc query <service>` or `sc queryex type= service state= all`
- **Start/Stop**: `sc start <service>`, `sc stop <service>` 🔒

> **PowerShell:** `Get-Service <name>`, `Start-Service <name>`, `Stop-Service <name>`

---

## Networking & DNS

- **Reachability (ICMP)**: `ping <host>`
- **Trace route**: `tracert <host>`
- **Path + loss/latency**: `pathping <host>`
- **IP configuration**: `ipconfig /all`
- **Renew DHCP**: `ipconfig /release` then `ipconfig /renew` 🔒
- **DNS cache**: `ipconfig /displaydns`, `ipconfig /flushdns` 🔒
- **TCP/UDP connections**: `netstat -ano` (add `-b` for binaries 🔒)
- **ARP table**: `arp -a`
- **Routing table**: `route print`
- **Add route**: `route add <network> mask <mask> <gateway> metric 1` 🔒
- **DNS lookup**: `nslookup <name>` (interactive: just `nslookup`)
- **Firewall (quick check)**: `netsh advfirewall show allprofiles` 🔒

> **PowerShell:** `Test-Connection <host>`, `Resolve-DnsName <name>`, `Get-NetIPConfiguration`, `Get-NetTCPConnection`

---

## Active Directory (domain-joined machines)
>
> Requires **RSAT** tools or admin station. Availability varies by Windows edition.

- **Find domain controller**: `nltest /dsgetdc:<domain>`
- **List domain controllers**: `netdom query dc`
- **Query users (legacy tools)**: `dsquery user -name "john*"`
- **Group membership**: `whoami /groups` (quick), or `dsget user <DN> -memberof`
- **Group Policy result**: `gpresult /r`

> **PowerShell (RSAT ActiveDirectory module):** `Get-ADUser -Identity <samAccountName> -Properties *`

---

## Utilities & Diagnostics

- **Compare files**: `fc <file1> <file2>`
- **Hashes**: `certutil -hashfile <file> SHA256`
- **Scheduled tasks**: `schtasks /query /fo LIST /v`
- **Registry (read)**: `reg query HKLM\Software\...`
- **HTTP requests**: `curl https://example.com` (included on Win10+)
- **Environment vars**: `set` (list), `set NAME=value` (temp for session)

---

## PowerShell Basics (quick map)

- **Current user**: `$env:USERNAME`
- **List dir**: `Get-ChildItem` (`gci`, `ls`)
- **Change dir**: `Set-Location` (`cd`)
- **Read file**: `Get-Content` (`gc`)
- **Search text**: `Select-String -Pattern "foo" -Path .\*.log` (`sls`)
- **Copy/Move/Delete**: `Copy-Item` (`cp`), `Move-Item` (`mv`), `Remove-Item -Recurse -Force` (`rm`)
- **Processes/Services**: `Get-Process` (`gps`), `Get-Service` (`gsv`)

---

## Safety Notes

- Many networking, service, and filesystem operations require **Administrator** privileges. If a command fails with `Access is denied`, reopen the shell as Admin.
- On corporate devices, security tooling and policy (AppLocker, SRP, Defender) may block some commands.

---

## References

- Windows commands index (Microsoft Learn)
- PowerShell docs: <https://learn.microsoft.com/powershell/>
- PowerShell Gallery: <https://www.powershellgallery.com/>
- PowerShell Cheat Sheet: <https://learn.microsoft.com/powershell/scripting/learn/ps101/cheat-sheet>
- PowerShell Scripting Guide: <https://learn.microsoft.com/powershell/scripting/learn/ps101/ps101-scripting-guide>
- PowerShell Best Practices: <https://learn.microsoft.com/powershell/scripting/learn/deep-dives/best-practices>

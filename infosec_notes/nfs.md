# Network File System (NFS)

## Skirmish

- default port: 2049 TCP or UDP
- to transfer files between computers running different Operating Systems
- uses RPC protocol to communicate between client and server
- allows system to share directories and files with other over network, so users and programs can access files on the remote systems
- how does it work?
  - client will request to mount directory from a remote host on a local directory
  - mount service will then act to connect to the relevant mount deaemon using RPC
  - server checks if user has permission to mount requested directory
    - NFS uses user id and group id for controlling user permissions
  - will then return file handle which uniquely identifies each file and directory on the server

## Mounting a NFS drive

- client’s system needs a directory where all content shared by host server in the export folder can be accessed
- you can create this folder anywhere on your system.
- once this mount point is created, you can use the `mount` command to connect the NFS share to the mount point on your machine like so: `sudo mount -t nfs IP:share /tmp/mount/ -nolock`
- Flags explained:
  | Tag | Function |
  | --- | -------- |
  | `mount` | execute mount command |
  | `-t nfs` | type of device to mount (here: `nfs`) |
  | `IP:share` | IP adress of NFS server and name of share we wish to mount |
  | `-nolock` | don't use NLM locking |

## Enumerating

- to use nfs we need some tools:
  - `nfs-common` (`sudo apt install nfs-common`)
    - gives us a couple of new commands: `lockd`, `statd`, `showmount`, `nfsstat`, `gssd`, `idmapd`, `mount.nfs`
- using `showmount` to list NFS shares and mount the enumerated share:

  ```bash
    # Create mount point
    mkdir /tmp/mymount
    # Enumerate Shares
    /usr/sbin/showmount -e <IP>
    # Mount using created mount point and enumerated share name
    sudo mount -t nfs <IP>:<enumerated_share_name> /tmp/mount/ -nolock
    # Use mounted share like any other directory in local filesystem
    cd /tmp/mymount
  ```

## Exploiting

- if you have low privilege shell on machine and you found NFS share on this machine you might be able to use that to escalate privileges
- Root-Squashing:
  - by default enabled on NFS shares
  - prevents anyone connecting to the share from having root access to the NFS volume
  - remote root users are assigned a user "nfsnobody" when connected --> has the least local privileges
  - if root squashing is turned off, it can allow the creation of SUID bit files allowing a remote user root access to the connected system
- Exploit Step-By-Step:
  `NFS Access ->
        Gain Low Privilege Shell ->
            Upload Bash Executable to the NFS share ->
                Set SUID Permissions Through NFS Due To Misconfigured Root Squash ->
                    Login through SSH ->
                        Execute SUID Bit Bash Executable ->
                            ROOT ACCESS`

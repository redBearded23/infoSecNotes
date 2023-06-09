# John The Ripper

- hash cracking tool with a broad range of compatible hash types
- if you have hashed version of a password and you know the hashing algorithm you can use a large number of words (dictionary) and compare their hashes to the one you're trying to crack --> dictionary attack
- John is a tool to allow you to conduct fast brute force attacks on a large array of different hash types
- there are different distributions of John --> most popular is "Jumbo John" (preinstalled on Kali)

## Usage

- Basic Syntax:

  `john <options> <path to file>`

  - `<pathtofile>` = file containing the hash you're trying to crack

- Automatic Cracking = let John find out the algorithm to crack (may be error-proned)

  ```bash
    john --wordlist=<pathtowordlist> <pathtofile>
    # Example:
    john --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
  ```

- Format specific cracking: if we know the hash algorithm, we can tell John to use it while cracking the provided hash

  ```bash
  john --format=<format> --wordlist=<pathtowordlist> <pathtofilewithash>
  # Example:
  john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
  ```

  - to list all possible John formats:

    `john --list=formats`

    - grep specific type:

      `john --list=formats | grep -iF "md5"`

## Identifying Hashes

- other tools to identify hashes, if John isn't able for himself
  - online hash identifiers for example: <https://hashes.com/en/tools/hash_identifier>
  - in Kali: hash-identifier --> `hash-identifier <hash>` or with piping: `cat hash.txt | hash-identifier`
  - hash-id: this can also be downloaded and used as python script:
    - `wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py`
    - `python3 hash-id.py`
  - a more modern and powerful alternative to hash-identifer is a tool called haiti: `https://github.com/noraj/haiti`

## Cracking Windows Hashes

- authentication hashes are the hashed versions of passwords
- sometimes it is possible to crack them using brute force
- **NTHash/NTLM**
  - format that modern Windows OSs store user and service passwords in
  - also references the previous version of Windows format for hashing passwords, which was called LM
- NTHash/NTLM-Hashes can be obtained by
  - **dumping the SAM database** on a Windows machine with tools like Mimikatz
  - from the **Active Directory Database: NTDS.dit**
- Cracking with John:
  - hash algorithm for Windows NTLM hash = `NT`
  - crack:
    - `john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt`

## Cracking Hashes from `/etc/shadow`

- linux passwords are stored in `/etc/shadow`
  - one entry per line for each user/account on system
- First unshadow the file together with `/etc/passwd`:
  - `unshadow <pathtopasswdfile> <pathtoshadowfile>`
  - Example: `unshadow /etc/passwd /etc/shadow > unshadowed.txt`
- then feed output from unshadow directly into John:

  ```bash
  john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt
  #Example
  john --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt unshadow.txt
  ```

## Single Crack Mode

- in this mode John uses only information provided in the username to try and work out possible passwords heuristically by slightly changing letters and numbers contained within the username --> this is called `Word Mangling`
- therefore different **mangling rules**:
- if cracking hashes in single crack mode we need to change file format that we're feeding for John to understand the data to create a wordlist from

  - we do this by prepending the hash with the username that hash belongs to:

  ```bash
    # Before
    1efee03cdcb96d90ad48ccc7b8666033
    # After
    mike:1efee03cdcb96d90ad48ccc7b8666033
  ```

- using Single Crack Mode:

  ```bash
    # Basic Syntax
    john --single --format=[format] [pathtofile]
    # Example:
    john --single --format=raw-sha256 hashes.txt
  ```

## Crack encrypted files using John

- if we don't have the password or key to decrypt a file
- `gpg2john`: binary that allows to convert gpg file into hash string that john the ripper can understand
- Example:
  ```bash
    # Create hash for John
    gpg2john secret.txt.gpg > hash.txt
    # crack the hash
    john --wordlist=[location/name of wordlist] --format=gpg [name of hash we just created]
    john --wordlist=rockyou.txt --format=gpg hash.txt
  ```

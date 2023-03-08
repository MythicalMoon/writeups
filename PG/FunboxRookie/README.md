## Recon

`nmap -sC -sV -vv $IP -oA nmap/initial`

Initial 1000 port scan reveals ports:
21 Allows anonymous logins
22 ssh
80 http, Apache base

# port 21 FTP
Login as anonymous:anonymous

we're faced with several zip files and .@admins .@users and a welcome.msg

Let's get all the files, check the files that we can for clues and work from there.

Messages indicated that zips contain rd_rsa's however they're password protected. Not a problem, let's use zip2john `zip2john {name.zip} > {name.hash}` and then try to crack the passwords using john and see what we get. `john --wordlist=/usr/share/wordlist/rockyou.txt {name.hash}` Repeat for every file or run a script to automate.

# port 22 SSH
We get the password for cathrine and tom. change bit mode to 600 for both after extraction and try to ssh with either. using the `-i` tag as we're supplying an id_rsa file.

Cathrine's connection closed.
Tom's got through. Local flag secured!

# Priv Escalation

Keep in mind we don't have the password for tom, we we can't check sudo for example. Let's look around and see what we find.

`ls -al` lists all files in the directory and one of which is a mysql_history, which could house something interesting. After a look through, we can see Tom's credentials being inserted into the database 'support'

# root

using `sudo -l` using the acquired password we discover we have ALL : ALL. so now we can just `sudo -i` to gain root. And submit the root flag!
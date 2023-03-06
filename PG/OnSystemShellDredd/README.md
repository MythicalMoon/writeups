Quite straight forward box using basic techniques.

I like to set an environmental variable for the IP for these types of boxes so that I don't have to go back and copy paste the IP every time.
`IP=192.xxx.xxx.xxx`

Like usual we run an inital nmap scan with
`nmap -sV -sC -vv -oA namp/initial $IP`
as well as a full one.
`nmap -sV -sC -vv -p- -oA nmap/full $IP`

Initial scan returned that only port 21 is open, which is a good starting point, -sC tag also informed us that login as anonymous is allowed. So let's do just that.

```bash
ftp $IP
anonymous
anonymous
```

Here we find a '.hannah' directory by listing all files. some might be tempted to try `get .hannah'` but remember to chech if it's a directory or not. Since it is infact a directory we change directory and find an id_rsa file. Now we can `get` that.

First things to do after getting an id_rsa file ir change it's mode bits. `chmod 600 id_rsa` 400 is also fine, but it has to be 400 or 600 or you won't be able to ssh into anything.

we have an id_rsa file for presumably Hannah, but our initial scan didn't list any ssh ports. Let's check our full scan now.

Full scan reveals that there is in fact a ssh port open on port 61000. Great let's try to connect using the rsa we obtained.

`ssh hannah@$IP -i id_rsa -p 61000`

Success. user flag is in our home directory as per usual. Now let's try to find a way to escalate our privileges.

after searching for files with SUID bit set we find a few, however cpulimit seems out of place. Time to check GTFOBins. cpulimit can be used to escape restricted environments however since we're not in one we just want to escalate privileges and since it has a SUID bit set we can do just that with the syntax `cpulimit -l 100 -f -- /bin/sh -p` . which spawns a shell with preserved root privileges. 

Now we just go to root's directory to get the flag.
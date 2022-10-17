# Recon

## nmap

- 21 ftp
- 22 ssh
- 80 webserver (Apache)

![](Screenshots/2022-10-16-19-53-40.png)

## gobuster

![](Screenshots/2022-10-16-20-41-43.png)

## main

![](Screenshots/2022-10-16-19-53-06.png)

## cute comment on main page

![](Screenshots/2022-10-16-19-54-35.png)

## /nothing

![](Screenshots/2022-10-16-19-56-22.png)

![](Screenshots/2022-10-16-19-56-57.png)

![](Screenshots/2022-10-16-19-57-13.png)

> highlight everything just in case, but nothing

![](Screenshots/2022-10-16-19-57-31.png)

## robots.txt

> New directory

![](Screenshots/2022-10-16-19-58-36.png)

## hidden_text

![](Screenshots/2022-10-16-19-59-14.png)

- secret.dict

![](Screenshots/2022-10-16-19-59-32.png)

## gobuster with this dict

![](Screenshots/2022-10-16-20-01-45.png)

## /pwned.vuln

![](Screenshots/2022-10-16-20-02-43.png)

- source

![](Screenshots/2022-10-16-20-03-03.png)

## creds for FTP

    ftpuser:B0ss_Pr!ncesS

# ftp

![](Screenshots/2022-10-16-20-04-46.png)

## files - ftp

> We get both files from the FTP server for further examination

![](Screenshots/2022-10-16-20-05-36.png)

> id_rsa for ariana presumably, so we change it for ssh use

    chmod 600 id_rsa

## note.txt

> That points towards the user "ariana"

![](Screenshots/2022-10-16-20-07-07.png)

# SSH

> Note indicates ariana and an id_rsa, so it's safe to assume we can try and SSH in with ariana

![](Screenshots/2022-10-16-20-07-59.png)

# User ariana:

## flag

![](Screenshots/2022-10-16-20-08-48.png)

## SUDO Enum

![](Screenshots/2022-10-16-20-10-06.png)

## all users: + messenger

![](Screenshots/2022-10-16-20-11-33.png)

## messenger.sh

> We can execute messeneger as selena

![](Screenshots/2022-10-16-20-13-36.png)

> From the code we can tell that we could execute code as selena, so if we run `bash -i` to run a bash interface, we should be golden.
>> The code doesn't actually send anything to any user, however it does execute the msg as a command and redirects errors into `/dev/null`

![](Screenshots/2022-10-16-20-14-04.png)

> to run a sudo command as another user we need to add the -u flag and the user.

    sudo -u selena /home/messenger.sh

![](Screenshots/2022-10-16-20-33-32.png)

> run a few basic enumerations to find something further, but I found nothing, so I had to examine groups assigned to selena. only one that's actually worth checking out is "docker"

## gtfobins

> we check docker's gtfobins and see a way to get a root shell.


![](Screenshots/2022-10-16-20-40-43.png)

    docker run -v /:/mnt --rm -it alpine chroot /mnt sh

![](Screenshots/2022-10-16-20-39-19.png)

> flag proof for root.

![](Screenshots/2022-10-16-20-54-10.png)


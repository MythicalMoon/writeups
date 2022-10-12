## nmap :

    nmap -sC -sV -vv 192.168.154.82

![](Screenshots/2022-10-10-08-52-32.png)

## gobuster port 80: - Nothing

## gobuster port 8088:

    gobuster dir -u http://192.168.154.82 -w /usr/share/wordlists/seclist/discovery/web-content/2.3-med.txt

![](Screenshots/2022-10-10-08-53-10.png)

## 8088:/docs - exposed old litespeed

![](Screenshots/2022-10-10-08-54-22.png)

## port 21:
tried login with anonymous - No luck

![](Screenshots/2022-10-10-08-54-41.png)

## searchsploit for litespeed 1.7: - no non-authentic exploits


![](Screenshots/2022-10-10-08-55-49.png)

## SSH port 22 Attack:
Before trying random usernames and password, lets assume the username is the theme of the website : geisha

    hydra -l geisha -P /usr/share/wordlists/rockyou.txt 192.168.154.82 ssh

![](Screenshots/2022-10-10-08-57-17.png)

## we're in: flag-proof:

![](Screenshots/2022-10-10-08-58-19.png)

    find / -perm /4000 2>/dev/null

![](Screenshots/2022-10-10-09-02-25.png)

## SUID priv esc of base32:

![](Screenshots/2022-10-10-09-03-01.png)

## /etc/shaodow

![](Screenshots/2022-10-10-09-09-47.png)

## cheating for flag:

![](Screenshots/2022-10-10-09-10-43.png)

## discovery of root's id_rsa:

![](Screenshots/2022-10-10-09-13-22.png)

## id_rsa 

![](Screenshots/2022-10-10-09-12-52.png)

## flag:

![](Screenshots/2022-10-10-09-13-55.png)

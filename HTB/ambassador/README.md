# Ambassador

# Nmap

![](Screenshots/2022-10-28-10-38-45.png)

### We have abit to explore right off the bat:
  * 22 - SSH
  * 80 - HTTP
  * 3000 - Another HTTP
  * 3306 - MySQL

# Noteworthy information
> Potentially "developer" is a user
>> Noted, but not very useful as we don't know the password.

![](Screenshots/2022-10-28-10-44-30.png)

# Gobuster

![](Screenshots/2022-10-28-10-45-11.png)

> After going through the directories, nothing of interest was found. We did download the sole image located in /images in case stegonography reveals something.

# Port 3000 - Grafana

![](Screenshots/2022-10-28-10-50-38.png)

> Innitial thought was to capture request and see if we can inject anything, Didn't seem like anything was going through after a light test, after running through sqlmap extensive attempt nothing was injectable as well.

> WE do have Grafana's version number, let's check that

![](Screenshots/2022-10-28-10-55-01.png)

![](Screenshots/2022-10-28-10-56-16.png)

> We run the script, and confirm we can read files.

![](Screenshots/2022-10-28-11-00-30.png)

> I'm not too familiar with Grafana so I researched it's documentation to find this:

![](Screenshots/2022-10-28-11-45-55.png)

> grafana.ini contains a password that will help us pivot

![](Screenshots/2022-10-28-11-04-53.png)

> With these credentials we can login grafana

![](Screenshots/2022-10-28-11-26-38.png)

> database name and user

![](Screenshots/2022-10-28-11-29-21.png)

> https://github.com/jas502n/Grafana-CVE-2021-43798

![](Screenshots/2022-10-28-11-39-01.png)

> if it's binary we can just curl it and output it and then preview with sqllite database browser

    curl --path-as-is http://10.10.11.183:3000/public/plugins/alertlist/../../../../../../../../var/lib/grafana/grafana.db -o grafana.db

> Exploring the database the table "data_source" contains the password for grafana user

![](Screenshots/2022-10-28-11-44-39.png)

# Port 3306

> Login with the Credentials

![](Screenshots/2022-10-28-11-54-53.png)

## SSH creds

![](Screenshots/2022-10-28-11-56-22.png)

> After a base64 decode we can login through SSH as developer

# Developer && PrivEsc

![](Screenshots/2022-10-28-11-59-50.png)

> User Flag:

![](Screenshots/2022-10-28-12-01-17.png)

> Directory of interest from .gitconfig

![](Screenshots/2022-10-28-12-06-08.png)

![](Screenshots/2022-10-28-12-07-33.png)

> This on it's own didn't mean much to me so I did some googling and consulted searchsploit yet again

![](Screenshots/2022-10-28-12-16-17.png)

## msfconsole - consul

    For this we will need to port forward our connection

    ssh developer@$target -L 8500:127.0.0.1:8500

![](Screenshots/2022-10-28-12-25-24.png)

![](Screenshots/2022-10-28-12-25-42.png)
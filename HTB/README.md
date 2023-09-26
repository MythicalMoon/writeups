# PC

ports 22 and 50051
50051 being gRPC

![](Screenshots/2023-08-29-17-02-55.png)

after installing and launching gRPCUI

https://github.com/fullstorydev/grpcui

`./grpcui -plaintext $IP:50051`

![](Screenshots/2023-08-29-17-02-16.png)

![](Screenshots/2023-08-29-17-03-27.png)

Trying default params we find `admin:admin` logs us in:

![](Screenshots/2023-08-29-17-04-46.png)

Now that we have a token, we can try and request info, However we got an interesting error, since that's the case we can attempt SQLi

![](Screenshots/2023-08-29-17-06-29.png)

save the request from BURP to a file and run an sqlmap with the request, by itendifying `id` as the parameter. `sqlmap -r http.req --risk=2 --level=2 -v 3 --tables -p id`

![](Screenshots/2023-08-29-17-10-24.png)

Dumping accounts provides us with credentials for user `sau`

![](Screenshots/2023-08-29-17-13-47.png)

`ssh sau@$IP`

![](Screenshots/2023-08-29-17-17-34.png)

manually enumerating we find some service running on port 8000:

![](Screenshots/2023-08-29-17-18-40.png)

So we'll use port tunneling through ssh to view what's on port 8000. `ssh sau@<IP> -L 8000:127.0.0.1:8000`

![](Screenshots/2023-08-29-17-20-30.png)

Checking what's on there in our browser of choice. It's a pyLoad, not too familiar what that is, but ...

![](Screenshots/2023-08-29-17-21-05.png)

`searchsploit pyload` finds a pre-auth RCE

![](Screenshots/2023-08-29-17-22-12.png)

`searchsploit -m 51532.py` copies the exploit to the current directory

![](Screenshots/2023-08-29-18-28-58.png)

Pretty straight forward usage. The script URL encodes `" "` for us but nothing else, so we we'll need to URL encode that ourselves.
```bash
"/" - "%2F"
"+" - "%2b"
```
You'll need to URL encode all special characters for which ever command you'll be using to escalate, I like just using `cp /bin/bash /tmp/moon/rootbash` and then `chmod u+s /tmp/moon/rootbash` . As we're on a public server make your own temporary location for your files to not disrupt other's experience. If we'd just add an SUID bit to /bin/bash someone might find that and use it before finding PyLoad therefore skipping the PrivEsc part of the CTF.

![](Screenshots/2023-08-29-17-58-07.png)

![](Screenshots/2023-08-29-17-58-32.png)
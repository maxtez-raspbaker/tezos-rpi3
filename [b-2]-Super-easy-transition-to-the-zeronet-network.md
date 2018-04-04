
This is an update to run a node with a precompiled Tezos for the zeronet network with RPI3.

If you had already a lot of fun with the alphanet following the recipe in the previous page[b-1], jump here(*) (next paragraph).

***

If you have a blank SD card wondering what to do, start following few steps from the previous page[b-1].

PLEASE NOTE
if you are planning to run the node only on the zeronet, stop following the old instructions after the command “./resize.sh” and jump to the next paragraph (*).

Or

if you are planning to run the node on the alphanet and also the zeronet then go one step further and stop after the command “./tz-block.sh”, then follow the procedure here below.

***

(*) For anyone.
Stop the node if it is running. If the RPI3 is off, just boot it then download the zip file tz-zero032618.zip
by typing on the terminal from the default location /home/tzuser:

_GOOD TO KNOW: please check the [e] Updates page for the latest zip file and the new script tz-switch.sh replacing those in this page_

`wget https://www.dropbox.com/s/xytmq80rs4a5akd/tz-zero032618.zip`

Next, download the script “tz-switch.sh”:

<!-- `wget https://www.dropbox.com/s/zrhdxfym2ryflaq/tz-switch.sh` -->
`wget -NO /home/tzuser/tz-switch.sh https://www.dropbox.com/s/3le4ccoelervw96/tz-switch032618.sh`

and change the access rules:

`chmod 764 tz-switch.sh`

Run the script by typing:

`./tz-switch.sh`

it will extract the zip file content and do few more things (the zip file will be automatically deleted after installation).
Now regardless whether you had previously ran the node on the alphanet or not, type:

`./tz-init.sh`

a new identity for the node is created with this command since you are on a different network. As with the alphanet, the above command is needed only once.

Ready to surf on the zeronet? just type:

`./tz-start.sh`

and the magic will happen. That’s it!

If you want to switch from the zeronet to the alphanet or vice versa, just stop the node with the script “tz-stop.sh” then type “tz-switch.sh” and you are ready to start playing with the other network with “./tz-start.sh”

In summary, the commands to start/stop the node on the zeronet are the same as with the alphanet. If at some point you decide to change network, stop the node, run the script “tz-switch.sh” and start the node again, now it will be on the alternative network.

***

A little background info (optional reading).
The script “tz-switch.sh” preserves any previous info for both networks (identity, blockchain, etc). For example going from the alphanet to the zeronet, it will simply move everything from the default folders into 3 temporary folders-alpha (/tezos-alpha, /.tezos-node-alpha and./tezos-client-alpha) and move the zeronet stuff from the temporary folders-zero (/tezos-zero, /.tezos-node-zero and./tezos-client-zero) into the default folders.

The script “tz-start.sh” is slightly different for the two networks therefore it is preserved in the files “tz-start-alpha.sh” and “tz-start-zero.sh”. No need to worry, the script “tz-switch.sh” will copy for you the right file version in “tz-start.sh”.
For practical purposes the script “tz-start.sh” is all that is needed to run the node either on the alphanet or the zeronet. The file “config.json” is also slightly different for the alphanet and zeronet network. The two versions are stored in their temporary folders.

Have a look at the new tezos-client front-end, it is much nicer and easier to use with the zeronet (great job of the Tezos Development team!). General manual: /home/tzuser/tezos/tezos-client man

The zeronet network is still quite volatile, and I decided to put a limit on the allocated CPU resources for the tezos-node program using a small utility called cpulimit. At the moment the CPU limit for the tezos-node program is set to 80% of the total RPI3 CPU resources. It is more a precaution than a real need,
from what I have seen it rarely happens to go beyond ~50%. You can monitor the situation typing “ps aux” and in case, change the limit by editing the file tz-start.sh.

In future I am also planning to introduce a small script attached to the crontab scheduler to monitor the RAM usage and eventually, if needed, restart the node before the end of the regular cycle (3 days).

Since Tezos for the zeronet is constantly evolving, I will try to post new precompiled versions for RPI3 possibly every ~week. It is fairly easy to do, once I have the new Tezos compiled, something that I will do for myself anyway, it is only a matter of zipping the Tezos folder and upload it. Then you can just download the new zip file and follow the same procedure above here.



The first step is to get a wallet from the faucet [Tezos faucet](https://faucet.tzalpha.net/) using another computer
because F28 minimal with X11 x-windows does not include a web-browser and wget cannot bypass the recaptcha.
The wallet file (xxxx.json) needs to be transferred to the RPI3.
Since the ssh service is disabled, there are two easy alternatives.

Option I) use a USB flash drive. To access the external drive, as a root/superuser type:\
`fdisk -l`\
this command will show the device location of the USB drive, usually it is _/dev/sda_ or _/dev/sda1_. If the location is for example _dev/sda_ then type:\
`mount /dev/sda /media/hd`\
then \
`cd /media/hd`\
make sure the file _xxxx.json_ is listed using the command `ls`, then copy the file on the RPI3, for example in the folder /home/tzuser/.tezos-client:\
`cp xxxx.json /home/tzuser/.tezos-client`\
now unmount the drive:\
`umount /dev/sda`\
and remove it from the RPI3.

Option II) upload the wallet file xxxx.json in your [dropbox account](https://www.dropbox.com/), create a shared link, and use wget to download the file on the RPI3.

***

Once the xxxx.json has been transferred on the RPI3, an account needs to be activated. With the RPI3 node synchronized and running, in a text-shell type:\
`tezos-client activate account <my_account> with /home/tzuser/.tezos-client/xxx.json`\
where <[]()my_account[]()> is the name you have chosen for the account. Wait for the operation to be completed and for more info on this page.....

(some references: [doc.tzalpha.net](http://doc.tzalpha.net/introduction/zeronet.html), [Fred Yankowski instructions](https://github.com/tezoscommunity/FAQ/wiki/Build-a-zeronet-node-on-Debian-9)






<!--- 
[e] About RPI3 on the betanet and mainnet network
At the moment (04/05/18) it is unclear whether the Tezos Foundation will provide instructions or support for RPI3 on the betanet and mainnet.

Regardless, as a token holder my plan is to go forward baking with 3 RPI3. 

Detailed instructions on how to compile the Tezos program for the betanet and mainnet will be made available on these wiki pages.
About sharing a pre-compiled version, it would be probably wise to seek first some kind of endorsement or official approval from the Tezos Foundation. 
I will try to contact the TF privately, as soon as I figure out how to do it. 

--->

Links to updates files (e.g. new precompiled tezos for the zeronet/alphanet/betanet/mainet network) will be posted here.

***
****05/18/2018****: Back to the alphanet (last chance to play with unreal XTZ). From `./home/tzuser` as a user, download the precompiled Tezos for the alphanet here:

`wget https://www.dropbox.com/s/6hy1f0w51dcbfkr/tz-alpha051818.zip`

and also this script:

`wget -NO /home/tzuser/tz-switch.sh https://www.dropbox.com/s/efvptwfujynqf79/tz-switch051818.sh`

To install the new Tezos program, run the script `./tz-switch.sh`, and your RPI3 is ready to go, as usual, just type `./tz-start.sh`. 

Please note, if you haven't installed the new Fedora 28 yet, that is the image in the previous post (RPI3-TEZOS-050618.img), please do it before downloading the two files from this update.
Painful but necessary, just follow the steps here below and after creating the new identity, please download the two files from this post. Then run the script `./tz-switch.sh` and your RPI3 is ready to go.

***
****05/09/2018****: MAJOR UPDATE. The new image file (RPI3-TEZOS-050618.img) of Fedora 28 + precompiled Tezos (zeronet) can be downloaded from here:

`https://drive.google.com/file/d/1fWNuEXEPgrUI3EQzHVZSV-BK5_QZ-V7s`

That's all is needed.\
Some info. The file is rather large, ~7GB, sorry I could not make it smaller. Good news, no more mammoth image files for a while, F28 will be the Fedora version supporting Tezos on the alphanet, betanet, mainnet until May 2019.

Requirement: 16GB SD card, RPI3 or RPI3+ with the usual accessories (power supply, monitor, keyboard, mouse, fans etc)

Installation: 
* Same as with the previous image file (instructions in [b-1]). Use etcher (https://etcher.io/) to copy the image on the SD card. 
* Boot the RPI3 (or RPI3+). It is a good day, no significant changes in the setup compared to the previous version/image (F26).
* Use the command `./expand.sh` to expand the OS over the entire SD card space (absolutely necessary).
* Type the command `./tz-init.sh` to create a new identity for the RPI3 Tezos node.
* To run the node type `./tz-start.sh`. To stop it `./tz-stop.sh`. To shutdown the RPI3 `./tz-sleep.sh` (automatic restart of the node is set every 3 days).

Sometimes in the next few days I will provide some info on how to get a wallet from the faucet and activate the wallet (zeronet network).

P.S. \
Password for user _tzuser_ is: _tzuser_\
Password for root/superuser is: _tothemoon_

(no need to say that they should be changed)

***
****04/18/2018****: Blockchain restarted on the zeronet. The new updated version of Tezos is here:

`wget https://www.dropbox.com/s/kw72wtb0vq92xl6/tz-zero041818.zip`

and 

`wget -NO /home/tzuser/tz-switch.sh https://www.dropbox.com/s/ey9fgl3n1a2k7p6/tz-switch041818.sh`

Before starting the node please delete all the content in "./.tezos-node/store" and "./.tezos-node/context" (e.g. `rm * /home/tzuser/.tezos-node/store`).

***
****04/13/2018****: there have been some changes in the zeronet network, please use the new pre-compiled Tezos: 

`wget https://www.dropbox.com/s/11s52oxxeip6h9u/tz-zero041318.zip`

and 

`wget -NO /home/tzuser/tz-switch.sh https://www.dropbox.com/s/66mrpkbkfoo1zin/tz-switch041318.sh`

The replacement follows the same procedure described earlier (here below and page [b-2]).

(Technical detail for the manual compilation of Tezos on the zeronet, a new library is needed: `opam install conf-libev` which will also recompile various other packages (e.g. lwt))

***
****04/04/2018****: here is a new precompiled Tezos for the zeronet. Not much different from the old one, this is more like a test of the update process. First stop the node if it is running then in /home/tzuser just type:

`wget https://www.dropbox.com/s/k9hp0vygufk987j/tz-zero040318.zip`


A new tz-switch.sh script is required:

`wget -NO /home/tzuser/tz-switch.sh https://www.dropbox.com/s/wyb5qihpj5vbpd7/tz-switch040318.sh`

By running the script (change the permission `chmod 764 tz-switch.sh` and type `./tz-switch.sh`) it will put the new tezos in its folder (/home/tzuser/tezos). It will also
create a new tz-start.sh script which does not use the utility cpulimit anymore and it will copy a new config.json file 
in /home/tzuser/.tezos-node (low RAM usage). The identity file identity.json (if exists) and the content of previous runs stored in /home/tzuser/.tezos-node/store and /home/tzuser/.tezos-node/context are preserved. If you have been on the zeronet before, just type as usual `./tz-start.sh`. If this is the first time, then type `tz-init.sh` and then `./tz-start.sh`.

***

****03/31/2018****: this is the script to start tezos-node and tezos-client in boostrapped mode (zeronet)

`wget -NO /home/tzuser/tz-start.sh https://www.dropbox.com/s/ny5t14ytwwhgow0/tz-start-alpha.sh`

****03/31/2018****: this is a revised config.json file that should keep the RAM usage to an acceptable level (zeronet).

`wget -NO /home/tzuser/.tezos-node/config.json https://www.dropbox.com/s/yfyowtlp6s4aotu/config-rev01.json`
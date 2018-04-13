
Links to updates files (e.g. new precompiled tezos for the zeronet network) will be posted here.

***
****04/13/2018****: there have been some changes in the zeronet network, please use the new pre-compiled Tezos: 

`wget https://www.dropbox.com/s/11s52oxxeip6h9u/tz-zero041318.zip`

and 

`wget -NO /home/tzuser/tz-switch.sh https://www.dropbox.com/s/66mrpkbkfoo1zin/tz-switch041318.sh`

The replacement follows the same procedure described earlier (here below and page [b-2])

Technical detail, a new library is needed to compile Tezos on the zeronet `opam install conf-libev` which will
also recompile various other packages (e.g. lwt).

***
****04/04/2018****: here is a new precompiled Tezos for the zeronet. Not much different from the old one, this is more like a test of the update process. First stop the node if it is running then in /home/tzuser just type:

`wget https://www.dropbox.com/s/k9hp0vygufk987j/tz-zero040318.zip`


A new tz-switch.sh script is required:

`wget -NO /home/tzuser/tz-switch.sh https://www.dropbox.com/s/wyb5qihpj5vbpd7/tz-switch040318.sh`

By running the script (change the permission `chmod 764 tz-switch.sh` and type `./tz-switch.sh`) it will put the new tezos in its folder (./home/tzuser/tezos). It will also
create a new tz-start.sh script which does not use the utility cpulimit anymore and it will copy a new config.json file 
in /home/tzuser/.tezos-node (low RAM usage). The identity file identity.json (if exists) and the content of previous runs stored in /home/tzuser/.tezos-node/store and /home/tzuser/.tezos-node/context are preserved. If you have been on the zeronet before, just type as usual `./tz-start.sh`. If this is the first time, then type `tz-init.sh` and then `./tz-start.sh`.

***

****03/31/2018****: this is the script to start tezos-node and tezos-client in boostrapped mode (zeronet)

`wget -NO /home/tzuser/tz-start.sh https://www.dropbox.com/s/ny5t14ytwwhgow0/tz-start-alpha.sh`

****03/31/2018****: this is a revised config.json file that should keep the RAM usage to an acceptable level (zeronet).

`wget -NO /home/tzuser/.tezos-node/config.json https://www.dropbox.com/s/yfyowtlp6s4aotu/config-rev01.json`
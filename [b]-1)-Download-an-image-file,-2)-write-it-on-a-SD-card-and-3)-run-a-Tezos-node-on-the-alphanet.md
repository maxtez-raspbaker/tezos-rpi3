
Here is a very easy option to run your own Tezos node on the alphanet almost effortlessly.

I made an image file containing Linux OS (Fedora) + precompiled Tezos.
The image file (RPI3-TEZOS-02282018.img) is ~4GB and can be downloaded from here:
https://drive.google.com/file/d/18uPhwxTQEebZZFP2nJmt1mmgqBdUOgiF

Requirement:

    Raspberry Pi 3 Model B
    Micro SD card (at least 16GB)
    USB keyboard & mouse
    monitor with HDMI port
    LAN internet (no wireless), static or dynamic IP address
    optional but recommended (cooling fan and heatsinks)
    optional small UPS (<700 VA) for the router and RPI3. The tiny machine doesn’t consume more that 5 W
    (there are also nice DIY projects to build your own UPS with Li-ion batteries)
    wish list a cool case with the Tezos logo

Write the image on a SD card using etcher (https://etcher.io) or a similar tool.
Put the SD card on your RPI3, power it up, on the screen there will be a minimal graphic interface with one text shell.
After the first boot, type 3 commands that will initialize the system, then 1 command will start the node.
For any subsequent boot, just 1 command and your Tezos node will start working.

The following 3 commands should be typed only one time after the first boot of the RPI3.
In the text shell type the first command (mandatory):

`./resize.sh`

this is absolutely mandatory. It will inflate the OS to take the entire space on the SD card

Next command (highly recommended):

`./tz-block.sh`

thanks to Fred Yankowski a new Tezos node does not need to sync starting from time zero but part of the blockchain up to ~Feb/10/2018 can be downloaded (more info here https://fred.yankowski.com/2017/10/04/build-tezos-on-debian-9). The above command will download the blockchain and extract the files.
This operation requires ~6GB of space (down to ~3GB after it is done) and may take a while depending on your LAN internet speed.

The third command (mandatory):

`./tz-init.sh`

will initialize the new node and will give it a unique identification fingerprint on the network.

The above 3 commands won’t be needed anymore after the first boot.

To start running the node simply type:

`./tz-start.sh`

This will open 2 text shells, one for the screen output of the tezos-node program and the second one for the output of the tezos-client.

That’s it. You have a functioning Tezos node.

After few mins you’ll see on one screen the node will start syncing and on the other screen a script like this:
“no nonce to reveal for block …2d8”

To stop the Tezos node just type:

`./tz-stop.sh`

and to shutdown the RPI3:

`./tz-sleep.sh`

Additional commands, for example to create an identity or to transfer “virtual” tezzies from an account
should follow the syntax described in the Tezos documentation (http://doc.tzalpha.net).

What is the fun without an update? While the node is running, in the text shell please type:
wget -N https://www.dropbox.com/s/eienap1sw6conzw/tz-start.sh
with the original script sometimes the tezos-client program fails to start, this update should reduce the risk.
and also:
wget -N -P /home/tzuser/.tezos-node https://www.dropbox.com/s/stu7gjjx7rsqyt0/config.json
a slightly revised version of config.json

A little bit more info on the setup. The Linux-OS includes a small number of services with a minimal graphical interface (X11). ssh, wifi, bluetooth are disabled, no mail, desktop or web-applications. It should be pretty secure, even though no login is required on boot. Beware that someone doesn’t physically steal your RPI3!
The default username of the only user is “tzuser”, password “tzuser”.
The administrator/root/superuser password is “mrgevers” (there is no excuse not to change it)

At the moment it is a good idea to restart the node after few days. The two scripts to do that (tz-stop.sh and tz-start.sh) are scheduled to run automatically using crontab every 3 days at 1:00AM (Paris time or the time on the clock if you change the time zone). In practice one should start the node manually with the command ./tz-start.sh and then let the automatic procedure take over, that is stop and restart the node. Note that 3 days means that the node will restart on the 3rd, 6th, 9th… of every month. If the RPI3 is powered on and the node is not running on these dates, it will start automatically.
I encourage anyone to explore the system by opening additional text shells, in particular have a look at the
files that I modified, config.json located in /home/tzuser/.tezos-node and the kernel configuration file sysctl.config located in the folder /etc.
It is probably wise to update the OS from time to time, type:
sudo dnf upgrade (followed by the root password)
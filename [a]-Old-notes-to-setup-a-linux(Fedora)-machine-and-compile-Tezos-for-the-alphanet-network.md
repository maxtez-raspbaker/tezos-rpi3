
Please Note: the notes provide only a general outline since some of the specifics are outdated. 

Completely revised notes will be released at some point in future.
***

maxtez-raspbaker on 12-18-17
### **Step by step setup of a Raspberry Pi 3 with Fedora and Tezos**

https://bold.io/step-by-step-setup-of-a-raspberry-2017-12-18

first release: 18/Dec/2017
revised(r1): xx/xxx/xx

The following instructions will prepare a Raspberry PI 3 Model B to run a Tezos node. The main features are:

    no desktop environment like Gnome or KDE, just basic X windows (X11) to allow for opening multiple terminals on the screen.
    only ssh server will be available to access the machine remotely through a non-standard port, firewall and SELinux enabled.
    setup of the DNS service and a domain for the Raspberry PI when the ISP assigns only a dynamic IP address for internet access.
    install OCaml with OPAM, compile and install the Tezos node without Docker.

I try to provide detailed instructions (mainly on "how" rather than "why" of the various commands) assuming minimal knowledge of the subjects. Time to complete the project may vary depending on experience. Links to additional info are posted along the way. For further details and/or for better understanding of the various commands, web-browsing of the internet is the best option.
There is only one specific requirement before starting, that is to have some familiarity with a command-line based text editor for linux (some editors here). My editor of choice is Vi mainly because it is preinstalled in all linux distributions (quick tutorial). 
I assume all this is in hand:

    Raspberry Pi 3 Model B+
    usb mouse and keyboard
    monitor with HDMI port 
    Raspberry Pi Power Supply 5V 3A (Micro USB) 
    LAN internet connection to a internet modem/router
    Micro SD card (some options here)
    The one I have is a Samsung MB-MC32GA/EU 32GB EVO Plus Micro SD UHS-I Card (speed up to 95MB/s).

Time required: 
Optimistic estimate: a day (skipping the Fedora setup, considering only the time to build up a Tezos node may take few hours/half a day if everything goes more or less smoothly).
Realistic estimate: several days (with some breaks, and a lot of coffee)

—————————————————————————————————

F-1) Download fedora ARM servers minimal instructions here
Write an image on the SD card using Etcher instructions here

Install the SD card in the Raspberry Pi and power on. During the first boot the following steps should be executed:
1. setup the password for the root (also called superuser or administrator) 
2. create at least one user that will have access to the Raspberry PI
3. setup time zone

NOTE: unless explicitly mentioned, all the following commands, steps F-1) through F-15) should be typed as root (superuser). 
The first login should be as a user then to login as a root (superuser, administrator) type:

$ su

followed by the root password. 
(NOTE: I do not particularly like the use of the sudo  command)

—————————————————————————————————

F-2) Enlarge the root partition

To expand Fedora over the entire available space of the SD card, type as a root:

$ df -h

$ growpart /dev/mmcblk0 4

$ resize2fs /dev/mmcblk0p4

$ df -h

—————————————————————————————————

F-3) List of active services (link1, link2)

To get an overview of what is running on Fedora, type:

$ systemctl list-units --type=service

—————————————————————————————————

F-4) Define the local name of the machine

Type:

$ hostnamectl set-hostname <name-of-the-machine>

$ hostnamectl status

where <name-of-the-machine> is the name you have chosen for the Raspberry PI.

—————————————————————————————————

F-5) Installation and minimal setup of X11 (X windows) (link1, link2, link3)

This will provide the environment to open multiple terminal shells. First download and install some packages:

$ dnf groupinstall base-x

$ dnf install twm

$ dnf install xorg-x11-apps

then change the directory:

$ cd /etc/x11

To list the content of the folder type:

$ ls

or alternatively: 

$ ls -l

With the editor of choice open the file Xresources and at the end of the file add the following text to configure a bit the terminal shells in X windows (NOTE: to use the vi editor just type vi Xresources):

xterm*vt100*Geometry: 120x40+0+0
xterm*Background: black
xterm*Foreground: white
xterm*Savelines: 2000

To start X windows type as a user (not root):

$ startx

(NOTE: to copy and paste text in X windows: copy-> highlight the text holding the left button of the mouse, paste-> press the central button of the mouse, text will be passed at the location of the cursor).

—————————————————————————————————

F-6) Install few more packages (link1, link2, link3)

Type:

$ dnf install nmap

$ dnf install policycoreutils-python-utils

check the status of the network and disable the wifi connection (for security reason):

$ nmcli general status

$ nmcli device status

$ nmcli radio wifi off

to look for open ports:

$ nmap -sT -O localhost

—————————————————————————————————

F-7) Get familiar with the firewall and change default access port number of the SSH server (22) to a different number (for example 19899) (link1, link2)

For an overview:

$ firewall-cmd --list-all-zones

or type this to list the ports that are open in the zone called public:

$ firewall-cmd --zone=public --list-ports

To add a port to a specific zone (in this case port 19899 to the zone named public):

$ firewall-cmd --permanent --zone=public --add-port=19899/tcp

If needed, type the following to remove a port:

$ firewall-cmd --permanent --zone=public --remove-port=19899/tcp

Copy the file ssh.xml in the folder /etc/firewalld/services by typing:

$ cp /usr/lib/firewalld/services/ssh.xml  /etc/firewalld/services

change directory:

$ cd /etc/firewalld/services

open the file ssh.xml with the editor and replace in <port protocol="tcp" port="22"/> the port number 22 with 19899, then restart the service:

$ systemctl restart firewalld

—————————————————————————————————

F-8) Add the new port for ssh to SELinux

To add the new port:

$ semanage port -a -t ssh_port_t -p tcp 19899

If the port is already defined by default, replace -a with -m and type instead:

$ semanage port -m -t ssh_port_t -p tcp 19899

If needed, to delete the port:

semanage port -d -t ssh_port_t -p tcp 19899

and to check the status:

semanage port -l |grep ssh_port_t

or:

sestatus

—————————————————————————————————

F-9) Changes in the configuration file of the service enabling the SSH server

In /etc/ssh use the editor to open the file sshd_config. The following lines should be present in the file:

port 19899
passwordAuthentication yes 
(NOTE: set no for passwordAuthentication after authorized_keys is copied in /home/max/.ssh, see later)
permitrootlogin no
permitemptypassword no
AllowUsers <name-user>
(NOTE: <name-user> is the name of the user that was created during the first installation) 
pubkeyauthentication yes
challengeresponseauthentication no

Restart the service:

systemctl restart sshd

—————————————————————————————————

F-10) Basic general instructions to open ports on the modem and router

In my case I have a DSL model connected to a router that provides access to internet for all my devices, computers, smartphone, etc. The following instructions may change for different models of DSL modems and routers. Only the general procedure is outlined here.

DSL modem
Access the DSL modem (usually with a web browser typing 192.168.1.1 or something like that, see the manual of the modem). The modem defines a private IP for the Raspberry PI or the router. The private IP address assigned by the modem to my router is 192.168.1.2. Find the option port forwarding and setup the internal/external port to 19899 (protocol TCP) for the specific IP address associated to the router or the Raspberry PI (if it is directly connected to the DSL modem).

When there is also a router
On the web-browser type the IP address to access the web interface of the device. This is not the private IP assigned by the modem (in the above case, 192.168.1.2). See the manual of the router. In my case the router internal IP address is 10.0.0.1

In the router a static internal IP address should be set for the Raspberry PI, for example something like 10.0.0.5.  If no router is present the same should be done instead in the modem. Find the port forwarding option and for the machine with IP 10.0.0.5 open the public/private port 19899.

—————————————————————————————————

F-11) Setup a static hostname when the Raspberry PI is connected to the internet via a dynamic IP address

Various DNS name services are available (usually for free). I have registered a domain with https://www.dynu.com/,
something like <mydomain>.dynu.net, where <mydomain> is the chosen name for the domain. In this way the Raspberry Pi can be accessed using an ssh client from anywhere. To make it working download and install the service ddclient:

dnf install ddclient 

then change directory:

cd /etc

and edit the file ddclient.conf to include the following instructions (more info here):

daemon=3600                                                                                        # Check every hour
syslog=yes                                                                                        # Log update msgs to syslog
mail=root                                                                                              # Mail all msgs to root
mail-failure=root                                                              # Mail failed update msgs to root
pid=/var/run/ddclient.pid                                                              # Record PID in file
use=web, web=checkip.dynu.com/, web-skip='IP Address' # Get ip from server
server=api.dynu.com                                                                          # IP update server
protocol=dyndns2
login=myusername            # the username to access your account at DYNU.NET
password=YOUR-PASSWORD                                   # Password to access DYNU.NET
<MYDOMAIN>.DYNU.NET

(NOTE: different DNS services may require some tuning of the instructions above here)
To make sure that ddclient will be updated regularly, type:

crontab -e 

then add the line:
45 04 * * * /usr/sbin/ddclient  --force

with this instruction an update will be performed daily at 04:45. 
Start and enable the service on boot by typing:

systemctl start ddclient

systemctl enable ddclient

to check how it is working:

ddclient -daemon -debug -verbose -noquiet

—————————————————————————————————

F-12) Configure the hosts file

The hosts file that is stored in the folder /etc should have at least the following lines:
127.0.0.1                    <localhost>.<localdomain> localhost
<IP>                                      <local-name>.<hostname> <local-name>

where:
<IP> -> is the ip address assigned or defined by the router.
<localname> -> is the name given to the Raspberry PI with the command hostnamectl at the beginning of the installation.
<hostname> -> is the name registered with the DNS service (e.g. if the domain is <mydomain>.dynu.net  then <hostname> is equal to <mydomain>).

—————————————————————————————————

F-13) Access remotely the Raspberry PI using a ssh client

For windows OS computers, for example use the program Putty or Bitwise ssh client (some info here).

Using the SSH client program, the input for the server host should be:
<mydomain>.dynu.net
and for the port:
19899

At the input request for the username provides the name of the user that was defined during the initial installation of Fedora on the Raspberry PI and then input the relative password.
(NOTE: the computer used to access the Raspberry PI server should not be on the same internet line of the server, basically it should have access internet from a different IP address)

—————————————————————————————————

F-14) Access the Raspberry PI using Key-Based authentication instead of password-based login

A RSA key pair should be created using Putty or Bitwise ssh client on your windows machine (not the Raspberry PI server). For example with Bitwise ssh client click on client key manager then generate new. Once it is created click on export to export the public key that will be named authorized_keys.

Use the sftp option of the ssh client program to transfer the file authorized_keys in the folder /home/<user>/.ssh.
(NOTE: <user> is the name of user that has access to the Raspberry PI)
(NOTE2: if the folder /home/<user>/.ssh does not exist it must be created by typing on the Raspberry PI machine as a user (not administrator/root): mkdir /home/<user>/.ssh)

Change some permission rules by typing:

chmod 0700 /home/<user>/.ssh

chmod 0600 /home/<user>/.ssh/authorized_keys

Finally modify the sshd_config file in /etc/ssh by replacing:

PasswordAuthentication yes
with:
PasswordAuthentication no

Now to access remotely the Raspberry PI no password will be require if the login is made from the same machine where the RSA key pair was generated.

—————————————————————————————————

F-15) Little extra protection against possible intrusions

Download and install fail2ban:

dnf install fail2ban

copy the file jail.conf in jail.local:

cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

If needed, the configuration file jail.local can be modified. With new updates of fail2ban, the local file won't be deleted. The default configuration seems ok to me and so far I made no changes.
Finally enable the service on boot:

systemctl enable fail2ban

—————————————————————————————————

F-16) Final remarks

Keep the Fedora packages updated regularly:

dnf upgrade

To turn off the Raspberry PI, type as a superuser:

halt

And to reboot the Raspberry PI:

shutdown -r now


—————————————————————————————————
—————————————————————————————————
Installation of Tezos and related stuff

—————————————————————————————————
—————————————————————————————————
Installation of a Tezos node connected to the alphanet should be simpler using Docker. 
Unfortunately I could not make it working. Docker CE is recommended with Tezos (see for example here), however Docker CE is not available for Fedora Arm platform (some info). I am not even sure if anyone was able to run Docker CE + Tezos on a Raspberry PI with a different linux distribution. The only solution at the moment is to compile Tezos from scratch using OCaml.

To be honest, I knew nothing about OCaml when I started this project and I know very little know, just enough to  clear the installation process and get through few hiccups along the way. I have applied the following instructions to two Raspberry PI and different issues came up. Anyway in both cases the Tezos node was compiled and installed successfully. Just please be aware that your installation may not necessarily comply exactly with the same steps outlined here, but eventually will work!

—————————————————————————————————

T-1) Preliminary step

Install a bunch of fedora packages (more may be installed automatically later), type (as a superuser):

dnf install git

dnf install wget

dnf install patch

dnf install m4

dnf install bzip2

dnf install unzip

dnf install tar

dnf install libsodium-devel

dnf install gcc

dnf install gcc-c++

dnf install leveldb-devel

dnf install make

dnf install snappy-devel

dnf install pkgconf-pkg-config

dnf install rpcbind

—————————————————————————————————

T-2) Enable sudo (I don't like it but is needed)

As a root (superuser) type:

usermod <user> -a -G wheel

where <user> is the name of the user that has access to the Raspberry PI (<user> is the name assigned during the initial Fedora installation. It will be invoked frequently further below). To activate the change, log out and log in again with the <user> name.

—————————————————————————————————

T-3) Set rpcbind at boot and through the firewall

Type (as a superuser):

systemctl enable rpcbind

systemctl start rpcbind

semanage permissive -a rpcbind_t

firewall-cmd --permanent --zone=public --add-service=rpc-bind

systemctl restart firewalld

and check that it is enabled:

firewall-cmd --list-all-zones

—————————————————————————————————

T-4) Installation of OCaml

Some some info, here1,  here2 and here3

As a superuser type:

wget https://raw.github.com/ocaml/opam/master/shell/opam_installer.sh -O - | sh -s /usr/local/bin

then as a user move to the folder /home/<user>:

cd /home/<user>

then type:

opam init --comp= 4.04.2

this will initialize OPAM and compile OCaml 4.04.2 (the current version of OCaml needed by Tezos). 
The process may take a while (warning about aspcud could be ignored).

—————————————————————————————————

T-5) Install Tezos (part I)

Some info, here1, here2 and here3

As a user in /home/<user> type:

git clone https://github.com/tezos/tezos

(NOTE: to install Tezos from the alphanet branch, move to /home/<user>/tezos and type:

git checkout alphanet

end of the NOTE)

Now type:

opam switch “tezos” --alias-of 4.04.2

eval `opam config env`

then type:

opam update

opam upgrade

The above steps opam switch.., opam update and opam upgrade may take a while.
I found that the next step was needed during the installation on the second Raspberry PI. I think it won't do any harm to do it anyway (always as a user):

opam install git-unix

if it is already installed, nothing will happen.
Now the big move, type:

make build-deps

It may take a while but eventually it will fail (guarantee it will fail!).
(NOTE: there are some warnings related to rsync WARNING Rsync partially failed: symlink has no referent.... So far I have ignored them with no apparent harm).

—————————————————————————————————

T-6) Install Tezos (part II): first fix (specific to Fedora distribution)

One reason for the make build-deps failure is that Tezos needs a package pkgconfig that in Fedora has been renamed pkgconf-pkg-config. During the installation it is recognized as the replacement for pkgconfig but during a final check it is marked as a missing package and the compilation stops. The problem has been discussed here. The way around this issue is to move as a user to /home/<user>/tezos/script:

cd /home/<user>/tezos/script

then edit the file install_build_deps.sh and comment the following line:
opam depext tezos

basically replace the above line with:
#opam depext tezos

Now try again:

make build-deps

(NOTE: this must be the second call of make build-deps because even though the first call failed, it installed some necessary packages that otherwise won't be included when opam depext tezos is commented). 
If everything goes as planned, after a while it will fail (yes, again) but this time the problem is with a package called uri.

—————————————————————————————————

T-7) Install Tezos (part III): specific procedure for any computer with ARM architecture

The OPAM package uri needed by Tezos does not compile on ARM architecture. The problem has been discussed here. The workaround is the following.
As a user  move to /home/<user>/.opam/tezos/build:

cd /home/<user>/.opam/tezos/build

and create a folder temp by typing:

mkdir temp

Move to the temp folder:

cd temp

and type:

opam source uri.1.9.5

This will download and extract the package files.
A file called jbuild needs to be modified in 3 locations. The locations are:

/home/<user>/.opam/tezos/build/temp/uri.1.9.5/config
/home/<user>/.opam/tezos/build/temp/uri.1.9.5/etc
/home/<user>/.opam/tezos/build/temp/uri.1.9.5/lib

Every jbuild file consist of block of instructions (called stanzas). The blocks look like this:

(library
            (  )
            (  )
            ..
            ..
            (  ))

or similar ones with library replaced for example by executable. Now in each block that begins  with library or executable the following two lines should be added (where the dots ... are shown in the above stanza):

(flags (:standard -w -3))
(ocamlopt_flags (:standard -fno-thumb))

One extra step. The jbuild file in /home/user/.opam/tezos/build/temp/uri.1.9.5/etc includes four stanzas. Two stanzas look like this:

(rule
             (service_full)
              ..
              ..
              (  ))

and this:

(library
             (service_full)
              ..
              ..
              (  ))

they both should be deleted so that only two stanzas remain in the jbuild file.
Now in the folder /home/user/.opam/tezos/build/temp type:

opam pin add uri uri.1.9.5

By doing this the modified package uri.1.9.5 will be compiled successfully and pinned locally.

—————————————————————————————————

T-8) Install Tezos (part IV)

If everything goes well this time:

make build-deps

will complete without errors. If not, then try again in sequence:

opam update

opam upgrade

eval `opam config env`

make build-deps

if still doesn't work then...see previous links for some help or more reading here1, here2 and here3.

If everything failed, delete the tezos and .opam folder. Type as a user:

cd /home/<user>

rm -rf tezos

rm -rf .opam

then go back to the beginning and start over the OCaml-OPAM-Tezos installation!!

—————————————————————————————————

T-9) Install Tezos (part V)

Final step in /home/<user>/tezos type as user:

make

this should create 💚tezos-node, 💚tezos-client and 💚tezos-protocol-compiler. Check them out:

ls -l

and see that the three (green) files are there (not the hearts though).
During the compilation on the first Raspberry PI there was a problem about EndianBigstring in minutils. Thanks to Milo Davis, there is a easy fix. Just edit jbuild in /home/<user>/tezos/src/minutils and add a line following the instruction in the easy fix link.

—————————————————————————————————

T-10) To switch to the alphanet branch of the Tezos project AFTER compilation of the source from the main branch

Move to the folder /home/<user>/tezos/script and in the file install_build_deps.sh uncomment the following line:

#opam depext tezos

that is remove #. Then in folder /home/<user>/tezos type (as a user):

git checkout alphanet

eval `opam config env`

make build-deps

Reintroduce the comment(#) of opam depext tezos (file install_build_deps.sh) and repeat:

make build-deps

and finally:

make clean

make

—————————————————————————————————

T-11) Open ports for Tezos

Assuming that the default ports for the network, RPC and peers search (9732, 8732, 7732) respectively will be changed to 9733(tcp), 8733(tcp and udc), 7733(udc), first open the ports in the modem and router following the procedure outlined earlier for the ssh service (in the previous example port 19899 was opened for ssh). Then type as a superuser:

firewall-cmd --permanent --zone=public --add-port=9733/tcp

firewall-cmd --permanent --zone=public --add-port=8733/tcp

firewall-cmd --permanent --zone=public --add-port=8733/udp

firewall-cmd --permanent --zone=public --add-port=7733/udp

systemctl restart firewalld

This may come handy if more than one machine is running Tezos using the same internet line.

—————————————————————————————————

T-12) Running the show (first rehearsal)

More info here and here.
In the folder home/<user>/.tezos-node as a user create with the editor a file config.json that includes the following lines:

{
      "rpc": {
        "listen-addr": "localhost:8733"
   },
      "net": {
         "bootstrap-peers": [
            "bootstrap.tezos.com",
            "localhost:7733"
         ],
         "listen-addr": "localhost:9733"
   }
}

(NOTE: more info about the format and options of file config.json can be  found by typing ./tezos-node config --help and then try one of the available options, for example ./tezos-node config --connections=20 will show the correct format to limit the number of connections).

Now in home/<user>/tezos type:

./tezos-node identity generate 24.

and start the service:

./tezos-node run 

more options ara available (see the above links). For example to manually setup the various IP addresses type:

./tezos-node run --net-addr localhost:9733 --rpc-addr localhost:8733 --peer localhost:7733 

While the tezos node is running, in another terminal shell type:

./tezos-client --port 8733 network stat

the IP address connecting the Raspberry PI to the internet (https://www.whatismyip.com/) should be in the list. For staking type:

./tezos-client --port 8733 launch daemon

 More commands are available, see the above links or type:

./tezos-client --port 8733 --help

and also:

./tezos-client --port 8733 man

For example, following the instructions from the above links, to generate a cryptographic identity type:

./tezos-client --port 8733 gen keys <my_identity>

where <my_identity> is...your chosen name for an identity. Then to get yourself a ~virtual~ reward of 100000 tezzies, type:

./tezos-client --port 8733 originate free account <my_account> for <my_identity>

where <my_account> should be replaced by your chosen name for a ~virtual~ account. To check the balance type:

./tezos-client --port 8733 get balance for <my_account>

and to delegate your staking right to a baker (in this case yourself), type:

./tezos-client --port 8733 get delegate for <my_account>

./tezos-client --port 8733 transfer 50,000.00 from <my_account> to <my_identity>

where with the last command you provide bonds taken from your default account in order to claim the delegation privilege.
To stop the services, type:

ps aux

then:

kill -9 <PID>

where <PID> is the number associated to the command ./tezos-node or ./tezos-client.
(NOTE: as pointed out here terminating the Tezos-node with the Ctrl-C sequence may lead to problems after restarting the Tezos service. I confirm this issue. The Tezos development team seems to be aware of the inconvenience).

Here are two links (1 and 2) to see the activities on the Tezos network. 

Hopefully with the official release of the network more info in particular about the tezos-client service will be released and a easy front-end for the various commands will be available. In the meantime a web-browser client for Tezos developed by Fredcy is available here.

—————————————————————————————————

T-13) Recompile Tezos (e.g. when a new release becomes available)

Remove the folder tezos in /home/<user>:

rm -rf tezos

then:

git clone https://github.com/tezos/tezos

to switch to the alphanet branch, move to /home/<user>/tezos and type as a user:

git checkout alphanet

Now in /home/<user> type:

opam switch “tezos” --alias-of 4.04.2

eval `opam config env`

then:

make build-deps

As discussed earlier, the compilation will fail. Move to the directory /home/<user>/tezos/script then edit the file install_build_deps.sh and comment the following line: opam depext tezos.  Now repeat the command make build-deps and this time the compilation should be successful. The final step:

make clean

make

will re-create the 3 green executables, tezos-node, tezos-client and tezos-protocol-compiler.

—————————————————————————————————

T-14) Raspberry PI 3 limitations (work in progress)

The service tezos-node runs as expected for a certain amount of time but eventually will crash. At the present time I am not sure the exact reason, the error logs are not consistent (out of memory, too many open files etc.). My guess is that the Raspberry PI 3 has a problem handling the increased number of peer connections that are established over time. I am considering the following possible remedies: 1) keep the amount of CPU used by tezos-node under control below 100%, 2) reduce the number of peer connections, 3) a combination of both.

There are few options to control the CPU usage, see for example here. I am testing the program cpulimit. 
In /home/<user> type:

wget -O cpulimit.zip https://github.com/opsengine/cpulimit/archive/master.zip

then:

unzip cpulimit.zip

cd cpulimit-master

make

in the folder /home/<user>/cpulimit-master/src type as a root:

cp cpulimit /usr/bin

This will complete the installation of cpulimit.
To launch tezos-node, in /home/<user>/tezos type as a user:

cpulimit -l 50 ./tezos-node run

where for example 50 is the maximum CPU in % allowed for the process (work in progress). Additional options to control cpulimit can be found in the previous link or here.

To control the number of connections, the file config.json in *home/<user>/.tezos-node8 should look like this:

{
      "rpc": {
        "listen-addr": "localhost:8733"
   },
      "net": {
         "bootstrap-peers": [
            "bootstrap.tezos.com",
            "localhost:7733"
         ],
         "listen-addr": "localhost:9733",
         "limits": {
           "min-connections": 1,
           "expected-connections": 10,
           "max-connections": 11
                       }
                }
}

where the numbers in min-connections, expected-connections and max-connections should be adjusted (work in progress). See my note in T-12) about the format of this file.

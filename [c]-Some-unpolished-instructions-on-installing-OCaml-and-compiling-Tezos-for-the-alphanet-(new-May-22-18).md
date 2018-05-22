\
\
Here some bare bones info to install OCaml and Opam and compile Tezos for the alphanet.

***

**Part I** Install or update OCaml (version 4.06.1) and Opam (version 2.0.0).\
If you have already installed Fedora 28 using the image file provided in this wiki pages (RPI3-TEZOS-050618.img), then
the correct versions of OCaml and Opam are already installed.
If you like have a look with the following commands then jump to **Part II** here below.\
`opam show`\
`opam config list`
\
\
If OCaml was never installed before, as a _root/superuser_ type:

`wget https://raw.github.com/ocaml/opam/master/shell/opam_installer.sh -O - | sh -s /usr/local/bin`

then as a _user_ move to the folder _/home/<[]()user>_ and type:

`opam init --comp= 4.06.1`

this will initialize Opam and compile OCaml 4.06.1 (the current version of OCaml needed by Tezos).                                                                                                                                                             The process may take a while (warning about *aspcud* could be ignored).
Use the commands given above to make sure that Opam 2.0.0 and OCaml 4.06.1 are installed correctly.
\
\
\
In case Opam 2.0.0 is not installed (find out with `opam config list`), then as a _root/superuser_ type:

`wget https://github.com/ocaml/opam/releases/download/2.0.0-rc/opam-2.0.0-rc-armhf-linux`

and\
`cp opam-2.0.0-rc-armhf-linux /usr/local/bin/opam`

then as a _user_ type:

`opam config list`\
`opam update`
\
\
\
In case OCaml 4.06.1 is not the default version (find out with `opam show`), then as a _user_ type:

`opam switch 4.06.1`

if a previous installation of OCaml compiler named _tezos_ is listed with the command `opam show`, just get rid of it:

`opam switch remove "tezos"`

and create a new one:

`opam switch create tezos ocaml-base-compiler.4.06.1`

and update the shell environment variables with: \
`eval $(opam env)`

To save some space remove any OCaml version that is not 4.06.1, for example:

`opam switch remove 4.04.2`
\
\
\

***

**Part II** Compile and install Tezos (alphanet) \
The procedure to compile and install tezos is much smoother than before. To get Tezos for the alphanet, as a _user_ move to this location _/home/tzuser_ and remove any existing folder named _/home/tzuser/tezos_:\
`rm -rf tezos`

then get the latest copy of tezos for the alphanet:

`git clone -b alphanet https://gitlab.com/tezos/tezos.git`

(note in the image file "RPI3-TEZOS-050618.img" for F28, the default user name is _tzuser_)\
Install manually the following library :

`opam install conf-libev`

This operation will recompile some of the packages already installed (e.g. lwt)\
then type: 

`make build-deps` 

and then

`make`

The last two steps should be completed without errors (verified on 05/18/18). Look in the folder _/home/tzuser/tezos_ for the green light! e.g. _tezos-node_, _tezos-client_ and few others.







 
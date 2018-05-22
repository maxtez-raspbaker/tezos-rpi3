
Here some bare bones info to compile Tezos for the alphanet.
\
\
\
**part I** 
If you have installed Fedora 28 using the image file provided in this wiki pages (tz-alpha051818.)
opam 2.0.0 is needed. It can be retrieved manually, as a superuser:

`wget https://github.com/ocaml/opam/releases/download/2.0.0-rc/opam-2.0.0-rc-armhf-linux`

`cp opam-2.0.0-rc-armhf-linux /usr/local/bin/opam`

then as a user:

`opam config list`

`opam update`
\
\
\
OCaml 4.06.1 is also needed (starting 03/31/2018) 

`opam switch 4.06.1`

`opam switch remove "tezos"`

`opam switch create tezos ocaml-base-compiler.4.06.1`

`eval $(opam env)`

`opam switch remove 4.04.2`
\
\
\
**part II** 
The procedure to compile and install tezos is much smoother than before. To get Tezos for the alphanet, from /home/tzuser
first remove any existing folder named tezos:\
`rm -rf tezos`\

then get the latest copy of tezos for the alphanet:\
`git clone -b alphanet https://gitlab.com/tezos/tezos.git`\

Install manually the following library :

`opam install conf-libev`

This operation will recompile some of the packages already installed (e.g. lwt)\
\
\
\
then type: \
`make build-deps` \
and then\
`make`\

The last two steps should complete without errors (verified on 05/18/18). Look in the folder "tezos" for the green lights, e.g. _tezos-node_, _tezos-client_ and few others.


<!---  If with the command `make` there is an error like this:

> Error: Integer literal exceeds the range of representable integers of type int

in "src/lib_data_encoding/data_encoding.ml", line 204, characters 17-30

replace -2_147_483_646 with -1_073_741_824 and replace 2_147_483_646  with 1_073_741_823 --->




 
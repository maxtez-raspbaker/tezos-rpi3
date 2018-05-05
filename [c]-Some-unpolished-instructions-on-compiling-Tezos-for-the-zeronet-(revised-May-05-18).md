I am throwing some bare bones info to compile Tezos for the zeronet.
\
\
\
opam 2.0.0 is needed. It can be retrieved manually, as a superuser:

`wget https://github.com/ocaml/opam/releases/download/2.0.0-rc/opam-2.0.0-rc-armhf-linux`

`cp opam-2.0.0-rc-armhf-linux /usr/local/bin/opam`

then as a user:

`opam config list`

`opam update`
\
\
\
OCaml 4.06.1 is also needed now (03/31/2018) 

`opam switch 4.06.1`

`opam switch remove "tezos"`

`opam switch create tezos ocaml-base-compiler.4.06.1`

`eval $(opam env)`

`opam switch remove 4.04.2`
\
\
\
To get Tezos for the zeronet, from /home/tzuser:

`rm -rf tezos`

`git clone -b zeronet https://gitlab.com/tezos/tezos.git`

<!--- The package URI still needs to be pinned locally, the new version is 1.9.6. In /home/tzuser/.opam/tezos/build/temp type:

`opam pin remove uri`

`opam source uri.1.9.6`

then edit 3 jbuild files as discussed in page [b-1] and compile the new URI:

`opam pin add uri uri.1.9.6`) --->


A new library should be installed to improve performance. It may also avoid a crash of the node with this error "_Unix.Unix_error(Unix.EINVAL, "select", "")_":

`opam install conf-libev`

This operation will recompile some of the packages already installed (e.g. lwt)
\
\
\
At the moment the package "ocplib-json-typed" should be installed manually somewhere, for example in /home/tzuser/.opam/tezos/build type:

`git clone https://github.com/ocamlpro/ocplib-json-typed.git`

`opam pin add ocplib-json-typed/`
\
\
It appears that there is a typo in the file /home/tzuser/tezos/src/lib_data_encoding/jbuild. The line "ocplib-json-typed.bson" should be "ocplib-json-typed-bson"

     ....

       (libraries (tezos-stdlib

              ocplib-json-typed

              ocplib-json-typed-bson

              ezjsonm))

     ....
\
With `make build-deps` the package "pkgconf-pkg-config" is still not recognized during the final check.
Follow the instructions in page [b-1] to solve this issue.



<!---  If with the command `make` there is an error like this:

> Error: Integer literal exceeds the range of representable integers of type int

in "src/lib_data_encoding/data_encoding.ml", line 204, characters 17-30

replace -2_147_483_646 with -1_073_741_824 and replace 2_147_483_646  with 1_073_741_823 --->




 
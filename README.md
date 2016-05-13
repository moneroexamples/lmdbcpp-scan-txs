# lmdbcpp-scan-txs

Monero is using [lmdb databases](http://symas.com/mdb/) to store its blockchain.

Although one can interact directly with the blockchain to get information
from it using Monero C++ libraries,
searching and retrieving some information maybe be not very efficient.

This in this example, it is shown how to use [previously created](https://github.com/moneroexamples/lmdbcpp-monero) custom lmdb databases
based on information from the blockchain using [lmdb++](https://github.com/bendiken/lmdbxx)
interface to the lmdb database.

## Custom databases

The example uses the following lmdb database based on the information
available in the blockchain:
- `output_info` - key: output public key as string; value: struct {tx_hash as hash,
tx_pub_key as public_key, amount as uint64_t, index_in_tx as uint64_t}

## Prerequisite

Everything here was done and tested using Monero 0.9.4 on
Xubuntu 16.04 x86_64.

Instruction for Monero 0.9 compilation and Monero headers and libraries setup are
as shown here:
 - [Compile Monero 0.9 on Ubuntu 16.04 x64](https://github.com/moneroexamples/compile-monero-09-on-ubuntu-16-04)


## C++ code
The main part of the example is main.cpp.

```c++
```



## Compile this example
The dependencies are same as those for Monero, so I assume Monero compiles
correctly. If so then to download and compile this example, the following
steps can be executed:

```bash
# download the source code
git clone https://github.com/moneroexamples/lmdbcpp-scan-txs.git

# enter the downloaded sourced code folder
cd lmdbcpp-scan-txs

# create the makefile
cmake .

# compile
make
```

## Other examples
Other examples can be found on  [github](https://github.com/moneroexamples?tab=repositories).
Please know that some of the examples/repositories are not
finished and may not work as intended.

## How can you help?

Constructive criticism, code and website edits are always good. They can be made through github.

Some Monero are also welcome:
```
48daf1rG3hE1Txapcsxh6WXNe9MLNKtu7W7tKTivtSoVLHErYzvdcpea2nSTgGkz66RFP4GKVAsTV14v6G3oddBTHfxP6tU
```

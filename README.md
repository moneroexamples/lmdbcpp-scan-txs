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
 - [lmdbcpp-monero](https://github.com/moneroexamples/lmdbcpp-monero) 

## C++ code
The main part of the example is main.cpp.

```c++
int main(int ac, const char* av[])  {


    // get command line options
    xmreg::CmdLineOptions opts {ac, av};

    auto help_opt = opts.get_option<bool>("help");

    // if help was chosen, display help text and finish
    if (*help_opt)
    {
        return EXIT_SUCCESS;
    }

    // get other options
    auto bc_path_opt  = opts.get_option<string>("bc-path");
    auto testnet_opt  = opts.get_option<bool>("testnet");
    auto search_opt   = opts.get_option<bool>("search");

    bool testnet         = *testnet_opt;

    string xmr_address_str {"43A7NUmo5HbhJoSKbw9bRWW4u2b8dNfhKheTR5zxoRwQ7bULK5TgUQeAvPS5EVNLAJYZRQYqXCmhdf26zG2Has35SpiF1FP"};
    string viewkey_str    {"9c2edec7636da3fbb343931d6c3d6e11bcd8042ff7e11de98a8d364f31976c04"};

    // parse string representing given monero address
    cryptonote::account_public_address address;

    if (!xmreg::parse_str_address(xmr_address_str,  address, 0))
    {
        cerr << "Cant parse string address: " << xmr_address_str << endl;
        return EXIT_FAILURE;
    }

    // parse string representing given private viewkey
    crypto::secret_key prv_view_key;

    if (!xmreg::parse_str_secret_key(viewkey_str, prv_view_key))
    {
        cerr << "Cant parse view key: " << viewkey_str << endl;
        return EXIT_FAILURE;
    }



    path blockchain_path;

    if (!xmreg::get_blockchain_path(bc_path_opt, blockchain_path))
    {
        // if problem obtaining blockchain path, finish.
        return 1;
    }


    fmt::print("Blockchain path: {:s}\n", blockchain_path);


    // the directory MUST already exist. Make it manually
    path mylmdb_location  = blockchain_path.parent_path() / path("lmdb2");

    // instance of MyLMDB class that interacts with the custom database
    xmreg::MyLMDB mylmdb {mylmdb_location.string()};

    uint64_t out_idx = {0};

    auto output_search = [&](crypto::public_key& out_pubkey,
                             xmreg::output_info& out_info) -> bool
    {

        // public transaction key is combined with our viewkey
        // to create, so called, derived key.
        crypto::key_derivation derivation;

        bool r = generate_key_derivation(out_info.tx_pub_key,
                                         prv_view_key,
                                         derivation);

        if (r)
        {

            // get the tx output public key
            // that normally would be generated for us,
            // if someone had sent us some xmr.
            crypto::public_key generated_pubkey;

            derive_public_key(derivation,
                              out_info.index_in_tx,
                              address.m_spend_public_key,
                              generated_pubkey);

            // check if generated public key matches the current output's key
            bool mine_output = (out_pubkey == generated_pubkey);

            if (++out_idx % 1000 == 0)
            {
                cout << "Checkign outout: "<< out_idx << endl;
            }

            if (mine_output)
            {
                cout << "Found mine output: " << out_pubkey << " \n"
                     << "\t - deriviation : " << derivation << "\n"
                     << "\t - tx_hash     : " << out_info.tx_hash << "\n"
                     << "\t - tx_pub_key  : " << out_info.tx_pub_key << "\n"
                     << "\t - amount      : " << XMR_AMOUNT(out_info.amount) << "\n"
                     << "\t - index_in_tx : " << out_info.index_in_tx
                     << endl;
            }

            //xmreg::wait_for_enter();
        }
        else
        {
            cerr << "Cant get dervied key for: "  << "\n"
                 << "pub_tx_key: " << out_info.tx_pub_key << " and "
                 << "prv_view_key" << prv_view_key << endl;
        }

        return true;
    };

    mylmdb.for_all_outputs(output_search);

    return EXIT_SUCCESS;
}
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

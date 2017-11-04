## Pirl
  * Time between block: 13 sec
  * total block reward : 12 PIRL
  * Miner reward : 10 PIRL / block
  * Masternode / dev : 2  PIRL / block
  * Network ID : 3125659152
  * rpc port : 6588
  * Explorer: https://explorer.pirl.io


## Open Firewall Port:

* 6588
* 30303 ( TCP / UDP )

## Quick command:

List all accounts: personal.listAccounts

Unlock wallet: personal.unlockAccount(youraddress, passwd, duration)

Number of synced blocks (compare with some pool or explorer if you are fully synced): eth.blockNumber

Info about connected peers: admin.peers

Balance: web3.fromWei(eth.getBalance("youraddress"), "ether") or or web3.fromWei(eth.getBalance(eth.coinbase), "ether")

Send transaction: eth.sendTransaction({from: "address", to: "address", value: web3.toWei(1, "ether")})

## Quickstart docker:
```
docker run -itd --name pirl  -p 6588:6588 -p 30303:30303 -p 30303:30303/udp pirl/pirl-node
```
  * to enter the container:
```
  docker exec -it pirl /bin/sh
```
You can also find compiled bin in release.

## Run bin in Linux:
```
  /usr/bin/pirl --rpc --rpcaddr 127.0.0.1 --rpccorsdomain * --rpcport 6588  --rpcapi "eth,net,web3"
```


## Systemd start:
```
[Unit]
Description=Pirl

[Service]
Type=simple
Restart=always
ExecStart=/usr/bin/pirl --rpc --rpcaddr 127.0.0.1 --rpccorsdomain * --rpcport 6588  --rpcapi "eth,net,web3"
[Install]
WantedBy=default.target

```



## Go Pirl

Official golang implementation of the Pirl protocol.


Automated builds are available for stable releases and the unstable master branch.
Binary archives are published at http://release.pirl.io
For linux: http://release.pirl.io/latest.tar.gz

## Building the source

Once the dependencies are installed, run

    `make pirl`

or, to build the full suite of utilities:

    `make all`

## Executables

The pirl project comes with several wrappers/executables found in the `cmd` directory.

| Command    | Description |
|:----------:|-------------|
| **`pirl`** | Our main pirl CLI client. It is the entry point into the pirl network (main-, test- or private net), capable of running as a full node (default) archive node (retaining all historical state) or a light node (retrieving data live). It can be used by other processes as a gateway into the pirl network via JSON RPC endpoints exposed on top of HTTP, WebSocket and/or IPC transports. `pirl --help` and the [CLI Wiki page](https://github.com/pirl/pirl/wiki/Command-Line-Options) for command line options. |
| `abigen` | Source code generator to convert pirl contract definitions into easy to use, compile-time type-safe Go packages. It operates on plain [pirl contract ABIs](https://github.com/pirl/wiki/wiki/pirl-Contract-ABI) with expanded functionality if the contract bytecode is also available. However it also accepts Solidity source files, making development much more streamlined. Please see our [Native DApps](https://github.com/pirl/pirl/wiki/Native-DApps:-Go-bindings-to-pirl-contracts) wiki page for details. |
| `bootnode` | Stripped down version of our pirl client implementation that only takes part in the network node discovery protocol, but does not run any of the higher level application protocols. It can be used as a lightweight bootstrap node to aid in finding peers in private networks. |
| `evm` | Developer utility version of the EVM (Ethereum Virtual Machine) that is capable of running bytecode snippets within a configurable environment and execution mode. Its purpose is to allow insolated, fine-grained debugging of EVM opcodes (e.g. `evm --code 60ff60ff --debug`). |
| `pirlrpctest` | Developer utility tool to support our [pirl/rpc-test](https://github.com/pirl/rpc-tests) test suite which validates baseline conformity to the [pirl JSON RPC](https://github.com/pirl/wiki/wiki/JSON-RPC) specs. Please see the [test suite's readme](https://github.com/pirl/rpc-tests/blob/master/README.md) for details. |
| `rlpdump` | Developer utility tool to convert binary RLP ([Recursive Length Prefix](https://github.com/pirl/wiki/wiki/RLP)) dumps (data encoding used by the pirl protocol both network as well as consensus wise) to user friendlier hierarchical representation (e.g. `rlpdump --hex CE0183FFFFFFC4C304050583616263`). |
| `swarm`    | swarm daemon and tools. This is the entrypoint for the swarm network. `swarm --help` for command line options and subcommands. See https://swarm-guide.readthedocs.io for swarm documentation. |
| `puppeth`    | a CLI wizard that aids in creating a new pirl network. |

## Running pirl

Going through all the possible command line flags is out of scope here (please consult our
[CLI Wiki page](https://github.com/pirl/pirl/wiki/Command-Line-Options)), but we've
enumerated a few common parameter combos to get you up to speed quickly on how you can run your
own pirl instance.

### Full node on the main Pirl network

By far the most common scenario is people wanting to simply interact with the Pirl network:
create accounts; transfer funds; deploy and interact with contracts. For this particular use-case
the user doesn't care about years-old historical data, so we can fast-sync quickly to the current
state of the network. To do so:

```
$ pirl --fast --cache=512 console
```

This command will:

 * Start pirl in fast sync mode (`--fast`), causing it to download more data in exchange for avoiding
   processing the entire history of the pirl network, which is very CPU intensive.
 * Bump the memory allowance of the database to 512MB (`--cache=512`), which can help significantly in
   sync times especially for HDD users. This flag is optional and you can set it as high or as low as
   you'd like, though we'd recommend the 512MB - 2GB range.
 * Start up pirl's built-in interactive [JavaScript console](https://github.com/pirl/pirl/wiki/JavaScript-Console),
   (via the trailing `console` subcommand) through which you can invoke all official [`web3` methods](https://github.com/pirl/wiki/wiki/JavaScript-API)
   as well as pirl's own [management APIs](https://github.com/pirl/pirl/wiki/Management-APIs).
   This too is optional and if you leave it out you can always attach to an already running pirl instance
   with `pirl attach`.


#### Docker quick start

One of the quickest ways to get pirl up and running on your machine is by using Docker:

```
docker run -itd --name pirl  -p 6588:6588 -p 30303:30303 -p 30303:30303/udp pirl/pirl-node

```

Create an address:
  * `docker exec -it pirl /bin/sh`
  * `pirl attach`
  * `personal.newAccount("passphrase")`
  * `!!!!!!!!!! retrieve content and backup wallet !!!!!!!!!!!!!!!!!!!!`
  * `exit`
  * `cd /root/.pirl/keystore/`
  * `ls ( to view the file )`
  * `cat NAME_OF_THE_FILE_YOU_SEE`
  * `Copy the output`
  * `Rpc port is available on your host @ 127.0.0.1:6588`


This will start pirl in fast sync mode with a DB memory allowance of 512MB just as the above command does.  It will also create a persistent volume in your home directory for saving your blockchain as well as map the default ports. There is also an `alpine` tag available for a slim version of the image.

### Programatically interfacing pirl nodes

As a developer, sooner rather than later you'll want to start interacting with pirl and the pirl
network via your own programs and not manually through the console. To aid this, pirl has built in
support for a JSON-RPC based APIs ([standard APIs](https://github.com/pirl/wiki/wiki/JSON-RPC) and
[pirl specific APIs](https://github.com/pirl/pirl/wiki/Management-APIs)). These can be
exposed via HTTP, WebSockets and IPC (unix sockets on unix based platforms, and named pipes on Windows).

The IPC interface is enabled by default and exposes all the APIs supported by pirl, whereas the HTTP
and WS interfaces need to manually be enabled and only expose a subset of APIs due to security reasons.
These can be turned on/off and configured as you'd expect.

HTTP based JSON-RPC API options:

  * `--rpc` Enable the HTTP-RPC server
  * `--rpcaddr` HTTP-RPC server listening interface (default: "localhost")
  * `--rpcport` HTTP-RPC server listening port (default: 8545)
  * `--rpcapi` API's offered over the HTTP-RPC interface (default: "eth,net,web3")
  * `--rpccorsdomain` Comma separated list of domains from which to accept cross origin requests (browser enforced)
  * `--ws` Enable the WS-RPC server
  * `--wsaddr` WS-RPC server listening interface (default: "localhost")
  * `--wsport` WS-RPC server listening port (default: 8546)
  * `--wsapi` API's offered over the WS-RPC interface (default: "eth,net,web3")
  * `--wsorigins` Origins from which to accept websockets requests
  * `--ipcdisable` Disable the IPC-RPC server
  * `--ipcapi` API's offered over the IPC-RPC interface (default: "admin,debug,eth,miner,net,personal,shh,txpool,web3")
  * `--ipcpath` Filename for IPC socket/pipe within the datadir (explicit paths escape it)

You'll need to use your own programming environments' capabilities (libraries, tools, etc) to connect
via HTTP, WS or IPC to a pirl node configured with the above flags and you'll need to speak [JSON-RPC](http://www.jsonrpc.org/specification)
on all transports. You can reuse the same connection for multiple requests!

**Note: Please understand the security implications of opening up an HTTP/WS based transport before
doing so! Hackers on the internet are actively trying to subvert pirl nodes with exposed APIs!
Further, all browser tabs can access locally running webservers, so malicious webpages could try to
subvert locally available APIs!**


#### Running a private miner directly on node

Mac : https://github.com/ethereum-mining/ethminer/releases/download/v0.12.0/ethminer-0.12.0-Darwin.tar.gz
Windows :
https://github.com/ethereum-mining/ethminer/releases/download/v0.12.0/ethminer-0.12.0-Windows.zip
Linux:
https://github.com/ethereum-mining/ethminer/releases/download/v0.12.0/ethminer-0.12.0-Linux.tar.gz



Mining on the public pirl network is a complex task as it's only feasible using GPUs, requiring
an OpenCL or CUDA enabled `ethminer` instance. For information on such a setup, please consult the
[EtherMining subreddit](https://www.reddit.com/r/pirl/) and the 

repository.

In a private network setting however, a single CPU miner instance is more than enough for practical
purposes as it can produce a stable stream of blocks at the correct intervals without needing heavy
resources (consider running on a single thread, no need for multiple ones either). To start a pirl
instance for mining, run it with all your usual flags, extended by:

```
$ pirl <usual-flags> --mine --minerthreads=1 --etherbase=0x0000000000000000000000000000000000000000
```

Which will start mining bocks and transactions on a single CPU thread, crediting all proceedings to
the account specified by `--etherbase`. You can further tune the mining by changing the default gas
limit blocks converge to (`--targetgaslimit`) and the price transactions are accepted at (`--gasprice`).


## License

The pirl library (i.e. all code outside of the `cmd` directory) is licensed under the
[GNU Lesser General Public License v3.0](https://www.gnu.org/licenses/lgpl-3.0.en.html), also
included in our repository in the `COPYING.LESSER` file.

The pirl binaries (i.e. all code inside of the `cmd` directory) is licensed under the
[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html), also included
in our repository in the `COPYING` file.

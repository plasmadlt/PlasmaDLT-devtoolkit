# PlasmaDLT ION.CDT Set-up Guide

# Minimum System Requirements
- The hardware must meet certain requirements to run a full node.
- 500 GB of free disk space
- Accessible at a minimum read/write speed of 100 MB/s
- 4 cores of CPU and 8 gigabytes of memory (RAM)

* Create dirs for ios node data
```
mkdir /data
mkdir /data/plasma
mkdir /data/plasma/ioncdt
mkdir /data/plasma/bios
mkdir /data/plasm/bios/wallet
```

# Installing the ION.CDT
```
docker pull plasmachain/ion.cdt
```
# Choice Local  
* go to https://hub.docker.com/r/plasmachain/plasma-local-node
```
docker pull plasmachain/plasma-local-node
```
# Choice testnet  
* go to https://hub.docker.com/r/plasmachain/friedman-testnet
```
docker pull plasmachain/friedman-testnet
```
# Choice mainet
* go to  https://cloud.docker.com/u/plasmachain/repository/docker/plasmachain/mainet
```
docker pull plasmachain/mainet
```


# Build contract
```
docker run -it --entrypoint /contacts/build.sh -v <contract source folder>:/contacts  plasmachain/ion.cdt:latest
```

# Setting up Configurations Ionode testnet or mainet

- Get file  config.ini choice local node or testnet/mainet
```
cd config
```
2. If testnet Setup the config file to configure our node
- You need to get an official account testnet or mainet on https://app.plasmapay.com/id/profile/developers
- Get test token on https://app.plasmapay.com/id/profile/developers
- Get P2P endpoint  http://plasmadlt.com/monitor

# Get and edit the configuration file accordingly config.ini :
- http-server-address = 0.0.0.0:8701  register the port on which the host will listen to http requests
- p2p-listen-endpoint = 0.0.0.0:8601  set the port on which the host will listen for p2p incoming connections. Also, the value of this parameter should be given to the network administrator, so that he would include this host in the list of p2p-peer-address addresses of all producer nodes of the network.
- p2p-peer-address fill in the list of p2p network producer nodes. It must be obtained from the network testnet or mainet from http://plasmadlt.com/monitor.
- agent-name  set the name of the node.
- producer-name set the node name equal to the name of the account that the node owner received in step 1
- signature-provider - set the key values ​​that the node owner received from  https://app.plasmapay.com/id/profile/developers


# Plugin - fill in the plugins section:
- ion :: chain_api_plugin,
- ion :: http_plugin,
- ion :: net_api_plugin
- optional (ion :: history_plugin, ion :: history_api_plugin)

4. Start the wallet service (wallet), command example
```
docker exec -i <network>-bios-node sol wallet import --private-key
```
# For local testmode

* Create  Wallet
```
docker exec -i <network>-bios-node sol wallet create --to-console
#You get your private key /bin/plk
5Hw6Hx****
```
Import private key
```
#import your private key
docker exec -i <network>-bios-node sol wallet import --private-key  5Hw6Hx****
```
Get public key
```
#Now you get public key
docker exec -i <network>-bios-node sol wallet create_key
Created new private key with a public key of: PLASMA6pGFs***
```

Create account with Public Key
```
docker exec -i <network>-bios-node sol create account PLASMA6pGFs***

```
Create account name
```
docker exec -i <network>-bios-node sol create account hello  PLASMA6pGFs***

executed transaction: b11f180bb***....  200 bytes  2101 us
#           ion <= ion::newaccount              {"creator":"ion","name":"hello","owner":{"threshold":1,"keys":[{"key":"PLASMA6p****...
```


* Start the ionode process, sample command
```
docker exec -i <network>-bios-node ionode -e -p ion --delete-all-blocks
```
* After the node has played all the blocks of blockchain and created its own replica, the following commands can be executed as a command line utility as a test
```
docker exec -i <network>-bios-node sol get table plasma.token EURP stat
docker exec -i <network>-bios-node sol get account plasma.token
docker exec -i <network>-bios-node sol get abi plasma.token
```

- Check if you can access you node using link http://you_server:your_http_port/v1/chain/get_info
-  If you would like to run a Block Producer  node you need register your node at https://plasmadlt.com/#register

* Create Contract Hello world

```
mkdir hello
cd hello
```
Create contract in directory hello.cpp, collected contracts will be in the folder <contract source folder>/build

```
#include <ion/ion.hpp>

using namespace ion;

class [[ion::contract]] hello : public contract {
  public:
      using contract::contract;

      [[ion::action]]
      void hi( name user ) {
         print( "Hello, ", user);
      }
};
```

* Publishing contract
```
docker exec -i <network>-bios-node sol set contract hello .../hello -p hello@active
Reading WASM from  ..../hello/hello.wasm...
Publishing contract...
executed transaction: 95c92e2e***...  672 bytes  4891 us
#           ion <= ion::setcode                 {"account":"hello","vmtype":0,"vmversion":0,"code":"006173****...
#           ion <= ion::setabi                  {"account":"hello","abi":"0c696f6e3a3a6****...
```

* Push Action on contract
```
docker exec -i <network>-bios-node sol push action hello hi '["123"]' -p hello@active
executed transaction: 7bd013....***  104 bytes  1499 us
#         hello <= hello::hi                    {"user":"123"}
>> Hello, 123
```

# BeraChain-Validator-Node
BeraChain Local Validator Node

System Requirements
OS: linux
CPU Architecture: amd64 or arm64
CPU: 4 cores
RAM: 16GB
Storage: 500GB


**Binary**
mv <path_to_berad_download> /usr/local/bin/berad;

**Docker**
docker run -d -p 8545:8545 registry.domain/berad/seed:amd-latest bash -c "cd scripts/ && ./start-1-node-testnet.sh";

**Building from source**
git clone https://github.com/berachain/berachain
cd berachain

**Running Berachain Node**

**Step 1 - Initializing the Node**
# FROM: /your/path/to/berad;

# NOTE: make sure you set proper permission on the file `chmod +x` to allow for usage
# additionally you can specify the config directory with this flag `--home /path/to/.berad`
berad init <MONIKER> --chain-id <CHAINID>; # Ex: berad init beraBeraBananaBoBera --chain-id 123456789


# [Expected Output]:
# {
#  "moniker": "beraBeraBananaBoBera",
# ...

The MONIKER is a custom human readable name for this node
The chain ID flag is the chain ID of whichever Berachain Network you're connecting to.
This command will create the following config files in your --home directory. Note that the default home directory is ~/.berad

If successful, you should see the newly created ./berad directory created will the following files.



# FROM: /your/path/to/.berad;

.                                   # ~/.berad
  |- data                           # Contains the databases used by the node.
  |- config/
      |- app.toml                   # Application-related configuration file.
      |- config.toml                # CometBFT-related configuration file.
      |- genesis.json               # The genesis file.
      |- node_key.json              # Private key to use for node authentication in the p2p protocol.
      |- priv_validator_key.json    # Private key to use as a validator in the consensus protocol.



**  Step 2 - Config Genesis File**
The genesis.json file is a configuration file used in blockchain networks. It serves as the starting point of the blockchain, defining the initial state of the blockchain before any blocks are mined.

The genesis.json can be found in the berachain/node-config repository.


# FROM: /your/path/to/.berad/config;

# remove existing genesis.json
rm genesis.json;

# download new genesis.json file from node-config repository
curl -o genesis.json https://raw.githubusercontent.com/berachain/node-config/main/networks/closed-testnet/genesis/genesis.json;

# (Optional) - in case json isn't formatted correctly
# jq . genesis.json > genesis.formatted.json; rm genesis.json; mv genesis.formatted.json genesis.json;



**Step 3 - Configure Toml Files**
The app.toml and config.toml can be found in the berachain/node-config repository.

# FROM: /your/path/to/.berad/config;

# remove existing app.toml and config.toml
rm app.toml config.toml;

# download app.toml
curl -o app.toml https://raw.githubusercontent.com/berachain/node-config/main/networks/closed-testnet/archive/config/app.toml;

# download config.toml
curl -o config.toml https://raw.githubusercontent.com/berachain/node-config/main/networks/closed-testnet/archive/config/config.toml;


Make these adjustments based on your requirements:

Modify your app.toml for configs like pruning, api, etc.
Modify your config.toml for configs like moniker, ports, etc.
Set seed node addresses in config.toml by looking for this section and pasting in between the quotes the full comma separated addresses:


File: /your/path/to/.berad/config/config.toml

# ...

# Comma separated list of seed nodes to connect to
seeds = ""

# Example
# seeds = "abcd12345@some.example-domain.xyz:123456789,efghi56789@some.example-domain.xyz:123456789"

# ...


**Step 4 - Start the Node**

TIP

If syncing your node for the first time, you may be interested in faster syncing options by following the Syncing Your Node Instructions: https://docs.berachain.com/nodes/quickstart/syncing-node

# FROM: /your/path/to/berad;

./berad start;

# [Expected Output]:
# (THIS SHOULD BE GOING REALLY FAST)
# ...
# 8:37AM INF ...
# 8:37AM INF ...


Test that your node is up and running correctly by performing an RPC request.

Open up another terminal, that has access to the node, and run the following:

# FROM: /any/path/doesnt/matter;

curl -X POST -H "Content-Type: application/json" -d '{
    "jsonrpc":"2.0",
    "method": "eth_blockNumber",
    "params": [],
    "id": 1
}
' http://localhost:8545;

# [Expected Output]:
# {"jsonrpc":"2.0","id":1,"result":"0x7e8"}



Syncing Node
The faster your node gets up to sync with the current state of the network, the faster it can be used. Syncing is the process of a node catching up with the current state.

There are multiple methods of syncing your node:

Block Sync
The node downloads all of the data of an application from genesis and verifies it
State Sync (⚠️ work-in-progress)
The node downloads data related to the head or near the head of the chain and verifies the data
Snapshots
Find a trusted snapshot provider, download the snapshot and restore the state from that snapshot


**Adjusting Block Sync Configuration**
This option can be adjusted in the config.toml file.


To enable / disable Block Sync, edit the block_sync value:
block_sync = true


To change the version of Block Sync you are running, edit version value:
#######################################################
###       Block Sync Configuration Options          ###
#######################################################
[blocksync]

# Block Sync version to use:
# 
# In v0.37, v1 and v2 of the block sync protocols were deprecated.
# Please use v0 instead.
#
#   1) "v0" - the default block sync implementation
version = "v0"




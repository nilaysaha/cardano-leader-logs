# cardano-leader-logs
In a community effort, led by Andrew Westberg [BCSH], we implemented a way to retrieve stakepool slot leader logs.

This is a nodejs + python implementation which was tested on Ubuntu 20.04.

Japanese README: https://github.com/btbf/cardano-leader-logs

# installation

## Ubuntu 20.04

### nodejs

```bash
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -  
sudo apt-get update
sudo apt-get install -y nodejs

export NODE_PATH=/usr/lib/node_modules/

// Need to override the default heap memory limit for NodeJS
export NODE_OPTIONS="--max-old-space-size=8192"
```

### python3

```bash
sudo apt-get update
sudo apt-get install -y software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install -y python3.9
sudo apt-get install -y python3-pip

python3 --version
pip3 --version

pip3 install pytz
```

### Libsodium

```
git clone https://github.com/input-output-hk/libsodium
cd libsodium
git checkout 66f017f1
./autogen.sh
./configure
make
sudo make install
```

### cardano-node

The node does not need to run as a block producer as long as it has access to the correct `vrf.skey`.




## slotLeaderLogsConfig.json

An example for epoch 221, which needs the below epoch nonce.  
Add your pool id, the path to your pool vrf.skey, paths for both node configs. 
As well as a path to the current ledgerstate.json, or null to generate a new one on runtime.
libsodium must be build as always from: https://github.com/input-output-hk/libsodium
Make sure to have the node stats available as seen below (or put your own port).
The path to the cardano-cli could be a cardano-cli or ./cardano-cli depending on how you installed the cli previously.

```javscript
{
  "poolId":           "00000000000000000000000000000000000000000000000000000000",
  "vrfSkey":          "/path/to/vrf.skey",

  "genesisShelley":   "/path/to/mainnet-shelley-genesis.json",
  "genesisByron":     "/path/to/mainnet-byron-genesis.json",

  "ledgerState":      "/path/to/cardano-leader-logs/ledgerstate.json",

  "libsodiumBinary":  "/usr/local/lib/libsodium.so",
  "cardanoCLI":       "cardano-cli",
  "nodeStatsURL":     "http://127.0.0.1:12798/metrics",
  
  "timeZone":         "Europe/Berlin"
}
```

## retrieving the epoch nonce

```
cardano-cli query protocol-state --mainnet | jq -r .csTickn.ticknStateEpochNonce.contents

0022cfa563a5328c4fb5c8017121329e964c26ade5d167b1bd9b2ec967772b60
```


## run

Note: this method only works for current epoch block assignments. Calculating next epochs assignments based on next epoch's nonce is not supported.

```bash
node cardanoLeaderLogs.js path/to/slotLeaderLogsConfig.json epochNonceHash
```

Adam put up a service to retrieve the current and past epoch nonces.

eg.:
https://epoch-api.crypto2099.io:2096/epoch/236

## thanks

Thanks to Andrew Westberg [BCSH], Papacarp [LOVE] and others who contributed.

This repository was created by Marcel Baumberg [TITAN], now maintained by Damjan Ostrelic [EDEN].


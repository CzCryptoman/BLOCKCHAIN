# Axelar ![image](https://github.com/user-attachments/assets/51b07584-8e56-47d8-8f6c-d31bb6797ff3)


/
## Installment

Axelar delivers secure cross-chain communication for Web3, enabling you to build Interchain dApps that grow beyond a single chain. Secure means Axelar is built on proof-of-stake, the battle-tested approach used by Ethereum, Polygon, Cosmos, and more. Cross-chain communication means you can build a complete experience for your users that lets them interact with any asset, any application, on any chain with one click.

Chain Id: `axelar-dojo-1` | Current node binary version `v0.35.5`

## Guide

## Install the dependencies
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git tar wget aria2 clang lz4 pkg-config libssl-dev jq build-essential
```


## Install Go
```
sudo rm -rf /usr/local/go 
ver="1.22.4"
wget https://go.dev/dl/go$ver.linux-amd64.tar.gz -O - | sudo tar -xzf - -C /usr/local
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```


## Install Binary
```
cd $HOME
git clone 
cd axelar
git checkout v0.35.5 
```


### Build binary
```
make build
```

### Setup the folder for Cosmovisor
```
mkdir -p $HOME/.axelar/cosmovisor/genesis/bin
mv build/axelard $HOME/.axelar/cosmovisor/genesis/bin
```

### Create binary symlinks 
```
sudo ln -s $HOME/.axelar/cosmovisor/genesis $HOME/.axelar/cosmovisor/current -f
sudo ln -s $HOME/.axelar/cosmovisor/current/bin/axelard /usr/local/bin/axelard -f
```


### Download and install Cosmovisor
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
Create SystemD Service
sudo tee /etc/systemd/system/axelar.service > /dev/null << EOF
[Unit]
Description=axelar node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.axelar"
Environment="DAEMON_NAME=axelard"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="UNSAFE_SKIP_BACKUP=true"


[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable axelar.service
```


### Node Configuration
```
PORT=26657
MONIKER=YOUR NODE NAME
```



### Setup node configuration
```
axelard config chain-id axelar-dojo-1
axelard config keyring-backend os
axelard config node tcp://localhost:$PORT
```

### Initialize the node
```
axelard init $MONIKER --chain-id axelar-dojo-1
```

### Download genesis
```
wget -qO $HOME/.axelar/config/genesis.json https://public-snapshot-storage-mainnet.s3.ap-southeast-1.amazonaws.com/axelar/axelar-dojo-1/genesis.json
```

### Add seeds
```
sed -i.bak -e "s/^seeds *=.*/seeds = \"59df4b3832446cd0f9c369da01f2aa5fe9647248@135.181.220.61:15256\"/" $HOME/.axelar/config/config.toml
```

### Add peers
```
PEERS="546597f5a1635de1ff4f025e1b58f543cd55cfbf@157.90.129.237:26656,3ae54d00de3678562ad7cbeba117ecc7adc84a82@37.27.56.238:26656,3b4ea7101b147aa345fda68299bcfb57315b7d10@65.108.51.111:26656,a87b3a94e4dd7fb5467c200ebe72c88fed0cfaf6@49.12.134.188:28656,49762b752c9fd10e7eefa323ce53b0a731920520@138.201.36.98:26657,5ba72d2f93d4f2db519fa08bf49d014176d5369a@34.123.94.73:26656,133586120b9f5cf1c8608e843723897cdadbc422@18.217.111.172:26656,2f8308f82bf9cd9592361f0fc35676bd1b258557@15.235.87.236:26656,664e90d7939f3eaeb03c4353ddcf23fa4148ced1@85.237.192.20:26656,d1d9761737f3008bfa6e48f356ea4f40073ef4da@84.244.95.227:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.axelar/config/config.toml
```

### Set pruning
```
sed -i -e 's|^pruning *=.*|pruning = "custom"|' \
-e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
-e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
-e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
$HOME/.axelar/config/app.toml
```

### Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.007uaxl\"|" $HOME/.axelar/config/app.toml
```


## Download Snapshot file
```
wget -O lasted_snapshot.tar.lz4 https://public-snapshot-storage-mainnet.s3.amazonaws.com/axelar/axelar-dojo-1/snapshots/axelar_13691938.tar.lz4
sha256sum lasted_snapshot.tar.lz4 \\The result from checksum should be: f443a3cc68cc5021a16ea22d5120d385cf312fb37f8bd72a79371d7781dc8ad2
lz4 -c -d lasted_snapshot.tar.lz4  | tar -x -C $HOME/.axelar
```

## Start Service and Monitor Log
```
sudo systemctl start axelar.service && sudo journalctl -u axelar.service -f --no-hostname
```

```
https://services.contributiondao.com/mainnet/axelar/installation
```

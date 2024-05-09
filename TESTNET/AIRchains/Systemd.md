
# AIRchains

Run the node using systemd

## Chain ID: `junction`

## Recommended Hardware Requirements

|   SPEC      |       Minimum          |
| :---------: | :-----------------------:|
|   **CPU**   |        2 Cores           |
|   **RAM**   |        4 GB              |
| **Storage** |    100 GB SSD or Nvme    |

|   SPEC      |       Recommend          |
| :---------: | :-----------------------:|
|   **CPU**   |        4 Cores           |
|   **RAM**   |        8 GB              |
| **Storage** |    200 GB SSD or Nvme    |


### Update and install packages for compiling
Install required packages
```
sudo apt update && sudo apt upgrade -y
sudo apt install make curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```
Install Go
```
cd $HOME && \
ver="1.22.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
### Build binary
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
sudo mv junctiond /usr/local/bin
```

### Initialize the Node with the Moniker
Replace <moniker> with your own moniker
```
junctiond init <moniker> --chain-id=junction
```

### Download Genesis
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/genesis.json
cp genesis.json $HOME/.junction/config/genesis.json
```
### Download Addrbook file
```
curl -Ls https://support.synergynodes.com/addrbook/junction_testnet/addrbook.json > $HOME/.junction/config/addrbook.json
```

### Configure
```
SEEDS="de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656"
PEERS="de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.junction/config/config.toml
```
### Set Pruning, Enable Prometheus, Gas Price, and Indexer
```
PRUNING="custom"
PRUNING_KEEP_RECENT="100"
PRUNING_INTERVAL="19"
sed -i -e "s/^pruning *=.*/pruning = \"$PRUNING\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \
\"$PRUNING_KEEP_RECENT\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \
\"$PRUNING_INTERVAL\"/" $HOME/.junction/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.junction/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.junction/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.00025amf\"/" $HOME/.junction/config/app.toml
```
### Download the snapshot
```
wget -O junction_1604.tar.lz4 https://support.synergynodes.com/snapshots/junction_testnet/junction_1604.tar.lz4
```
### Decompress the snapshot
```
lz4 -c -d junction_1604.tar.lz4 | tar -x -C $HOME/.junction
```
### Create Service
```
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=Junctiond Daemon
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which junction) start --home $HOME/.junction
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable junctiond
```
OR:
```
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=Junctiond Daemon
#After=network.target
StartLimitInterval=350
StartLimitBurst=10

[Service]
Type=simple
User=node
ExecStart=/home/node/go/bin/junctiond start
Restart=on-abort
RestartSec=30

[Install]
WantedBy=multi-user.target

[Service]
LimitNOFILE=1048576
EOF
```

### Start service
```
sudo service junctiond start
```
### Start Node
```
junctiond start
```
### Check logs
```
sudo journalctl -fu junctiond
```

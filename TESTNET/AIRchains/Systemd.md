
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
PEERS=48887cbb310bb854d7f9da8d5687cbfca02b9968@35.200.245.190:26656,2d1ea4833843cc1433e3c44e69e297f357d2d8bd@35.203.171.169:26656,de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656,1918bd71bc764c71456d10483f754884223959a5@35.240.206.208:26656,ddd9aade8e12d72cc874263c8b854e579903d21c@34.165.28.145:26656,eb62523dfa0f9bd66a9b0c281382702c185ce1ee@38.242.145.138:26656,0305205b9c2c76557381ed71ac23244558a51099@162.55.65.162:26656,086d19f4d7542666c8b0cac703f78d4a8d4ec528@135.148.232.105:26656,3e5f3247d41d2c3ceeef0987f836e9b29068a3e9@168.119.31.198:56256,8b72b2f2e027f8a736e36b2350f6897a5e9bfeaa@131.153.232.69:26656,6a2f6a5cd2050f72704d6a9c8917a5bf0ed63b53@93.115.25.41:26656,e09fa8cc6b06b99d07560b6c33443023e6a3b9c6@65.21.131.187:26656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = "$PEERS"/" $HOME/.junction/config/config.toml
```

# Download the snapshot
```
wget -O junction_1604.tar.lz4 https://support.synergynodes.com/snapshots/junction_testnet/junction_1604.tar.lz4
```
# Decompress the snapshot
```
lz4 -c -d junction_1604.tar.lz4 | tar -x -C $HOME/.junction
```
# Create Service
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

# Start service
```
sudo service junctiond start
```
### Start Node
```
junctiond start
```
# Check logs
```
sudo journalctl -fu junctiond
```

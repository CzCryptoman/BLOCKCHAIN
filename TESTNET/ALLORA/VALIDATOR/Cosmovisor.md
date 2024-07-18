# Allora Network

# Installment
`allora`
The Allora Network is a state-of-the-art protocol that uses decentralized AI and machine learning (ML) to build and deploy predictions among its participants. It offers actors who wish to use AI predictions a formalized way to obtain the output of state-of-the-art ML models on-chain and to pay the operators of AI/ML nodes who create these predictions. Allora bridges the information gap between data owners, data processors, AI/ML predictors, market analysts, and the end-users or consumers who can execute these insights.

Chain Id: `edgenet` | Current node binary version `v0.2.7`

# Guide

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
git clone https://github.com/allora-network/allora-chain
cd allorad
git checkout v0.2.7 
```

### Build binary
```
make all
```
or
```
make build
```

### Setup the folder for Cosmovisor
```
mkdir -p $HOME/.allorad/cosmovisor/genesis/bin
mv build/allorad $HOME/.allorad/cosmovisor/genesis/bin
```

### Create binary symlinks 
```
sudo ln -s $HOME/.allorad/cosmovisor/genesis $HOME/.allorad/cosmovisor/current -f
sudo ln -s $HOME/.allorad/cosmovisor/current/bin/allorad /usr/local/bin/allorad -f
```

## Download and install Cosmovisor
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

## Create SystemD Service
```
sudo tee /etc/systemd/system/allora.service > /dev/null << EOF
[Unit]
Description=allora node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.allorad"
Environment="DAEMON_NAME=allorad"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="UNSAFE_SKIP_BACKUP=true"


[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable allora.service
```

## Node Configuration
```
PORT=26657
MONIKER=YOUR NODE NAME
```

### Setup node configuration
```
allorad config chain-id edgenet
allorad config keyring-backend os
allorad config node tcp://localhost:$PORT
```

### Initialize the node
```
allorad init $MONIKER --chain-id edgenet
```

### Download genesis
```
wget -qO $HOME/.allorad/config/genesis.json https://public-snapshot-storage-develop.s3.ap-southeast-1.amazonaws.com/allora/edgenet/genesis.json
```

### Add seeds
```
sed -i.bak -e "s/^seeds *=.*/seeds = \"59df4b3832446cd0f9c369da01f2aa5fe9647248@162.55.65.137:26856\"/" $HOME/.allorad/config/config.toml
```

### Add peers
```
PEERS="f8dcdb9696bada028ef945ccb0b536e9e24afc49@142.132.209.236:26756,58ac43b166c4e450cf51daf4d7d061eed75cc7a7@85.215.54.231:26656,b2c78fafe1836132209fea2461ec9becd267987b@37.252.186.221:26656,6bff559c9134e6833dbad6aa4084aa8b518dd07a@35.228.176.202:26656,6479f0d0d265faca134befe5c547e0dfe8f959e6@171.7.36.63:26656,7d9a0772fbe927e4854ce41df111fa8e80bbe6a0@94.72.114.113:26656,3b751018454285d95c87fbbe8bfa971b9ab1236c@45.61.161.241:26656,7cea2cd1d4a991a208bd35b79ed1506523c3f7b0@62.169.31.141:26656,95e0eeedd8513c8e19a04e277c9af2780e4c91a1@109.199.99.67:26656,1d34d5b4fb7da9d1487b0bf06c440ea91ad50ba8@82.208.21.115:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.allorad/config/config.toml
```

### Set pruning
```
sed -i -e 's|^pruning *=.*|pruning = "custom"|' \
-e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
-e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
-e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
$HOME/.allorad/config/app.toml
```

### Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0uallo\"|" $HOME/.allorad/config/app.toml
```

## Download Snapshot file
```
wget -O lasted_snapshot.tar.lz4 https://public-snapshot-storage-develop.s3.amazonaws.com/allora/edgenet/snapshots/allora_756401.tar.lz4
sha256sum lasted_snapshot.tar.lz4 \\The result from checksum should be: ae7742c52ac2271f84907e5149a30e65ba6fd69344562a26b71c86053a39f7cf
lz4 -c -d lasted_snapshot.tar.lz4  | tar -x -C $HOME/.allorad
```

## Start Service and Monitor Log
```
sudo systemctl start allora.service && sudo journalctl -u allora.service -f --no-hostname
```

# END

`https://services.contributiondao.com/testnet/allora/installation`

# ARCHWAY

# Installation

Chain ID: archway-1 | Current Node Version: v7.0.0

### Install dependencies
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade

### Install GO
```
ver="1.21.3" &&
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" &&
sudo rm -rf /usr/local/go &&
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" &&
rm "go$ver.linux-amd64.tar.gz" &&
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile &&
source $HOME/.bash_profile &&
go version
```

# Install with Cosmovisor
ADVANCED ROUTE
Cosmosvisor is a process manager for Cosmos SDK application binaries that monitors the governance module for incoming chain upgrade proposals. If a proposal is approved, cosmosvisor can automatically download the new binary, stop the current one, switch to the new binary, and restart the node with the new binary.

### Download and build binaries

Clone Archway repo and build archwayd `v7.0.0`
```
cd $HOME
git clone https://github.com/archway-network.git
cd archway-network
git checkout v7.0.0
```

### Build binaries
```
make install
```

### Prepare binaries for Cosmovisor
```
cd $HOME
mkdir -p ~/.archwayd/cosmovisor/upgrades/v7.0.0/bin
mv $HOME/go/bin/archwayd ~/.archwayd/cosmovisor/upgrades/v7.0.0/bin/
```

### Create symlinks
```
sudo ln -s ~/.archwayd/cosmovisor/genesis ~/.archwayd/cosmovisor/current -f
sudo ln -s ~/.archwayd/cosmovisor/current/bin/archwayd /usr/local/bin/archwayd -f
```

### Download and install Cosmovisor
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

## Run node

### Create service
```
sudo tee /etc/systemd/system/archwayd.service > /dev/null << EOF
[Unit]
Description=archway node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=~/.archwayd"
Environment="DAEMON_NAME=archwayd"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:~/.archwayd/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```

# Install without Cosmovisor

### Download and build binaries

Clone Archway repo and build archwayd `v7.0.0`
```
cd $HOME
git clone https://github.com/archway-network.git
cd archway-network
git checkout v7.0.0
```

### Build binaries
```
make install
```

## Run node

### Create service
```
sudo tee /etc/systemd/system/archwayd.service > /dev/null << EOF
[Unit]
Description=archway node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which archwayd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"

[Install]
WantedBy=multi-user.target
EOF
```

### Enable service
```
sudo systemctl daemon-reload
sudo systemctl enable archwayd
```
## Node configuration

### Set config
```
archwayd config chain-id archway-1
archwayd config keyring-backend os
archwayd config node tcp://localhost:26657
```

### Initialize the node
```
archwayd init NAME_OF_YOUR_VALIDATOR --chain-id archway-1
```

### Download genesis and addrbook
```
curl https://config.noders.services/archway/genesis.json -o ~/.archwayd/config/genesis.json
curl https://config.noders.services/archway/addrbook.json -o ~/.archwayd/config/addrbook.json
```



### Add peers
```
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"c4b36b605667e3896eb6f57c5d731519b89dfc6f@archway-rpc.noders.services:13656\"/" ~/.archwayd/config/config.toml
```


### Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.001aarch\"|" ~/.archwayd/config/app.toml
```


### Set pruning
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  ~/.archwayd/config/app.toml
```

### Set custom ports
```
echo "export archwayd_PORT="SET_YOUR_PORT"" >> $HOME/.bash_profile
```

### Set custom ports in `app.toml`
```
sed -i.bak -e "s%:1317%:${archwayd_PORT}317%g" \
-e "s%:8080%:${archwayd_PORT}080%g" \
-e "s%:9090%:${archwayd_PORT}090%g" \
-e "s%:9091%:${archwayd_PORT}091%g" \
-e "s%:8545%:${archwayd_PORT}545%g" \
-e "s%:8546%:${archwayd_PORT}546%g" \
-e "s%:6065%:${archwayd_PORT}065%g" ~/.archwayd/config/app.toml
```

### Set custom ports in config.toml file
```
sed -i.bak -e "s%:26658%:${archwayd_PORT}658%g" \
-e "s%:26657%:${archwayd_PORT}657%g" \
-e "s%:6060%:${archwayd_PORT}060%g" \
-e "s%:26656%:${archwayd_PORT}656%g" \
-e "s%:26660%:${archwayd_PORT}660%g" ~/.archwayd/config/config.toml
```

### Start node and check logs
```
sudo systemctl start archwayd && sudo journalctl -u archwayd -f --no-hostname -o cat
```

# END

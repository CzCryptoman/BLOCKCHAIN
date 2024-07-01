# Guide Installation Hedgeblock
Binary Info


app name: hedged

version:

cosmos sdk version:

git commit:

### Update and Install dependencies:
```
sudo apt update
sudo apt upgrade -y
sudo apt install git build-essential curl jq -y
```

### Installing Go:
```
ver="1.22.3"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
```
```
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```
go version
```

`go version 1.22.3 ...`

### Install cosmovisor v1.5.0 ( recommended ):
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

### Install app:
```
cd $HOME
wget -O hedged https://github.com/hedgeblock/testnets/releases/download/v0.1.0/hedged_linux_amd64_v0.1.0 &&\
chmod +x hedged &&\
if [ ! -d "$HOME/go/bin" ]; then
  mkdir -p $HOME/go/bin
fi
mv $HOME/hedged $HOME/go/bin
```

### Initialize node:
change `"NODE_NAME"` according to your moniker / node name
```
hedged init NODE_NAME --chain-id berberis-1 --home $HOME/.hedge
```

### Config node ( optional ):
this will change your `client.toml` value
```
hedged config chain-id berberis-1
hedged config keyring-backend test
hedged config node tcp://localhost:26657
```

### Configure cosmovisor:
```
cd $HOME
export DAEMON_NAME=hedged
export DAEMON_HOME=$HOME/.hedge
cosmovisor init $(which hedged)
```

### Download genesis and addrbook file:
```
wget -O $HOME/.hedge/config/genesis.json "https://snapshot-de-1.genznodes.dev/hedgeblock/genesis.json"
wget -O $HOME/.hedge/config/addrbook.json "https://snapshot-de-1.genznodes.dev/hedgeblock/addrbook.json"
```

### Using our node snapshot / statesync ( if needed )
See detail in page
snapshot page
statesync page

### Set service files:
```
sudo tee /etc/systemd/system/hedged.service > /dev/null <<EOF
[Unit]
Description="Hedgeblock daemon node"
After=network-online.target

[Service]
User=$USER
Environment="DAEMON_NAME=hedged"
Environment="DAEMON_HOME=$HOME/.hedge"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="DAEMON_PREUPGRADE_MAX_RETRIES=0"
Environment="DAEMON_LOG_BUFFER_SIZE=512"
ExecStart=$(which cosmovisor) run start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### Launch nodes:
```
sudo systemctl daemon-reload
sudo systemctl enable hedged
sudo systemctl start hedged
journalctl -fu hedged -o cat
```

# Troubleshooting
### 1. Error: `binary not present`

If you see logs like this, pay attention to `[UPGRADE_NAME]` :
`Error: binary not present`
```
stat $HOME/.hedge/cosmovisor/upgrades/[UPGRADE_NAME]/bin/hedged: no such file or directory"
hedged.service: Main process exited, code=exited, status=1/FAILURE
hedged.service: Failed with result 'exit-code'.
```
### Fix with:
Make sure you change [UPGRADE_NAME] according to your logs
```
export DAEMON_NAME=hedged
export DAEMON_HOME=$HOME/.hedge
cosmovisor add-upgrade [UPGRADE_NAME] $(which hedged)
```
### Check node logs:
```
journalctl -fu hedged -o cat
```

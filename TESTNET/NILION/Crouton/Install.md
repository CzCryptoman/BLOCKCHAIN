# NILION

Public   Endpoints

RPC:  `https://nillion-testnet-rpc.crouton.digital`

API: `https://nillion-testnet-api.crouton.digital`

gRPC: `nillion-testnet-api.crouton.digital:28590`

peer: `f3775e73d5e9b30d777caefeb9b7b69ed0b1bfe2@65.109.93.124:28556`

## Prepare

### Update and install packages
```
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y
```


### Installing Go v1.22.0


GO_VERSION: `1.22.0`
```
cd $HOME && \ 
wget "https://golang.org/dl/go1.22.0.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go1.22.0.linux-amd64.tar.gz" && \
rm "go1.22.0.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## Installing nillion binary

### Building from Source
```
git clone https://github.com/NillionNetwork/nilchain nillion 
cd nillion 
git checkout v0.2.1 
make install
nilchaind version --long | grep -e version -e commit
```

`version: v0.2.1`

`commit: 179cfef832447f2cb681c996cf9e87387f4d4d9f`


### Download Prebuilt Binary
```
wget https://storage.crouton.digital/testnet/nillion/bin/nilchaind
chmod +x nilchaind
mv nilchaind $HOME/go/bin
nilchaind version --long | grep -e version -e commit
# version: v0.2.1
# commit: 179cfef832447f2cb681c996cf9e87387f4d4d9f
````

## Initialization


MONIKER: `VALIDATOR_NAME`
```
nilchaind init VALIDATOR_NAME --chain-id nillion-chain-testnet-1 && \
nilchaind config set client chain-id nillion-chain-testnet-1 && \
nilchaind config set client keyring-backend test
```

### Download genesis
```
wget https://storage.crouton.digital/testnet/nillion/files/genesis.json -O $HOME/.nillionapp/config/genesis.json
```

### Download addrbook
```
wget https://storage.crouton.digital/testnet/nillion/files/addrbook.json -O $HOME/.nillionapp/config/addrbook.json
```

## Configuration
Change ports (Optional)


NODE-NUMBER: `1`

```
EXTERNAL_IP=$(wget -qO- eth0.me)

sed -i.bak \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((1 + 266))58\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((1 + 266))57\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$((1 + 60))60\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((1 + 266))56\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$((1 + 266))56\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$((1 + 266))56\"/}" \
    -e "s/\(prometheus_listen_addr = \":\)\([0-9]*\).*/\1$((1 + 266))60\"/"                            $HOME/.nillionapp/config/config.toml

sed -i.bak \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 13))17\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 90))90\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 90))91\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 85))45\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(ws-address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 85))46\4/}"  $HOME/.nillionapp/config/app.toml 
    
echo "export NODE=http://localhost:$((1+266))57" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
nilchaind config set client node $NODE
```


### Web Services (Optional)


RPC-OPEN: `127.0.0.1`

API-ENABLE: `false`

gRPC-ENABLE: `false`

gRPC-WEB-ENABLE: `false`

JSON-RPC-ENABLE: `false`

GAS-PRICE: `0.001`

```
sed -i.bak -e "/^\[rpc\]/,/^laddr =/s|laddr = \"tcp://127\.0\.0\.1:|laddr = \"tcp://127.0.0.1:|"        $HOME/.nillionapp/config/config.toml
sed -i.bak -e "/^\[api\]/,/^enable/s|^enable *=.*|enable = 'false'|"                                    $HOME/.nillionapp/config/app.toml
sed -i.bak -e "/^\[grpc\]/,/^enable/s|^enable *=.*|enable = 'false'|"                                   $HOME/.nillionapp/config/app.toml
sed -i.bak -e "/^\[grpc-web\]/,/^enable/s|^enable *=.*|enable = 'false'|"                               $HOME/.nillionapp/config/app.toml
sed -i.bak -e "/^\[json-rpc\]/,/^enable/s|^enable *=.*|enable = 'false'|"                               $HOME/.nillionapp/config/app.toml
sed -i.bak -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = '0.001unil'|"                           $HOME/.nillionapp/config/app.toml
```

### Pruning (Optional)


INDEXER-ENABLE: `null`

PRUNING: `custom`

PRUNING-KEEP-RECENT: `100`

PRUNING-KEEP-EVERY: `0`

PRUNING-INTERVAL: `17`

SNAPSHOT-INTERVAL: `100`

```
sed -i.bak -e "s/^indexer *=.*/indexer = \"null\"/"                                                     $HOME/.nillionapp/config/config.toml
sed -i.bak -e "s/^pruning *=.*/pruning = \"custom\"/"                                                   $HOME/.nillionapp/config/app.toml
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/"                              $HOME/.nillionapp/config/app.toml
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"0\"/"                                  $HOME/.nillionapp/config/app.toml 
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"17\"/"                                     $HOME/.nillionapp/config/app.toml
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"100\"/"                                  $HOME/.nillionapp/config/app.toml
```

### Create a service file
```
sudo tee /etc/systemd/system/nilchaind.service > /dev/null <<EOF
[Unit]
Description=nillion_node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which nilchaind) start --home $HOME/.nillionapp
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### Start the node
```
sudo systemctl daemon-reload && \
sudo systemctl enable nilchaind && \
sudo systemctl restart nilchaind && \
sudo journalctl -u nilchaind -f -o cat
```

## Recover

### Snapshot
```
sudo apt install lz4 -y
```
```
sudo systemctl stop nilchaind
```
```
cp $HOME/.nillionapp/data/priv_validator_state.json $HOME/.nillionapp/priv_validator_state.json.backup
```
```
rm -rf $HOME/.nillionapp/data 
```

```
curl https://storage.crouton.digital/testnet/nillion/snapshots/nillion_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.nillionapp
```
```
mv $HOME/.nillionapp/priv_validator_state.json.backup $HOME/.nillionapp/data/priv_validator_state.json
```
```
sudo systemctl restart nilchaind && sudo journalctl -u nilchaind -f
```

### State Sync
```
SNAP_RPC="https://nillion-testnet-rpc.crouton.digital:443"
```
```
sudo systemctl stop nilchaind
```
```
cp $HOME/.nillionapp/data/priv_validator_state.json $HOME/.nillionapp/priv_validator_state.json.backup
nilchaind tendermint unsafe-reset-all --home $HOME/.nillionapp --keep-addr-book 
```
```
peers="7db51040f340be9e92579c06ec2878770a2cb2b4@65.109.93.124:25756"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nillionapp/config/config.toml 
```

```
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 
```

```
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2
```

```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.nillionapp/config/config.toml
```

```
mv $HOME/.nillionapp/priv_validator_state.json.backup $HOME/.nillionapp/data/priv_validator_state.json
```

```
sudo systemctl restart nilchaind && sudo journalctl -u nilchaind -f
```

### Upgrade Node

APP_VERSION: `v0.2.15`

```
git clone https://github.com/NillionNetwork/nilchain nillion 
cd $HOME 
rm -rf nillion 
git clone https://github.com/NillionNetwork/nilchain nillion 
cd nillion / 
git checkout tags/v0.2.15 -b v0.2.15
make install
sudo systemctl restart nilchaind && sudo journalctl -u nilchaind -f
```

### Delete Node
```
sudo systemctl stop nilchaind
sudo systemctl disable nilchaind
sudo rm -rf /etc/systemd/system/nilchaind.service
sudo rm $(which nilchaind)
sudo rm -rf $HOME/.nillionapp
```

## Key management


WALLET: `wallet`

### Add New Wallet

This command generates a new wallet with a unique keypair.

Use this to create a secure, brand-new wallet for transactions.
```
nilchaind keys add wallet 
```

### Recover Wallet
```
# This command restores a wallet using a mnemonic phrase.
# Ideal for accessing a previously created wallet on a new device.
nilchaind keys add wallet --recover
``` 


### Delete Wallet
```
# This command removes a wallet from the node's key management system.
# Ensure you have backed up the key before deleting, as this action is irreversible.
nilchaind keys delete wallet
```


### List All Wallets
```
# This command lists all the wallets managed by the node.
# Useful for getting an overview of all available wallets and their statuses.
nilchaind keys list
``` 

### Export Key
```
# This command exports the wallet's private key to a file.
# Use this to backup your wallet's key securely.
nilchaind keys export wallet
``` 

### Import Key
```
# This command imports a wallet key from a backup file.
# Essential for restoring wallet access from a previously exported key file.
nilchaind keys import wallet  wallet.backup
```


## Validator management

MONIKER: `VALIDATOR_NAME`
 
IDENTITY:
 
WEBSITE:
 
DETAILS: 


WALLET: `wallet`

COMISSION-RATE: `0.1`

MAX-RATE: `0.2`

CHANGE-RATE: `0.01`

### Create Validator
```
nilchaind tx mstaking create-validator \
--amount 1000000unil \
--moniker "VALIDATOR_NAME" \
--identity "" \
--website "" \
--details "" \
--from wallet \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--pubkey $(nilchaind tendermint show-validator) \
--chain-id nillion-chain-testnet-1 \
--gas 2000000 \
--fees 300000unil \
-y 
```

### Edit Validator
```
nilchaind tx mstaking edit-validator \
--moniker "VALIDATOR_NAME" \
--identity "" \
--website "" \
--details "" \
--from wallet \
--commission-rate 0.1 \
--chain-id nillion-chain-testnet-1 \
--gas auto --fees 80000unil \
-y 
```


WALLET: `wallet`

### Validator Details

Retrieves comprehensive information regarding the validator.

This command is utilized to fetch details about the current validator, including its address, public key, and associated data.
```
nilchaind q mstaking validator $(nilchaind keys show wallet --bech val -a) 
```

### Signing Info
```
# Fetches information about the validator's signing behavior.
# This command extracts details concerning the signing behavior associated with the current validator, including missed block counts and other security-related parameters.
nilchaind q slashing signing-info $(nilchaind tendermint show-validator) 
``` 

### Slashing Parameters
```
# Retrieves parameters pertinent to slashing.
# This command extracts the current parameters governing slashing, such as threshold values for penalties and punishment durations.
nilchaind q slashing params 
``` 

### Check Key
```
# Validates the status of your key.
# This command compares the public key of your validator with the key associated with your node to ensure that the key is in an up-to-date state.
[[ $(nilchaind q mstaking validator $(nilchaind keys show wallet  --bech val -a) -oj | jq -r .consensus_pubkey.key) == $(nilchaind status | jq -r .ValidatorInfo.PubKey.value) ]]
echo -e "Your key status is ok" || echo -e "Your key status is error"
``` 

### Jailing Info
```
# Retrieves information about validator penalties.
# This command fetches details about current penalties imposed on the validator, including information about blocks where signatures were missed.
nilchaind q slashing signing-info $(nilchaind tendermint show-validator) 
``` 

### Active Set
```
# Obtains details of the active set of validators.
# This command retrieves information about all validators currently in the active set, their current statuses, and other associated parameters.
nilchaind q mstaking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
``` 

### Inactive Set
```
# Obtains details of the inactive set of validators.
# This command fetches information about all validators currently in the inactive set, their current statuses, and other associated parameters.
nilchaind q mstaking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
``` 





## 🚨 Maintenance

### Get validator info
```
nilchaind status 2>&1 | jq .ValidatorInfo
```

### Get sync info
```
nilchaind status 2>&1 | jq .SyncInfo
```

### Get node peer
```
echo $(nilchaind tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat .nillionapp/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Check if validator key is correct
```
[[ $(nilchaind q staking validator $(nilchaind keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(nilchaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get live peers
```
curl -sS http://localhost:24757/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

### Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"\"/" .nillionapp/config/app.toml
```

### Enable prometheus
```
sed -i -e "s/prometheus = false/prometheus = true/" .nillionapp/config/config.toml
```

### Reset chain data
```
nilchaind tendermint unsafe-reset-all --home .nillionapp --keep-addr-book
```

## Onchain

WALLET: `wallet`

### Withdraw Rewards


Withdraw all accumulated rewards from your account.

This command retrieves and withdraws all rewards earned from staking.
```
nilchaind tx distribution withdraw-all-rewards --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment=1.15 --gas auto --gas-prices 80000unil -y
```

### Withdraw Rewards & Commission
```
# Withdraw rewards and commission earned from your validator.
# Use this command to claim both validator rewards and commission earnings.
nilchaind tx distribution withdraw-rewards  --from wallet --commission --chain-id nillion-chain-testnet-1 --gas-adjustment=1.15 --gas auto 80000unil -y 
``` 

### Check Balance
```
# Check the balance of your account.
# This command displays the current balance of your account.
nilchaind query bank balances $WALLET_ADDRESS
``` 

### Delegate to Self
```
# Delegate tokens from your account to yourself as a validator.
# This command allows you to stake tokens on your own validator node.
nilchaind tx staking delegate $(nilchaind keys show wallet --bech val -a) 1000000unil --from wallet --chain-id nillion-chain-testnet-1 --gas-adjustment=1.15 --gas auto --gas-prices 80000unil -y 
``` 

### Delegate to Other
```

```
### Redelegate Stake
```

```
### Unbond Tokens
```

```
### Transfer Funds
```

```
# END






# COMMAND
![image](https://github.com/user-attachments/assets/64cef768-5304-4981-9bc0-f4dd7426cd84)


## 🔑 Key management

### ADD NEW KEY
```
nolusd keys add wallet
```

### RECOVER EXISTING KEY
```
nolusd keys add wallet --recover
```

### LIST ALL KEYS
```
nolusd keys list
```

### DELETE KEY
```
nolusd keys delete wallet
```

### EXPORT KEY TO A FILE
```
nolusd keys export wallet
```

### IMPORT KEY FROM A FILE
```
nolusd keys import wallet wallet.backup
```

### QUERY WALLET BALANCE
```
nolusd q bank balances $(nolusd keys show wallet -a)
```
## 👷 Validator management
Please make sure you have adjusted moniker, identity, details and website to match your values.

### CREATE NEW VALIDATOR
```
nolusd tx staking create-validator \
--amount 1000000unls \
--pubkey $(nolusd tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id pirin-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--fees 2500unls \
-y
```

### EDIT EXISTING VALIDATOR
```
nolusd tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id pirin-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--fees 2500unls \
-y
```

### UNJAIL VALIDATOR
```
nolusd tx slashing unjail --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### JAIL REASON
```
nolusd query slashing signing-info $(nolusd tendermint show-validator)
```

### LIST ALL ACTIVE VALIDATORS
```
nolusd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### LIST ALL INACTIVE VALIDATORS
```
nolusd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### VIEW VALIDATOR DETAILS
```
nolusd q staking validator $(nolusd keys show wallet --bech val -a)
```

## 💲 Token management
### WITHDRAW REWARDS FROM ALL VALIDATORS
```
nolusd tx distribution withdraw-all-rewards --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR
```
nolusd tx distribution withdraw-rewards $(nolusd keys show wallet --bech val -a) --commission --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### DELEGATE TOKENS TO YOURSELF
```
nolusd tx staking delegate $(nolusd keys show wallet --bech val -a) 1000000unls --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### DELEGATE TOKENS TO VALIDATOR
```
nolusd tx staking delegate <TO_VALOPER_ADDRESS> 1000000unls --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### REDELEGATE TOKENS TO ANOTHER VALIDATOR
```
nolusd tx staking redelegate $(nolusd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000unls --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### UNBOND TOKENS FROM YOUR VALIDATOR
```
nolusd tx staking unbond $(nolusd keys show wallet --bech val -a) 1000000unls --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### SEND TOKENS TO THE WALLET
```
nolusd tx bank send wallet <TO_WALLET_ADDRESS> 1000000unls --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

## 🗳 Governance
### LIST ALL PROPOSALS
```
nolusd query gov proposals
```

### VIEW PROPOSAL BY ID
```
nolusd query gov proposal 1
```

### VOTE ‘YES’
```
nolusd tx gov vote 1 yes --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### VOTE ‘NO’
```
nolusd tx gov vote 1 no --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### VOTE ‘ABSTAIN’
```
nolusd tx gov vote 1 abstain --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

### VOTE ‘NOWITHVETO’
```
nolusd tx gov vote 1 NoWithVeto --from wallet --chain-id pirin-1 --gas-adjustment 1.4 --gas auto --fees 2500unls -y
```

## ⚡️ Utility
### UPDATE PORTS
```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.nolus/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.nolus/config/app.toml
```

### UPDATE INDEXER

Disable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.nolus/config/config.toml
```

Enable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.nolus/config/config.toml
```
### UPDATE PRUNING
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.nolus/config/app.toml
```

## 🚨 Maintenance
### GET VALIDATOR INFO
```
nolusd status 2>&1 | jq .ValidatorInfo
```

### GET SYNC INFO
```
nolusd status 2>&1 | jq .SyncInfo
```

### GET NODE PEER
```
echo $(nolusd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.nolus/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### CHECK IF VALIDATOR KEY IS CORRECT
```
[[ $(nolusd q staking validator $(nolusd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(nolusd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### GET LIVE PEERS
```
curl -sS http://localhost:14357/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

### SET MINIMUM GAS PRICE
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025unls\"/" $HOME/.nolus/config/app.toml
```

### ENABLE PROMETHEUS
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.nolus/config/config.toml
```

### RESET CHAIN DATA
```
nolusd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.nolus --keep-addr-book
```

### REMOVE NODE
Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your priv_validator_key.json!
```
cd $HOME
sudo systemctl stop nolus.service
sudo systemctl disable nolus.service
sudo rm /etc/systemd/system/nolus.service
sudo systemctl daemon-reload
rm -f $(which nolusd)
rm -rf $HOME/.nolus
rm -rf $HOME/nolus-core
```

## ⚙️ Service Management
### RELOAD SERVICE CONFIGURATION
```
sudo systemctl daemon-reload
```

### ENABLE SERVICE
```
sudo systemctl enable nolus.service
```

### DISABLE SERVICE
```
sudo systemctl disable nolus.service
```

### START SERVICE
```
sudo systemctl start nolus.service
```

### STOP SERVICE
```
sudo systemctl stop nolus.service
```

### RESTART SERVICE
```
sudo systemctl restart nolus.service
```

### CHECK SERVICE STATUS
```
sudo systemctl status nolus.service
```

### CHECK SERVICE LOGS
```
sudo journalctl -u nolus.service -f --no-hostname -o cat
```


# END

# CLI Cheatsheet

Chain ID: `archway-1` | Current Node Version: `v7.0.0`

This cheatsheet collects commonly used `CLI commands` for node operators to easily copy and paste. A few conventions we follow:

- Capitalized words indicate placeholders

- Always use our own `[NODERS]TEAM RPC endpoints`

- Always specify `--chain-id` and `--node` flags even when they are unnecessary

- Query CLI command always uses `--output json` flag and pipes result through `jq`


## Wallet generate and recover

### Add new key
```
archwayd keys add KEY
```

### Recover key (via existing mnemonic)
```
archwayd keys add KEY --recover
```

### List all keys
```
archwayd keys list
```

### Delete key
```
archwayd keys delete KEY
```

## Wallet

### Wallet balance
```
archwayd q bank balances $(archwayd keys show KEY -a) --node https://archway-rpc.noders.services:443
```

### Send
```
archwayd tx bank send YOUR_KEY RECEIVER_ADDRESS 1000000aarch \
  --chain-id archway-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --from KEY
```

### Withdraw rewards from all validators

```
archwayd tx distribution withdraw-all-rewards \
  --chain-id archway-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --from KEY
```

### Withdraw Rewards including Commission
```
archwayd tx distribution withdraw-rewards VALIDATOR_ADRESS \
  --commission \
  --chain-id archway-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --from KEY
```

### Delegate tokens to yourself
```
archwayd tx staking delegate $(archwayd keys show KEY --bech val -a) 1000000aarch \
--chain-id archway-1 \
--node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
--from KEY
```

### Delegate tokens to validator
```
archwayd tx staking delegate VALIDATOR_ADDRESS 1000000aarch \
--chain-id archway-1 \
--node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
--from KEY
```

### Redelegate tokens to another validator
```
archwayd tx staking redelegate $(archwayd keys show KEY --bech val -a) VALIDATOR_ADDRESS 1000000aarch \
  --chain-id archway-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --from KEY
```


### Unbond tokens from your validator
```
archwayd tx staking unbond $(archwayd keys show KEY --bech val -a) aarch \
  --chain-id andromeda-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --from KEY
```

## Governance

### List of all proposals
```
archwayd query gov proposals --node https://archway-rpc.noders.services:443
```

### Check vote
```
archwayd query gov proposal PROPOSAL_NUMBER \
  --chain-id archway-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --output json | jq
```

## Vote

### Vote options:
yes
no
no_with_veto
abstain

```
archwayd tx gov vote PROPOSAL_NUMBER VOTE_OPTION \
  --chain-id archway-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --from KEY
```

## Validator management

### Create Validator

`NOTE`: We use example filed values instead of capitalized dummy words for demo purpose in this command. Please make sure to adjust accordingly for your use.

```
archwayd tx staking create-validator \
  --amount 1000000aarch \
  --commission-max-change-rate "0.05" \
  --commission-max-rate "0.10" \
  --commission-rate "0.05" \
  --min-self-delegation "1" \
  --pubkey=$(archwayd tendermint show-validator) \
  --moniker '[NODERS]TEAM SERVICE' \
  --website "https://noders.team" \
  --identity "220491ADDD660741" \
  --details "Trusted blockchain validator and web3 developer team" \
  --security-contact="office@noders.team" \
  --chain-id archway-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --from KEY
```

### Edit validator
```
archwayd tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id archway-1 \
--commission-rate 0.05 \
--from KEY \
--node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
```

### Unjail
```
archwayd tx slashing unjail \
  --chain-id archway-1 \
  --node https://archway-rpc.noders.services:443 --fees 3000000000aarch \
  --from KEY
```

### Jail reason
```
archwayd query slashing signing-info $(archwayd tendermint show-validator)
```

### Validator details
```
archwayd q staking validator $(archwayd keys show KEY --bech val -a)
```

## Maintenance

### Get validator info
```
archwayd status 2>&1 | jq .ValidatorInfo
```

### Get sync info
```
archwayd status 2>&1 | jq .SyncInfo
```

### Get node peer
```
echo $(archwayd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat ~/.archwayd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```


### Check if validator key is correct
```
[[ $(archwayd q staking validator $(archwayd keys show KEY --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(archwayd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```


### Get live peers
```
curl -sS https://archway-rpc.noders.services:443/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```


### Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.001aarch\"/" ~/.archwayd/config/app.toml
```


### Enable prometheus

```
sed -i -e "s/prometheus = false/prometheus = true/" ~/.archwayd/config/config.toml
```

### Reset chain data
```
archwayd tendermint unsafe-reset-all --keep-addr-book --home ~/.archwayd
```

## Service Management

### Reload service configuration
```
sudo systemctl daemon-reload
```

### Enable service
```
sudo systemctl enable archwayd
```

### Disable service
```
sudo systemctl disable archwayd
```

### Start service
```
sudo systemctl start archwayd
```

### Stop service
```
sudo systemctl stop archwayd
```

### Restart service
```
sudo systemctl restart archwayd
```

### Check service status
```
sudo systemctl status archwayd
```

### Check service logs
```
sudo journalctl -u archwayd -f --no-hostname -o cat
```

### Remove node
```
cd $HOME
sudo systemctl stop archwayd
sudo systemctl disable archwayd
sudo rm /etc/systemd/system/archway.service
sudo systemctl daemon-reload
rm -f $(which archwayd)
rm -rf ~/.archwayd
rm -rf $HOME/archway-network
```

# END

`https://noders.services/mainnet-networks/archway/cli-cheatsheet`

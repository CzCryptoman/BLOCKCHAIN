# Nillion ![image](https://github.com/user-attachments/assets/7204947d-7577-4877-800d-7b4dbbe87596)


# SNAPSHOT

description: Catch the latest block faster by using our daily snapshots.


Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically every `12 hours`

pruning: `107/0/13` | indexer: `null` | version tag: `v0.2.1`

BLOCK	: `2494238` - AGE: `4 hours` - DOWNLOAD: `snapshot (11.13 GB)`


## Instructions

### Quick setup
One liner to get moving quickly

```
sudo systemctl stop nilchaind
cp .nillionapp/data/priv_validator_state.json .nillionapp/priv_validator_state.json.backup
rm -rf .nillionapp/data
curl -L https://snapshots.lavenderfive.com/testnet-snapshots/nillion/nillion_2494238.tar.lz4 | tar -Ilz4 -xf - -C .nillionapp
mv .nillionapp/priv_validator_state.json.backup .nillionapp/data/priv_validator_state.json
sudo systemctl start nilchaind && sudo journalctl -u nilchaind -f --no-hostname -o cat
```

## STEP by STEP:

### Stop nilchaind and reset the data
```
sudo systemctl stop nilchaind
cp .nillionapp/data/priv_validator_state.json .nillionapp/priv_validator_state.json.backup
rm -rf .nillionapp/data
```


### Download latest snapshot
```
curl -L https://snapshots.lavenderfive.com/testnet-snapshots/nillion/nillion_2494238.tar.lz4 | tar -Ilz4 -xf - -C .nillionapp
mv .nillionapp/priv_validator_state.json.backup .nillionapp/data/priv_validator_state.json
```

### Restart nilchaind and check the log
```
sudo systemctl start nilchaind && sudo journalctl -u nilchaind -f --no-hostname -o cat
```

# END.
`https://services.lavenderfive.com/testnet/nillion/snapshot`

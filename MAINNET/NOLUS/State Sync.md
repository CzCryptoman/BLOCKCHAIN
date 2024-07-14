![image](https://github.com/user-attachments/assets/ee931ba7-876b-44a3-a408-18ad37ef6f7b)

# State Sync

State Sync allows a new node to join the network by fetching a snapshot of the application state at a recent height instead of fetching and replaying all historical blocks. Since the application state is generally much smaller than the blocks, and restoring it is much faster than replaying blocks, this can reduce the time to sync with the network from days to minutes.

## Stop the service and reset the data
```
sudo systemctl stop nolus.service
cp $HOME/.nolus/data/priv_validator_state.json $HOME/.nolus/priv_validator_state.json.backup
nolusd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.nolus
```

## Get and configure the state sync information
```
STATE_SYNC_RPC=https://nolus.rpc.kjnodes.com:443
STATE_SYNC_PEER=d9bfa29e0cf9c4ce0cc9c26d98e5d97228f93b0b@nolus.rpc.kjnodes.com:14356
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 1000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```

```
sed -i \
  -e "s|^enable *=.*|enable = true|" \
  -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
  -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
  -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
  -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
  $HOME/.nolus/config/config.toml
```

```
mv $HOME/.nolus/priv_validator_state.json.backup $HOME/.nolus/data/priv_validator_state.json
```

## (Optional) Download latest wasm
If the chain does not support copy of the `wasm` folder, you can download it manually.
```
curl -L https://snapshots.kjnodes.com/nolus/wasm_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.nolus
```

## Restart the service and check the log
```
sudo systemctl start nolus.service && sudo journalctl -u nolus.service -f --no-hostname -o cat
```
# END

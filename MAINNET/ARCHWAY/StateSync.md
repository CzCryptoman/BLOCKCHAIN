# State sync

Chain ID: `archway-1` | Current Node Version: `v7.0.0`

```
SNAP_RPC=https://archway-rpc.noders.services:443 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```

```
sudo systemctl stop archwayd && archwayd tendermint unsafe-reset-all --home ~/.archwayd --keep-addr-book
```

```
peers="c4b36b605667e3896eb6f57c5d731519b89dfc6f@archway-rpc.noders.services:13656"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.archwayd/config/config.toml
```

```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" ~/.archwayd/config/config.toml
```

```
sudo systemctl restart archwayd && sudo journalctl -fu archwayd --no-hostname -o cat
```

# END

`https://noders.services/mainnet-networks/archway/statesync`

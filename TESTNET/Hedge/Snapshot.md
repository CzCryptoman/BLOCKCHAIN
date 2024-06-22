
# SNAPSHOT

### 1. Download latest snapshot from our endpoint
```bash
wget https://rpc-hedge-testnet.trusted-point.com/latest_snapshot.tar.lz4 
```
### 2. Stop the node
```bash
sudo systemctl stop hedged
```
### 3. Backup priv_validator_state.json 
```bash
cp $HOME/.hedge/data/priv_validator_state.json $HOME/.hedge/priv_validator_state.json.backup
```
### 4. Reset DB
```bash
hedged tendermint unsafe-reset-all --home $HOME/.hedge --keep-addr-book
```
### 5. Extract files fromt the arvhive 
```bash
lz4 -d -c ./latest_snapshot.tar.lz4 | tar -xf - -C $HOME/.hedge
```
### 6. Move priv_validator_state.json back
```bash
mv $HOME/.hedge/priv_validator_state.json.backup $HOME/.hedge/data/priv_validator_state.json
```
### 7. Restart the node
```bash
sudo systemctl restart hedged && sudo journalctl -u hedged -f -o cat
```
### 8. Check the synchronization status
```bash
hedged status | jq .SyncInfo
```
Snapshot is being updated every 3 hours

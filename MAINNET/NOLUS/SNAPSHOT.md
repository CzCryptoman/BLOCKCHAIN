# SNAPSHOT

![image](https://github.com/user-attachments/assets/49582586-5264-4358-92f6-3fcbd101cbd1)

Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically every 6 hours starting at 03:15 UTC

Pruning: `100/0/19`

Indexer: `null`

Version Tag: `v0.5.3`
	
Block: `6225826`

Age: `5 hours`

Download: `snapshot (1.71 GB) with sha256sum 257121`


## Stop the service
```
sudo systemctl stop nolus.service
```

## Reset the data and save validator state
```
cp $HOME/.nolus/data/priv_validator_state.json $HOME/.nolus/priv_validator_state.json.backup
rm -rf $HOME/.nolus/data
```

## Download latest snapshot and recover validator state
```
curl -L https://snapshots.kjnodes.com/nolus/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.nolus
mv $HOME/.nolus/priv_validator_state.json.backup $HOME/.nolus/data/priv_validator_state.json
```

## Restart the service and check the log
```
sudo systemctl start nolus.service && sudo journalctl -fu nolus.service -o cat
```

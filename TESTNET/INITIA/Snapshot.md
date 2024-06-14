# SNAPSHOTS
`https://polkachu.com/testnets/initia/snapshots`

`https://www.staking4all.org/initia-testnet-services`

`https://services.tienthuattoan.com/testnet/initia/snapshot`


Snapshots enable a fresh node to enter the network by restoring application state from a backup file. Each snapshot comprises a compressed copy of the chain data directory. To maintain compact backup files, the snapshot server undergoes periodic state synchronization.

### You need to have lz4 installed in order to extract the snapshot.
```
sudo apt update
sudo apt install snapd -y
sudo snap install lz4
```
### Begin by stopping the Initia node so that we can replace the node data with the snapshot.
```
sudo service initiad stop
```
### Now we create a backup of the ‘priv_validator_state.json’ file, for a validator node this is critical as it indicates the last block you signed.
```
cp $HOME/.initia/data/priv_validator_state.json $HOME/.initia/priv_validator_state.json.backup
```
### Make a backup of the data. We will restore this from our snapshot.
```
mv $HOME/.initia/data $HOME/.initia/data_bk 
```
### We can now download the snapshot and its contents
```
curl -L http://snapshots.staking4all.org/testnet-snapshots/initia/latest/initia.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.initia
```
### Now, it's crucial to reinsert the 'priv_validator_state.json' file to its original location to prevent the node from attempting to sign blocks that have already been signed.
```
cp $HOME/.initia/priv_validator_state.json.backup $HOME/.initia/data/priv_validator_state.json
```
### You can now start your node.
```
sudo service initiad start
sudo service initiad status
```
### Ensure that the node is syncing by reviewing the logs.
```
sudo journalctl -u initiad -f
```
### You can now remove the backup data, if for some reason it did not work you can revert to your original state by placing the content back.
```
rm -r $HOME/.initia/data_bk 
rm $HOME/.initia/priv_validator_state.json.backup
```

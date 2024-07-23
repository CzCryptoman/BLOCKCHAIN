# ![image](https://github.com/user-attachments/assets/4119d79d-805a-48f8-83dc-81127e3478d6)

# SNAPSHOT

Axelar delivers secure cross-chain communication for Web3, enabling you to build Interchain dApps that grow beyond a single chain. Secure means Axelar is built on proof-of-stake, the battle-tested approach used by Ethereum, Polygon, Cosmos, and more. Cross-chain communication means you can build a complete experience for your users that lets them interact with any asset, any application, on any chain with one click.

Pruning snapshots are taken automatically every 12 hours

Block	Snapshot time	Size	Download	Checksum

13691938	9 hours ago	69.97 GB	LINK	f443a3cc68cc5021a16ea22d5120d385cf312fb37f8bd72a79371d7781dc8ad2
13684668	21 hours ago	70.03 GB	LINK	9e048d54c7bd38af9ec38217c6bc2318d55d88a3dc282078314c18bbc30af365

## Guide

### Preparation install the dependencies
```
sudo apt update
sudo apt install snapd -y
sudo snap install lz4
```


### Stop service and clean the data
```
sudo systemctl stop axelar.service
```

```
cp $HOME/.axelar/data/priv_validator_state.json $HOME/.axelar/priv_validator_state.json.backup
```

```
rm -rf $HOME/.axelar/data
```

or:

```
axelard tendermint unsafe-reset-all --home $HOME/.axelar --keep-addr-book
```

```
rm -rf $HOME/.axelar/wasm
```

## Download snapshot file
```
wget -O lasted_snapshot.tar.lz4 https://public-snapshot-storage-mainnet.s3.amazonaws.com/axelar/axelar-dojo-1/snapshots/axelar_13691938.tar.lz4
```

```
sha256sum lasted_snapshot.tar.lz4 \\The result from checksum should be: f443a3cc68cc5021a16ea22d5120d385cf312fb37f8bd72a79371d7781dc8ad2
```

```
lz4 -c -d lasted_snapshot.tar.lz4 | tar -x -C $HOME/.axelar
```


## Recovery private validator state (If you run a validator)
```
cp $HOME/.axelar/priv_validator_state.json.backup $HOME/.axelar/data/priv_validator_state.json
```


## Remove snapshot file
```
rm -v lasted_snapshot.tar.lz4
```


## Start node and verify log
```
sudo systemctl restart axelar.service && sudo journalctl -u axelar.service -f --no-hostname
```


```
https://services.contributiondao.com/mainnet/axelar/snapshots
```

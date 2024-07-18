# SNAPSHOT

## Download snapshot file
```
wget -O lasted_snapshot.tar.lz4 https://public-snapshot-storage-develop.s3.amazonaws.com/allora/edgenet/snapshots/allora_756401.tar.lz4
```

```
sha256sum lasted_snapshot.tar.lz4 \\The result from checksum should be: ae7742c52ac2271f84907e5149a30e65ba6fd69344562a26b71c86053a39f7cf
```

```
lz4 -c -d lasted_snapshot.tar.lz4 | tar -x -C $HOME/.allorad
```

## Recovery private validator state (If you run a validator)
```
cp $HOME/.allorad/priv_validator_state.json.backup $HOME/.allorad/data/priv_validator_state.json
```

## Remove snapshot file
```
rm -v lasted_snapshot.tar.lz4
```

## Start node and verify log
```
sudo systemctl restart allora.service && sudo journalctl -u allora.service -f --no-hostname
```

# END

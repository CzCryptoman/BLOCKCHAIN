# Snapshot

height: `1,046,324 (11h ago)`;

size: `279G`;

pruning: `custom/100/0/10`;

indexer: `null`

# Install dependencies, if needed
```
sudo apt update
sudo apt install lz4 -y
sudo systemctl stop junctiond
```

```
cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
```

```
junctiond tendermint unsafe-reset-all --home $HOME/.junction --keep-addr-book
curl https://snapshots-testnet.nodejumper.io/airchains-testnet/airchains-testnet_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junction
```

```
mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json
```

```
sudo systemctl restart junctiond
sudo journalctl -u junctiond -f --no-hostname -o cat
```

## Live Peers

number of active peers: 20
```
e2f4bdc67c3a9c2aa93f8a4b8ba400a8311c9bfb@198.7.120.70:26656,545b1032799ab7b6f353c362bc07f9f6b151ed32@65.108.232.156:28656,dbbed6c70e9fe1894d84884640e1145ca2506a5c@14.161.28.247:26656,2ab37263ec407f6effc314626af99167533945a6@45.84.138.4:43456,2ebd062435937af4050486c6e51d3b896d69b4de@65.108.11.119:17656,ac64702f39e3c2ff0cede08a571f2bd6946ed777@100.42.190.127:63656,62338fe5926b9f83b13c942bf619f37c009c11ea@195.26.246.86:63656,c70f853dba012ee6b6f50070657ca28dbd12daf4@65.108.0.88:13056,6634f6c94a64056ae0e0c668786e3fc7e3ba0466@65.109.117.113:28156,0006ec33f419df1530f463cbc18aba9ea9f0abf2@31.220.72.81:26656,60133849b4c83531eb2d835970035a0f08868658@65.109.93.124:28156,5717aadf2f21e223012a1ab27e21307f510037c3@167.235.2.54:13756,d4f57858803002125fe54261e97fa693f26c3fcc@46.196.214.35:63656,083cb86b905731a0685c20408cafaea742711855@195.26.251.142:43456,74761bfe8b88ddd6d733a92ffa28fdb7fddb498f@173.212.250.15:19656,cd118ef6dc1ef27fb9cb734a0a63c1558aa9729b@195.26.252.198:43456,5880ddf4518b061c111ae6bf07b1ef76ef2a42af@158.220.100.154:26656,c70eb86913e7db1a24892f08b1443ad4ed2fdf97@37.27.127.147:11056,bb8ab25f098c7230280645c8a7f72d75eebe7b4e@144.76.112.58:13756,a33c84d70590b2871777c0afc3e9f320fd5d3c4c@144.217.68.182:19656
PEERS="e2f4bdc67c3a9c2aa93f8a4b8ba400a8311c9bfb@198.7.120.70:26656,545b1032799ab7b6f353c362bc07f9f6b151ed32@65.108.232.156:28656,dbbed6c70e9fe1894d84884640e1145ca2506a5c@14.161.28.247:26656,2ab37263ec407f6effc314626af99167533945a6@45.84.138.4:43456,2ebd062435937af4050486c6e51d3b896d69b4de@65.108.11.119:17656,ac64702f39e3c2ff0cede08a571f2bd6946ed777@100.42.190.127:63656,62338fe5926b9f83b13c942bf619f37c009c11ea@195.26.246.86:63656,c70f853dba012ee6b6f50070657ca28dbd12daf4@65.108.0.88:13056,6634f6c94a64056ae0e0c668786e3fc7e3ba0466@65.109.117.113:28156,0006ec33f419df1530f463cbc18aba9ea9f0abf2@31.220.72.81:26656,60133849b4c83531eb2d835970035a0f08868658@65.109.93.124:28156,5717aadf2f21e223012a1ab27e21307f510037c3@167.235.2.54:13756,d4f57858803002125fe54261e97fa693f26c3fcc@46.196.214.35:63656,083cb86b905731a0685c20408cafaea742711855@195.26.251.142:43456,74761bfe8b88ddd6d733a92ffa28fdb7fddb498f@173.212.250.15:19656,cd118ef6dc1ef27fb9cb734a0a63c1558aa9729b@195.26.252.198:43456,5880ddf4518b061c111ae6bf07b1ef76ef2a42af@158.220.100.154:26656,c70eb86913e7db1a24892f08b1443ad4ed2fdf97@37.27.127.147:11056,bb8ab25f098c7230280645c8a7f72d75eebe7b4e@144.76.112.58:13756,a33c84d70590b2871777c0afc3e9f320fd5d3c4c@144.217.68.182:19656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.junction/config/config.toml
```

```
sudo systemctl restart junctiond
sudo journalctl -u junctiond -f --no-hostname -o cat
```

## Address Book

updates every: `1h`

```
curl -s https://snapshots-testnet.nodejumper.io/airchains-testnet/addrbook.json > $HOME/.junction/config/addrbook.json
```

```
sudo systemctl restart junctiond
sudo journalctl -u junctiond -f --no-hostname -o cat
```

# END

```
https://app.nodejumper.io/airchains-testnet/sync
```

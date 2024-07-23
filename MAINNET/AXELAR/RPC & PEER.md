![image](https://github.com/user-attachments/assets/6fc5eda8-2f92-4cfb-9649-54ba0acfae4d)

# Axelar

## RPC

Axelar delivers secure cross-chain communication for Web3, enabling you to build Interchain dApps that grow beyond a single chain. Secure means Axelar is built on proof-of-stake, the battle-tested approach used by Ethereum, Polygon, Cosmos, and more. Cross-chain communication means you can build a complete experience for your users that lets them interact with any asset, any application, on any chain with one click.
RPC URL Endpoint: 
```
https://axelar-rpc.contributiondao.com
```

API URL Endpoint:
```
https://axelar-api.contributiondao.com
```


## Update Seed
```
sed -i.bak -e "s/^seeds *=.*/seeds = \"59df4b3832446cd0f9c369da01f2aa5fe9647248@135.181.220.61:15256\"/" $HOME/.axelar/config/config.toml
```

Peer list will update everyday at 00:00 UTC

## Update Peer List
```
PEERS=546597f5a1635de1ff4f025e1b58f543cd55cfbf@157.90.129.237:26656,3ae54d00de3678562ad7cbeba117ecc7adc84a82@37.27.56.238:26656,3b4ea7101b147aa345fda68299bcfb57315b7d10@65.108.51.111:26656,a87b3a94e4dd7fb5467c200ebe72c88fed0cfaf6@49.12.134.188:28656,49762b752c9fd10e7eefa323ce53b0a731920520@138.201.36.98:26657,5ba72d2f93d4f2db519fa08bf49d014176d5369a@34.123.94.73:26656,133586120b9f5cf1c8608e843723897cdadbc422@18.217.111.172:26656,2f8308f82bf9cd9592361f0fc35676bd1b258557@15.235.87.236:26656,664e90d7939f3eaeb03c4353ddcf23fa4148ced1@85.237.192.20:26656,d1d9761737f3008bfa6e48f356ea4f40073ef4da@84.244.95.227:26656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.axelar/config/config.toml
```

## Restart your node and verify log
```
sudo systemctl restart axelar.service && sudo journalctl -u axelar.service -f --no-hostname
```


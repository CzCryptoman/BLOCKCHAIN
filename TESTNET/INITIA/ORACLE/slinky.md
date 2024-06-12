# How to running oracle Initia Testnet
```
git clone https://github.com/skip-mev/slinky.git 
cd slinky
```
### checkout proper version
```
git checkout v0.4.3
```
```
/nano slinky/config/core/oracle.json
```
```
{ "name": "marketmap_api", "api": { "enabled": true, "timeout": 20000000000, "interval": 10000000000, "reconnectTimeout": 2000000000, "maxQueries": 1, "atomic": true, "url": "0.0.0.0:{GRPC PORT}", // URL that must point to a node GRPC endpoint on .initia/config.toml "endpoints": null, "batchSize": 0, "name": "marketmap_api" }, "type": "market_map_provider" } ], "metrics": { "prometheusServerAddress": "0.0.0.0:8002", "enabled": true }, "host": "0.0.0.0", "port": "8080" same public port on initia app.toml Oracle Address }
```
```
make build
```

### create service slinky: port 10690
```
sudo tee /etc/systemd/system/slinky.service > /dev/null <<EOF
[Unit]
Description=Initia Slinky Oracle
After=network-online.target

[Service]
User=$USER
ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 0.0.0.0:10690
Restart=on-failure
RestartSec=30
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable slinky.service
```

or default port 9090
```
sudo nano /etc/systemd/system/slinky.service
```
```
[Unit]
Description=Slinky Service
After=network.target

[Service]
Type=simple
ExecStart=/root/slinky/build/slinky --oracle-config-path ./config/core/oracle.json --market-map-endpoint 0.0.0.0:(set as port same as grpc)
WorkingDirectory=/root/slinky
Restart=on-failure
User=root

[Install]
WantedBy=multi-user.target
```
### Start slinky (oracle)
```
sudo systemctl daemon-reload
sudo systemctl enable slinky
sudo systemctl start slinky
journalctl -fu slinky
```

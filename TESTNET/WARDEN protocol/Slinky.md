
# Run Oracle
### Clone the Repository and build binaries
```
# Clone repository
cd $HOME
rm -rf slinky
git clone https://github.com/skip-mev/slinky.git
cd slinky
git checkout v1.0.9

# Build binaries
make build

# Move binary to local bin
sudo mv build/slinky /usr/local/bin/
rm -rf build
```


### Creat systemd service _(Check your port)_
```
sudo tee /etc/systemd/system/slinky.service > /dev/null <<EOF
[Unit]
Description=warden Slinky Oracle
After=network-online.target

[Service]
User=$USER
ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 0.0.0.0:42290
Restart=on-failure
RestartSec=30
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable slinky.service
```


### Enable Oracle Vote Extension
```
# Update Oracle app.toml
nano $HOME/.warden/config/app.toml
```

add:

```
###############################################################################
###                                  Oracle                                 ###
###############################################################################
[oracle]
# Enabled indicates whether the oracle is enabled.
enabled = "true"

# Oracle Address is the URL of the out of process oracle sidecar. This is used to
# connect to the oracle sidecar when the application boots up. Note that the address
# can be modified at any point, but will only take effect after the application is
# restarted. This can be the address of an oracle container running on the same
# machine or a remote machine.
oracle_address = "0.0.0.0:42280"

# Client Timeout is the time that the client is willing to wait for responses from 
# the oracle before timing out.
client_timeout = "2s"

# MetricsEnabled determines whether oracle metrics are enabled. Specifically
# this enables instrumentation of the oracle client and the interaction between
# the oracle and the app.
metrics_enabled = "true"
```

_**Track oracle with promethus via port: `"x.x.x.x:42280`**_


### Update Oracle config.toml _(restart your warden node after change config)_
```
sed -i.bak \
    -e 's/^timeout_propose *=.*/timeout_propose = "3s"/' \
    -e 's/^timeout_propose_delta *=.*/timeout_propose_delta = "500ms"/' \
    -e 's/^timeout_prevote *=.*/timeout_prevote = "1s"/' \
    -e 's/^timeout_prevote_delta *=.*/timeout_prevote_delta = "500ms"/' \
    -e 's/^timeout_precommit *=.*/timeout_precommit = "1s"/' \
    -e 's/^timeout_precommit_delta *=.*/timeout_precommit_delta = "500ms"/' \
    -e 's/^timeout_commit *=.*/timeout_commit = "1s"/' \
    $HOME/.warden/config/config.toml
```


### Run oracle
```
sudo systemctl start slinky.service
journalctl -fu slinky --no-hostname
```


### Remove Oracle
```
cd $HOME
sudo systemctl stop slinky
sudo systemctl disable slinky
rm /etc/systemd/system/slinky.service
sudo systemctl daemon-reload
cd $HOME
rm -rf $(which slinkyd)
rm -rf slinky
```

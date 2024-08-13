# UPGRADE v0.4.1


```
sudo systemctl stop wardend
cp $HOME/.warden/data/priv_validator_state.json $HOME/priv_validator_state.json
cd $HOME
sudo rm -rf warden
sudo rm -rf /usr/local/bin/wardend
```

```
wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.4.1/wardend_Linux_x86_64.zip
unzip wardend_Linux_x86_64.zip && rm -rf wardend_Linux_x86_64.zip
chmod +x wardend
sudo mv wardend /usr/local/bin
```


```
wardend version --long | tail
```

```
sudo systemctl restart wardend
sudo journalctl -u wardend -f --no-hostname -o cat
```

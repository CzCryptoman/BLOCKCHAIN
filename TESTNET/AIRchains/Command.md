### CHECK SYNC
```
junctiond status 2>&1 | jq .SyncInfo.catching_up
```
### CHECK LOG:
```
sudo journalctl -u junctiond -f --no-hostname -o cat
```
### Reload Service
```
sudo systemctl daemon-reload
```
### Enable Service
```
sudo systemctl enable junctiond
```
### Disable Service
```
sudo systemctl disable junctiond
```
### Start Service
```
sudo systemctl start junctiond
```
### Stop Service
```
sudo systemctl stop junctiond
```
### Restart Service
```
sudo systemctl restart junctiond
```
### Check Service Status
```
sudo systemctl status junctiond
```
### Check Service Logs
```
sudo journalctl -u junctiond -f --no-hostname -o cat
```

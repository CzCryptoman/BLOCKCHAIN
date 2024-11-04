# Waku guide


## Base install:

```
sudo apt update && sudo apt upgrade -y
```

```
sudo apt-get install build-essential git libpq5 jq
```


```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```


```
source "$HOME/.cargo/env"
```

```
rustc --version
```

```
sudo apt install docker.io
```


```
docker --version
```


```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```


```
sudo chmod +x /usr/local/bin/docker-compose
```


```
docker-compose --version
```

```
git clone https://github.com/waku-org/nwaku-compose
cd nwaku-compose
```

```
cp .env.example .env
nano .env
```


```
./register_rln.sh
```



### Port:
```
sudo ufw enable
sudo ufw allow 22     # SSH access
sudo ufw allow 3000   # Grafana dashboard
sudo ufw allow 8545   # Ethereum JSON-RPC
sudo ufw allow 8645   # Waku REST API
sudo ufw allow 9005   # discv5 routine
sudo ufw allow 30304  # libp2p communication
```

```
docker-compose up -d
```


```
docker-compose ps
```


```
docker-compose logs nwaku
```


## Monitoring:
```
nano ~/nwaku-compose/docker-compose.yml
```


Navigate to line 105 and modify the port binding from `127.0.0.1:3000:3000` to `0.0.0.0:3000:3000`. This change allows external access to the Grafana dashboard, making it accessible from any IP address, not just localhost.


Restart Docker Compose Services: Make sure you’re in the `~/nwaku-compose` directory. First, bring down the services with:

```
docker-compose down
```



### 4. Then, start them again in detached mode:
```
docker-compose up -d
```
These commands stop and then restart your services, applying the new configuration. Access your node metrics, by opening a web browser and navigate to `http://<your_ip_address>:3000/d/yns_4vFVk/nwaku-monitoring`, replacing `<your_ip_address` with the actual `IP address` of `your VPS`. After a few minutes, the dashboard will show metrics from your node, including average message size, message distribution, connected peers, and much more.


 •   To Retrieve the Nwaku Node Version: Use the following curl command to fetch the version of your nwaku node. This can be useful for debugging or ensuring you’re running the intended version.
curl --location 'http://127.0.0.1:8645/debug/v1/version'
 •   To Get Information About the Nwaku Node: This command provides detailed information about your node’s configuration and current state. It’s helpful for a comprehensive overview of the node’s operational parameters.
curl --location 'http://127.0.0.1:8645/debug/v1/info'
These commands are executed from your terminal and will return JSON responses with the requested data. They’re particularly useful for monitoring the health and status of your nwaku node, ensuring it’s functioning as expected within the network.



Congratulations on successfully launching your Nwaku node with RLN enabled through Docker Compose!


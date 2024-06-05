# Create an EVM ZK Rollup
### 1. Clone GitHub Repositories
Start by cloning the necessary repositories from GitHub. Use the following commands in your terminal:

```
git clone https://github.com/airchains-network/evm-station.git
git clone https://github.com/airchains-network/tracks.git
```
If you're utilizing the Eigen DA, there's no necessity to clone any repository. Otherwise, clone the respective repositories for the DA client you're using.

Avail

```
wget https://github.com/airchains-network/tracks/releases/download/v0.0.2/avail-light
```

Celetia
```
wget https://github.com/airchains-network/tracks/releases/download/v0.0.2/celestia
```
### 2. Setting Up and Running the EVM Station
Installing Dependencies
After cloning the repository, navigate into the project directory.

```
cd evm-station
```
Then, tidy up the Go modules to ensure that dependencies are properly managed.

```
go mod tidy
```
Running the Project
To run the EVM Station project locally, execute the following command:

```
/bin/bash ./scripts/local-setup.sh
```
`save data`

Run in new tmux session:
```
/bin/bash ./scripts/local-start.sh
```
Get Your Private Key of  EVM Station
```
 /bin/bash ./scripts/local-keys.sh
```
`save data`

### 3. Setting Up DA Keys and Running DA Client

Eigen
Download the Binary to Generate Keys
```
cd $HOME
wget https://github.com/airchains-network/tracks/releases/download/v0.0.2/eigenlayer
```
Create and List Keys

```
./eigenlayer operator keys create --key-type ecdsa [yourkeyname]
```
Save your data to use later. 
Public Hex Key is the DA key. it look like:

```
eigenlayer operator keys create --key-type ecdsa test
? Enter password to encrypt the ecdsa private key:
ECDSA Private Key (Hex):  b3eba201405d5b5f7aaa9adf6bb734dc6c0f448ef64dd39df80ca2d92fca6d7b
Please backup the above private key hex in safe place.

Key location: /home/ubuntu/.eigenlayer/operator_keys/test.ecdsa.key.json
Public Key hex:  f87ee475109c2943038b3c006b8a004ee17bebf3357d10d8f63ef202c5c28723906533dccfda5d76c1da0a9f05cc6d32085ca1af8aaab5a28171474b1ad0aa68
Ethereum Address 0x6a8c0D554a694899041E52a91B4EC3Ff23d8aBD5
```

### 4. Setting Up and Running Tracks
Remove Old Data

Before initiating the setup process, ensure that any old data is removed if present. Use the following command:

```
sudo rm -rf ~/.tracks
```
Installing Dependencies
After cloning the repository, navigate into the project directory.

```
cd $HOME/tracks
```
Then, tidy up the Go modules to ensure that dependencies are properly managed.

```
go mod tidy
```
Initiate Sequencer

To initiate the sequencer, execute the following command:

Please Add Da-rpc, Da-keys and Da-type accordingly. Not doing this Can cause Unexpected Errors while Submitting to DA Clients

```
go run cmd/main.go init --daRpc "da-rpc" --daKey "daKey" --daType "<da-type>" --moniker "<moniker-name>" --stationRpc "http://127.0.0.1:8545" --stationAPI "http://127.0.0.1:8545" --stationType "evm"
```
Specify the DA type using the --daType flag with one of the following options:

`"eigen"` for `Eigen DA`

The DA keys were defined in the section above when we were setting up the DA. Use the Da Keys Accordingly.

Da Key for `Eigen-Da` is `Eigen-Key`

List of DA RPC

Eigen:-  `disperser-holesky.eigenda.xyz`


Create Keys for Junction

Generate keys for the Junction component using the following command:

```
go run cmd/main.go keys junction --accountName <account-name> --accountPath $HOME/.tracks/junction-accounts/keys
```
Fund Keys for Junction Testnet

Navigate to the Switchyard faucet in the Airchains Discord group and follow the provided steps to obtain Switchyard tokens for funding the keys.

Initiate Prover

Initiate the Prover component using the following command:

```
go run cmd/main.go prover v1EVM
```
Create Station on Junction

Create a station on the Junction component with the specified parameters using the following command:

```
go run cmd/main.go create-station --accountName <account-name> --accountPath $HOME/.tracks/junction-accounts/keys --jsonRPC "https://airchains-testnet-rpc.cosmonautstakes.com/" --info "EVM Track" --tracks <wallet-address> --bootstrapNode "/ip4/192.168.1.24/tcp/2300/p2p/<node_id>"
```
In the --bootstrapNode parameter, input your persistence peer. Follow these steps to create your bootstrapNode persistence_peer with the `node ID`:

Locate the node ID in the `~/.track/config/sequencer.toml` configuration file.

Copy the `node ID` from the configuration file.

Insert the copied `node ID` into the following string format:

`"/ip4/<user-ip>/tcp/2300/p2p/<node_id>"`
Replace `<user-ip>` with your `actual IP address` and `<node_id>` with the copied `node ID`.

This ensures that your track ID is correctly linked with the corresponding node ID for persistence_peer configuration.

Start Node

To start the node, execute the following command: (in new tmux session)

```
go run cmd/main.go start
```
This sequence sets up the necessary components for managing tracks effectively. Ensure that each step is executed correctly to ensure smooth operation.

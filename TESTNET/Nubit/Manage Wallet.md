# Manage Keys and Wallet

### List Keys
Use the following command to view your Nubit address list:

```
nkey list --p2p.network $NETWORK --node.type $NODE_TYPE
```
### Export Keys
If initializing the light node for the first time, a Nubit address named my_nubit_key will be automatically generated. The mnemonic phrase will only appear once, so make sure to record it.


Note this MNEMONIC only appear once
If you missed the mnemonic phrase for your address, no need to worry; you can use the following command to view the unarmored hex private key for the address. Replace my_nubit_key with the name of the address you want to query.

```
nkey export my_nubit_key --unarmored-hex --unsafe --p2p.network $NETWORK --node.type $NODE_TYPE
```
Import Keys into Keplr Wallet
Nubit Alpha Testnet has been integrated with the Keplr wallet. To better manage your nubit address, you can import it into your Keplr wallet by following these steps.

Visit the Keplr Chains website at chains.keplr.app, search for "Nubit Alpha Testnet," and add it to your wallet.


Add "Nubit Alpha Testnet" Chain to Keplr
Open the Keplr wallet extension and click the user avatar in the top right corner. Select "Add Wallet," then choose either "Import an existing wallet" or "Create a new wallet." Opt for the "24 words recovery phrase" and enter your mnemonic phrase, or opt for the "private key" and enter your unarmored hex private key.

Set a custom name for your wallet, select "Nubit Alpha Testnet" Chain and confirm. Your Nubit address will now appear in the Keplr wallet extension, ready for receive and send transactions. For more faucet details, please refer to "Get NUB" section.


Check your Nubit Wallet in Keplr Extension
### Import Keys
You can create a Nubit address in the Keplr wallet and import its mnemonic phrase into nubit-node. Replace my_keplr_key with the desired key name. When prompted with "Enter your bip39 mnemonic," enter your mnemonic phrase:

```
nkey add my_keplr_key --recover --keyring-backend test --node.type $NODE_TYPE --p2p.network $NETWORK
```
Nubit-node also supports importing addresses using private key files. Refer to the following command, replacing my_nubit_key with the desired key name and ~/nubit-da/nubit-node/account1.private with the actual location of your private key file:

```
nkey import my_nubit_key ~/nubit-da/nubit-node/account1.private --keyring-backend test --node.type $NODE_TYPE --p2p.network $NETWORK
```
### Delete Keys
Please note that nubit-node will only use the first key it encounters. If you wish to switch to a new key, you need to delete the previous key. This command will clear all your address data in nubit-node, so proceed with caution! We recommend exporting your keys and storing them safely before deleting any addresses to ensure you can reuse them later:

```
rm -rf $HOME/.nubit-$NODE_TYPE*
```

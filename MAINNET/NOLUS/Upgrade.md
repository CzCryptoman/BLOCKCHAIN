# UPGRADE

## Download and build upgrade binaries

### Clone project repository
```
cd $HOME
rm -rf nolus-core
git clone https://github.com/Nolus-Protocol/nolus-core.git
cd nolus-core
git checkout v0.5.3
```

### Build binaries
```
make build
```

### Prepare binaries for Cosmovisor
```
mkdir -p $HOME/.nolus/cosmovisor/upgrades/v0.5.3/bin
mv target/release/nolusd $HOME/.nolus/cosmovisor/upgrades/v0.5.3/bin/
rm -rf build
```

Thats it! Now when upgrade block height is reached, Cosmovisor will handle it automatically!

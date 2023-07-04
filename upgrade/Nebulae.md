# Nebulae Upgrade Instructions

RIZON Mainnet Nebulae upgrade is expected to happen on `9,522,000` block height. See [here](https://www.mintscan.io/rizon/blocks/9522000) for the countdown.

Currently, the on-chain governance proposal of [#4](https://www.mintscan.io/rizon/proposals/4) has been submitted. Once this proposal passes, Titan will be upgraded.

# Upgrade features
- Apply CosmWasm (wasmd [v0.31.0](https://github.com/CosmWasm/wasmd/releases/tag/v0.31.0), wasmvm [v1.2.1](https://github.com/CosmWasm/wasmvm/releases/tag/v1.2.1))
- Upgrade Tendermint to Comet BFT [v0.34.28](https://github.com/cometbft/cometbft/releases/tag/v0.34.28)
- Bump Cosmos SDK to [v0.45.16](https://github.com/cosmos/cosmos-sdk/releases/tag/v0.45.16)
- Bump IBC Go to [v4.4.1](https://github.com/cosmos/ibc-go/releases/tag/v4.4.1)
- Add modules
  - globalfee
  - packet-forward-middleware
  - ibc-fee
  - ibc-hooks
- Upgrade golang to [1.20.x](https://go.dev/blog/go1.20)


# Getting prepared for the upgrade

## Upgrade manually

Run `rizond` v0.4.1 till upgrade height `9,522,000`, the node will panic:

```
ERR UPGRADE "Nebulae" NEEDED at height: 9522000
panic: UPGRADE "Nebulae" NEEDED at height: 9522000
```

After stopping the node, install `rizond` v0.5.0 and simply restart node by `rizond start`.

```
cd $RIZON_SOURCE_DIRECTORY

git checkout v0.5.0
make install

rizond start
```

## Use Cosmovisor

### Install and setup Cosmovisor

We highly recommend validators use cosmovisor to run their nodes. This will make low-downtime upgrades smoother,
as validators don't have to manually upgrade binaries during the upgrade, and instead can preinstall new binaries, and
cosmovisor will automatically update them based on on-chain SoftwareUpgrade proposals.

You should review the docs for cosmovisor located here: <https://docs.cosmos.network/v0.45/run-node/cosmovisor.html>

If you choose to use cosmovisor, please continue with these instructions.

To install Cosmovisor:

```sh
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.0.0

```

After this, you must make the necessary folders for cosmosvisor in your daemon home directory (~/.rizon).

```sh
mkdir -p ~/.rizon
mkdir -p ~/.rizon/cosmovisor
mkdir -p ~/.rizon/cosmovisor/genesis
mkdir -p ~/.rizon/cosmovisor/genesis/bin
mkdir -p ~/.rizon/cosmovisor/upgrades
mkdir -p ~/.rizon/cosmovisor/upgrades/Gargantua
mkdir -p ~/.rizon/cosmovisor/upgrades/Gargantua/bin
```

Cosmovisor requires some ENVIRONMENT VARIABLES be set in order to function properly.  We recommend setting these in
your `.profile` so it is automatically set in every session.

For validators we recommmend setting
- `DAEMON_ALLOW_DOWNLOAD_BINARIES=false` for security reasons
- `DAEMON_LOG_BUFFER_SIZE=512` to avoid a bug with extra long log lines crashing the server.
- `DAEMON_RESTART_AFTER_UPGRADE=true` for unattended upgrades

```
echo "# Setup Cosmovisor" >> ~/.profile
echo "export DAEMON_NAME=rizond" >> ~/.profile
echo "export DAEMON_HOME=$HOME/.rizon" >> ~/.profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=false" >> ~/.profile
echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.profile
echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.profile
echo "export UNSAFE_SKIP_BACKUP=true" >> ~/.profile
source ~/.profile
```
You may leave out `UNSAFE_SKIP_BACKUP=true`, however the backup takes a decent amount of time and public snapshots of old states are available.

Next, you should copy the **current** version `v0.4.1` rizond binary into the cosmovisor *bin* folders.
```
cp $GOPATH/bin/rizond ~/.rizon/cosmovisor/genesis/bin
cp $GOPATH/bin/rizond ~/.rizon/cosmovisor/upgrades/Gargantua/bin
```


### Make new binary for upgrade and run cosmovisor
To prepare for the upgrade, you need to create **NEXT** upgrade folders, build and install the new binary to there.

```
mkdir -p ~/.rizon/cosmovisor/upgrades/Nebulae/bin

cd $RIZON_SOURCE_DIRECTORY

git checkout v0.5.0
make install

cp $GOPATH/bin/rizond ~/.rizon/cosmovisor/upgrades/Nebulae/bin
```

Now cosmovisor will run with the current binary, and will automatically upgrade to this new binary at the appropriate height if run with:
```
cosmovisor start --x-crisis-skip-assert-invariants
```

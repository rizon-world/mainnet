# WIP

# Gargantua Upgrade Instructions

RIZON Mainnet (a.k.a Titan) v0.4.0 upgrade is expected to happen on `5,113,000` block height.
Currently, the on-chain governance proposal of #2 has been submitted. Once this proposal passes, Titan will be upgraded.

# Upgrade features
 - Upgrade Cosmos SDK to [v0.45.6](https://github.com/cosmos/cosmos-sdk/releases/tag/v0.45.6). See the [CHANGELOG.md](https://github.com/cosmos/cosmos-sdk/blob/v0.45.6/CHANGELOG.md) for details.
 - Upgrade IBC to [v3.0.0](https://github.com/cosmos/ibc-go/releases/tag/v3.0.0). See the [CHANGELOG.md](https://github.com/cosmos/ibc-go/blob/v3.0.0/CHANGELOG.md) for details.
 - Upgrade tendermint to [v0.34.19](https://github.com/tendermint/tendermint/releases/tag/v0.34.19). See the [CHANGELOG.md](https://github.com/tendermint/tendermint/blob/v0.34.19/CHANGELOG.md#v0.34.19) for details.
 - Add [interchain account](https://github.com/cosmos/ibc-go/tree/main/modules/apps/27-interchain-accounts) (ica) module.
 - Add migration logs for upgrade process.
 - Bump golang prerequisite to 1.18.


# Getting prepared for the upgrade
## Install and setup Cosmovisor

We highly recommend validators use cosmovisor to run their nodes. This will make low-downtime upgrades smoother,
as validators don't have to manually upgrade binaries during the upgrade, and instead can preinstall new binaries, and
cosmovisor will automatically update them based on on-chain SoftwareUpgrade proposals.

You should review the docs for cosmovisor located here: https://docs.cosmos.network/master/run-node/cosmovisor.html

If you choose to use cosmovisor, please continue with these instructions:

To install Cosmovisor:

```sh
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor

```

After this, you must make the necessary folders for cosmosvisor in your daemon home directory (~/.rizon).

```sh
mkdir -p ~/.rizon
mkdir -p ~/.rizon/cosmovisor
mkdir -p ~/.rizon/cosmovisor/genesis
mkdir -p ~/.rizon/cosmovisor/genesis/bin
mkdir -p ~/.rizon/cosmovisor/upgrades
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

Finally, you should copy the current rizond binary into the cosmovisor/genesis folder.
```
cp $GOPATH/bin/rizond ~/.rizon/cosmovisor/genesis/bin
```


# Make new binary for upgrade and run cosmovisor
To prepare for the upgrade, you need to create some folders, and build and install the new binary.

```
mkdir -p $DAEMON_HOME/cosmovisor/upgrades/Gargantua/bin
git clone https://github.com/rizon-world/rizon
cd rizon
git checkout v0.4.0
make install
cp $GOPATH/bin/rizond $DAEMON_HOME/cosmovisor/upgrades/Gargantua/bin
```

Now cosmovisor will run with the current binary, and will automatically upgrade to this new binary at the appropriate height if run with:
```
cosmovisor start
```

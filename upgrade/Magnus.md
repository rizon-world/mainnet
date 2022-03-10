
 RIZON Mainnet (a.k.a Titan) 0.3.0 upgrade is expected to happen on ## 2,648,000 block height. 
Currently, the on-chain governance proposal of #1 has been submitted on March 9. Once this proposal passes, RIZON Titan will be upgrading to mainnet 0.3.0 on ## 2,648,000 block height. 

# Upgrade features
	- Bump Cosmos SDK to [v0.44.5](https://github.com/cosmos/cosmos-sdk/releases/tag/v0.44.5). See the [CHANGELOG.md](https://github.com/cosmos/cosmos-sdk/blob/v0.44.5/CHANGELOG.md) for details.
	- Add  [authz](https://github.com/cosmos/cosmos-sdk/tree/v0.44.3/x/authz/spec)  and  [feegrant](https://github.com/cosmos/cosmos-sdk/tree/v0.44.3/x/feegrant/spec)  modules from Cosmos SDK v0.44.3.
	- Add IBC as a standalone module from the Cosmos SDK using version [v2.0.2](https://github.com/cosmos/ibc-go/releases/tag/v2.0.2). See the [CHANGELOG.md](https://github.com/cosmos/ibc-go/blob/v2.0.2/CHANGELOG.md) for details.
	- Remove legacy migration code.
	- fix: panic on export zero-height because variable-length addresses
	- add NewSetUpContextDecorator to antedecorator
	- new AnteHandler that rejects redundant IBC transactions to save relayers fees.
	- Add MsgServer to Configurator for [ADR 031](https://github.com/cosmos/cosmos-sdk/blob/58597139fa0fb9e9be60deebee3df1663aa2cfaf/docs/architecture/adr-031-msg-service.md) wiring
	- fix: Capability Issue on Restart, Backport to v0.43
	- Remove IBC logic from x/upgrade
	- Modify IBC client governance unfreezing to reflect ADR changes 
	- Refactoring : {
		- fix: possibly lossy conversion
		- chore: fix comment description
		- fix: hard-coded values to constant
		- fix: improper use of MarshalBinaryBare function
		- fix: store key naming convention
		- fix: remove redundant casting
		- fix: introduce module store key retrieval function
	}
	- fix: Upgrading to v0.44 breaks vested accounts
	- Bump tendermint to [v0.34.15](https://github.com/tendermint/tendermint/releases/tag/v0.34.15). See the [CHANGELOG.md](https://github.com/tendermint/tendermint/blob/v0.34.15/CHANGELOG.md#v0.34.15) for details. 
	- Bump golang prerequisite to 1.17. 

# Getting prepared for the upgrade
## Install and setup Cosmovisor

We highly recommend validators use cosmovisor to run their nodes. This will make low-downtime upgrades smoother,
as validators don't have to manually upgrade binaries during the upgrade, and instead can preinstall new binaries, and
cosmovisor will automatically update them based on on-chain SoftwareUpgrade proposals.

You should review the docs for cosmovisor located here: https://docs.cosmos.network/master/run-node/cosmovisor.html

If you choose to use cosmovisor, please continue with these instructions:

To install Cosmovisor:

```
git clone https://github.com/cosmos/cosmos-sdk
cd cosmos-sdk
git checkout v0.42.9
make cosmovisor
cp cosmovisor/cosmovisor $GOPATH/bin/cosmovisor
cd $HOME
```

After this, you must make the necessary folders for cosmosvisor in your daemon home directory (~/.rizond).

```sh
mkdir -p ~/.rizond
mkdir -p ~/.rizond/cosmovisor
mkdir -p ~/.rizond/cosmovisor/genesis
mkdir -p ~/.rizond/cosmovisor/genesis/bin
mkdir -p ~/.rizond/cosmovisor/upgrades
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
echo "export DAEMON_HOME=$HOME/.rizond" >> ~/.profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=false" >> ~/.profile
echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.profile
echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.profile
echo "export UNSAFE_SKIP_BACKUP=true" >> ~/.profile
source ~/.profile
```
You may leave out `UNSAFE_SKIP_BACKUP=true`, however the backup takes a decent amount of time and public snapshots of old states are available.

Finally, you should copy the current rizond binary into the cosmovisor/genesis folder.
```
cp $GOPATH/bin/rizond ~/.rizond/cosmovisor/genesis/bin
```


# Make new binary for upgrade(Magnus) and Run cosmovisor 
To prepare for the upgrade, you need to create some folders, and build and install the new binary.

```
mkdir -p $DAEMON_HOME/cosmovisor/upgrades/Magnus/bin
git clone https://github.com/rizon-world/rizon
cd rizon
git checkout v0.3.0
make install
cp $GOPATH/bin/rizond $DAEMON_HOME/cosmovisor/upgrades/Magnus/bin
```

Now cosmovisor will run with the current binary, and will automatically upgrade to this new binary at the appropriate height if run with:
```
cosmovisor start
```


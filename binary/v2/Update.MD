<p align="center">
#Cosmovisor setup for IDEP Network
</p>

##Installation
`go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@latest`


### Your installation can be confirmed with

`which cosmovisor`

### Add environment variables to your shell
In the .profile file, usually located at ~/.profile, add:


`export DAEMON_NAME=iond`<br />
`export DAEMON_HOME=$HOME/.ion`<br />
`export DAEMON_RESTART_AFTER_UPGRADE=true`<br />

Then source your profile to have access to these variables:

`source ~/.profile`

You can confirm success like so:

`echo $DAEMON_NAME`

It should return iond

## Set up folder structure
Cosmovisor expects a certain folder structure:

.  
├── current -> genesis or upgrades/<name>  
├── genesis  
│   └── bin  
│       └── $DAEMON_NAME  
└── upgrades  
└── (name)  
└── bin  
└── $DAEMON_NAME  

Don't worry about current - that is simply a symlink used by Cosmovisor.
The other folders will need setting up, but this is easy:

`mkdir -p $DAEMON_HOME/cosmovisor/genesis/bin`<br />
`mkdir -p $DAEMON_HOME/cosmovisor/upgrades/phase2/bin`<br />
`cd $DAEMON_HOME/cosmovisor/genesis/bin`<br />
`wget https://github.com/IDEP-network/incentivized-testnet/raw/main/binary/iond`<br />
`chmod +x iond`
`cd $DAEMON_HOME/cosmovisor/upgrades/phase2/bin`<br />
`wget https://github.com/IDEP-network/incentivized-testnet/raw/main/binary/v2/iond`<br />
`chmod +x iond`

## Install Wasmvm dependency

`go get -u github.com/CosmWasm/wasmvm@v1.0.0-beta4`<br />
`cd $HOME/go/pkg/mod/github.com/!cosm!wasm/wasmvm@v1.0.0-beta4/api`<br />
`chmod +x libwasmvm.so`<br />

### Add in ~/.bashrc
`export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/go/pkg/mod/github.com/!cosm!wasm/wasmvm@v1.0.0-beta4/api`

### Stop existing node instance

## Start Cosmovisor
`cosmovisor start`




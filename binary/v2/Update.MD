<h1 align="center">
Cosmovisor setup for IDEP Network
</h1>

## Installation

`go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@latest`

### Your installation can be confirmed with

`which cosmovisor`

### Set variables

```
DAEMON_NAME=iond
DAEMON_HOME=$HOME/.ion
```

## Set up folder structure

Cosmovisor expects a certain folder structure:
```
.  
├── current -> genesis or upgrades/<name>  
├── genesis  
│   └── bin  
│       └── $DAEMON_NAME  
└── upgrades  
└── (name)  
└── bin  
└── $DAEMON_NAME
```

Don't worry about current - that is simply a symlink used by Cosmovisor.
The other folders will need setting up, but this is easy:
```
mkdir -p $DAEMON_HOME/cosmovisor/genesis/bin
mkdir -p $DAEMON_HOME/cosmovisor/upgrades/phase2/bin
cd $DAEMON_HOME/cosmovisor/genesis/bin
wget https://github.com/IDEP-network/incentivized-testnet/raw/main/binary/iond
chmod +x iond
cd $DAEMON_HOME/cosmovisor/upgrades/phase2/bin
wget https://github.com/IDEP-network/incentivized-testnet/raw/main/binary/v2/iond
chmod +x iond
```

## Install Wasmvm dependency
```
go get -u github.com/CosmWasm/wasmvm@v1.0.0-beta4
cd $HOME/go/pkg/mod/github.com/\!cosm\!wasm/wasmvm@v1.0.0-beta4/api
chmod +x libwasmvm.so
```

### Add LD_LIBRARY_PATH
```
echo "export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/go/pkg/mod/github.com/\!cosm\!wasm/wasmvm@v1.0.0-beta4/api" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Stop existing node instance

`sudo systemctl stop iond`

### Backup current service

`mv /etc/systemd/system/iond.service /etc/systemd/system/iond_backup.service`

## Recreate service with Cosmovisor
```
sudo tee /etc/systemd/system/iond.service > /dev/null <<EOF  
[Unit]
Description=iond cosmovisor
After=network-online.target
[Service]
User=$USER
ExecStart=$(which cosmovisor) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.ion"
Environment="DAEMON_NAME=iond"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"
[Install]
WantedBy=multi-user.target
EOF
```

### Run service

```
sudo systemctl daemon-reload
sudo systemctl enable iond
sudo systemctl restart iond && journalctl -u iond -f
```


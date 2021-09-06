# ki-testnet-relayer

# Relayer installation
git clone https://github.com/cosmos/relayer.git
<br>cd relayer & make install

# Init config relayer
rly cfg init

# config of different chain

vi kichain-t-4.json<br>
{
  <br>"chain-id": "kichain-t-4",
  <br>"rpc-addr": "http://127.0.0.1:26657",
  <br>"account-prefix": "tki",
  <br>"gas-adjustment": 1.5,
  <br>"gas-prices": "0.001utki",
  <br>"trusting-period": "48h"
<br>}
<br>
<br>vi akash-testnet-6.json
<br>{
<br>"chain-id":"akash-testnet-6",
<br>"rpc-addr":"http://147.75.32.35:26657",
<br>"account-prefix":"akash",
<br>"gas-adjustment":1.5,
<br>"gas-prices":"0.025uakt",
<br>"trusting-period":"48h"
<br>}
<br>
<br>vi cosmos.json 
<br>{
 <br> "chain-id": "cosmoshub-testnet",
 <br> "rpc-addr": "https://rpc.testnet.cosmos.network:443",
  <br>"account-prefix": "cosmos",
  <br>"gas-adjustment": 1.5,
  <br>"gasPrice": "0.0stake",
  <br>"trusting-period": "48h"
<br>}

# load chain settings in relayer
rly chains add -f kichain-a.json
<br>rly chains add -f akash-testnet-6.json
<br>rly chains add -f cosmos.json

# Create wallet for chain
rly keys add kichain-t-4 testkey
<br>rly keys add akash-testnet-6 testkey
<br>rly keys add cosmoshub-testnet testkey
<br>
<br>it's possible to recover existing wallet with 
rly keys restore <chain-id> <wallet-name> "mnemonic"
  
# Add token to wallet
use faucet of different chain for send some token
<br>for cosmos : curl -X POST -d '{"address": "<ADRESS>"}' https://faucet.testnet.cosmos.network
<br>for akash : https://akash.vitwit.com/faucet

# Verify balance of wallet
<br>rly q balance kichain-t-4
<br>rly q balance akash-testnet-6
<br>rly q balance cosmoshub-testnet
  
# Link wallet to chain 
<br>rly chains edit kichain-t-4 key testkey
<br>rly chains edit akash-testnet-6 key testkey
<br>rly chains edit cosmoshub-testnet key testkey
  
# Initialyze light client
<br>rly light init kichain-t-4 -f
<br>rly light init cosmoshub-testnet -f
<br>rly light init akash-testnet-6 -f
  
# Create Paths 
<br>rly paths generate kichain-t-4 cosmoshub-testnet transfer --port=transfer
<br>rly paths generate kichain-t-4 akash-testnet-6 transfer2 --port=transfer
  
# Create channel connection and client 
rly tx link transfer -d -o 3s
rly tx link transfer2 -d -o 3s
  
# Ready to send some token

<br>rly tx transfer kichain-t-4 akash-testnet-6 50000utki <ADRESS_DEST> --path transfer2
<br>rly tx transfer kichain-t-4 cosmoshub-testnet 50000utki <ADRESS_DEST> --path transfer
<br>rly tx transfer akash-testnet-6 kichain-t-4  50000uakt <ADRESS_DEST> --path transfer2
<br>rly tx transfer cosmoshub-testnet kichain-t-4 50000uphoton <ADRESS_DEST> --path transfer
<br>
<br>example :
<br>/opt/kinodetestnet2# rly tx transfer cosmoshub-testnet kichain-t-4 50000uphoton tki1ezz7wm32v5457a94ad33p2vyal4ysu6jduzqdy --path transfer
<br>I[2021-09-06|22:31:37.258] ✔ [cosmoshub-testnet]@{315654} - msg(0:transfer) hash(60344DD0DB48CFA4C69E034D6B63E5DE1314B6867D79949D976B6C484C4C03E6)

# create service for relayer
<br>vi /etc/systemd/system/rly-cosmos.service
<br>[Unit]
<br>Description=Ki Chain relayer cosmos
<br>After=network-online.target
<br>
<br>[Service]
<br>User=root
<br>ExecStart=/root/go/bin/rly start transfer
<br>Restart=always
<br>RestartSec=3
<br>LimitNOFILE=4096
<br>
<br>[Install]
<br>WantedBy=multi-user.target
<br>  
<br> 
<br>vi /etc/systemd/system/rly-akash.service
<br>[Unit]
<br>Description=Ki Chain relayer akash
<br>After=network-online.target
<br>
<br>[Service]
<br>User=root
<br>ExecStart=/root/go/bin/rly start transfer2
<br>Restart=always
<br>RestartSec=3
<br>LimitNOFILE=4096
<br>
<br>[Install]
<br>WantedBy=multi-user.target







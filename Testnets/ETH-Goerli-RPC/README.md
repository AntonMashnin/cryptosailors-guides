<p align="center">
 <img src="https://i.postimg.cc/L8DRwBr1/Ethereum-1.jpg"width="900"/></a>
</p>

# In this guide we will setup ETH RPC node on Goerli network

#### Flollowing parametrs:

- 4-CPU
- 8-GBRAM
- 400-GBSSD 

## 1. Node Preparation
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install make clang pkg-config libssl-dev libclang-dev build-essential git curl ntp jq llvm tmux htop screen unzip cmake -y
```
```
sudo apt -y install software-properties-common wget curl ccze
```
## 2. Installing Geth
```
sudo add-apt-repository -y ppa:ethereum/ethereum
```
```
sudo apt -y install geth
```
## 3. Install Prysm
```
mkdir prysm && cd prysm
wget https://github.com/eth-clients/eth2-networks/raw/master/shared/prater/genesis.ssz
```
```
curl https://raw.githubusercontent.com/prysmaticlabs/prysm/master/prysm.sh --output prysm.sh && chmod +x prysm.sh
```
```
./prysm.sh beacon-chain generate-auth-secret
```
```
cd ~
```
## 4. Configuring and launch Geth and Prysm node.
```
sudo tee /etc/systemd/system/geth.service > /dev/null <<EOF
[Unit]
Description=Ethereum Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/bin/geth --goerli --syncmode "snap" --http --http.api=eth,net,web3,engine --http.vhosts * --http.addr 0.0.0.0 \
 --authrpc.jwtsecret=/root/prysm/jwt.hex 
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
Launch our Geth node
```
sudo systemctl daemon-reload
sudo systemctl enable geth
sudo systemctl start geth
sudo journalctl -u geth -f -n 100
```
Our node start looking for peers and beacon. Now we need configure our prysm beacon.
```
sudo tee /etc/systemd/system/prysm.service > /dev/null <<EOF
[Service]
User=$USER
Type=simple
ExecStart=/root/prysm/prysm.sh beacon-chain --prater --execution-endpoint=http://localhost:8551 --jwt-secret=/root/prysm/jwt.hex --suggested-fee-recipient=0x01234567722E6b0000012BFEBf6177F1D2e9758D9 --genesis-state=/root/prysm/genesis.ssz --accept-terms-of-use --checkpoint-sync-url=https://goerli.beaconstate.info --genesis-beacon-api-url=https://goerli.beaconstate.info
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
Launch our Prysm node
```
sudo systemctl daemon-reload
sudo systemctl enable prysm
sudo systemctl start prysm
sudo journalctl -u prysm -f -n 100
```
By command bellow you can check a status of synchronization process of your node.
```
curl -X POST http://localhost:8545 \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}'
```
- If the show `false` that means that your node is fully synchronized.
#

👉[Hetzner — server rental](https://hetzner.cloud/?ref=NY9VHC3PPsL0)

👉[SSH terminal MobaxTerm](https://mobaxterm.mobatek.net/download.html)

👉[Prysm](https://docs.prylabs.network/docs/getting-started)

🔰[Our Telegram Channel](https://t.me/CryptoSailorsAnn)

🔰[Our WebSite](cryptosailors.tech)

🔰[Our Twitter](https://twitter.com/Crypto_Sailors)

🔰[Our Youtube](https://www.youtube.com/@CryptoSailors)

#### Guide created by 
Pavel-LV | C.Sailors#7698 / @SeaInvestor



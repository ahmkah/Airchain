# Airchain Validator Node Kurulumu


## Sistemi güncelliyoruz
```sh
sudo apt update  
sudo apt upgrade -y
```
```sh
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```


## Ignite CLI  yüklüyoruz
```sh
wget https://github.com/ignite/cli/releases/download/v0.27.1/ignite_0.27.1_linux_amd64.tar.gz

chmod +x ignite_0.27.1_linux_amd64.tar.gz

tar -xvf ignite_0.27.1_linux_amd64.tar.gz

rm -rf ignite_0.27.1_linux_amd64.tar.gz

sudo mv ignite /usr/local/bin

ignite version
```
```txt
🛸 Ignite CLI v28.3.0 is available
```


## go yüklüyoruz

```sh
sudo rm -rf /usr/local/go 

curl -L https://go.dev/dl/go1.21.6.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local

echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile

source .bash_profile
```


## Junction node indirip kuruyoruz
```sh
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond

chmod +x junctiond

sudo mv junctiond /usr/local/bin
```

## Moniker adınızı değiştirin aşağıdaki komudu girin
```sh
junctiond init <MONIKER> 
```

## genesis ekliyoruz
```sh
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/genesis.json

cp genesis.json ~/.junction/config/genesis.json
```

## Seed ve peer ekliyoruz ve min gas ayarı yapıyoruz
```sh
SEEDS=""
PEERS="2d1ea4833843cc1433e3c44e69e297f357d2d8bd@5.78.118.106:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.junction/config/config.toml

sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.00025amf\"|" $HOME/.junction/config/app.toml
```

## Pruning komutları (opsiyonel)
```sh
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.junction/config/app.toml
```

## screen içinde çalıştırıyoruz
```sh
screen -S node

junctiond start
```

## Wallet oluşturuyoruz . Mnomanicleri kaydedin
```sh
junctiond keys add wallet

```
## Faucet token alın


```sh

junctiond query bank balances <wallet_adress>

```

## Aşağıdaki komut çıktı verdiğinde eşleşmişsinizdir.Fakat explorerdan kontrol edin

junctiond status

```txt
https://testnets.cosmosrun.info/junction/blocks

```


#validator oluşturmak için validator.json oluşturuyoruz. {} içinde yazan kodları direk yapıştırın ve içlerini doldurun sadece pubkey yazan komudu değiştirirken aşağıdaki çıktıyı değiştirin."junctiond tendermint show-validator" çıktısını pubkey yazan yere yazın ve CTRL+X ile kaydedip kapatın


/ junctiond tendermint show-validator  

```sh
nano ~/.junction/config/validator.json
```
```sh

{
        "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"+9FxV2E8vVqgk4ayncBm34Vtaq+Tj+R3+U6rEdNoMIw="},
        "amount": "900000000amf",
        "moniker": "<MONIKER>",
        "identity": "",
        "website": "",
        "details": "",
        "commission-rate": "0.1",
        "commission-max-rate": "0.2",
        "commission-max-change-rate": "0.01",
        "min-self-delegation": "1",
        "chain-id": "junction"
}

```
/Aşağıdaki komudu girin

```sh
junctiond tx staking create-validator ~/.junction/config/validator.json --from wallet --gas="200000" --fees="1000amf" --chain-id=junction
```

## Explorer
```txt
https://testnets.cosmosrun.info/junction/blocks
```

## kontrol için aşağıdaki komudu çalıştırın

junctiond query tendermint-validator-set

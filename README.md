# Tutorial INSTALL Operator AVS Coinbase
![Banner](https://github-production-user-asset-6210df.s3.amazonaws.com/38981255/356076147-d318c586-e56c-4c98-a10b-1e084ddca58c.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20241031%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20241031T203315Z&X-Amz-Expires=300&X-Amz-Signature=8668cfd4f0b590b8ae6f224968e5082c608cdc2cffc3823463786ce237f69a59&X-Amz-SignedHeaders=host)

## Persyaratan Hardware
| Class | vCPUs (10th gen+) | Memory | Networking Capacity |
|-------|-------------------|--------|-------------------|
| General Purpose - large | 2 | 8 GB | 5 Mbps |
| General Purpose - xl | 4 | 16 GB | 25 Mbps |
| General Purpose - 4xl | 16 | 64 GB | 5 Gbps |

## *NOTE:*

#### *Baca dengan teliti agar tidak bingung ditengah-tengah, ikuti step by step jika di rasa command tidak respond cek directory dan cari file berada*
### *SIAP MUMET = SIAP JP (SEMOGA)*
----------------------------------------------------
# LFG !!!


## 1. Install Software Yang Dibutuhkan
### Install Docker
```bash
sudo apt-get update && \
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common gnupg && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg && \
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null && \
sudo apt-get update && \
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```
```
sudo apt install docker-compose
```

## Install GO
```bash
wget https://go.dev/dl/go1.22.6.linux-amd64.tar.gz && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf go1.22.6.linux-amd64.tar.gz && \
echo "export PATH=\$PATH:/usr/local/go/bin" >> ~/.profile && \
source ~/.profile && \
go version
```

## 2. Install CLI Menggunakan GO
```bash
go install github.com/Layr-Labs/eigenlayer-cli/cmd/eigenlayer@latest
export PATH=$HOME/go/bin:$PATH
echo 'export PATH=$HOME/go/bin:$PATH' >> ~/.profile
source ~/.profile
eigenlayer --version
```

## 3. Buat Wallet Operator Baru
```bash
echo "password" | eigenlayer operator keys create --key-type ecdsa keyname
```
- Ganti "password" dengan password kalian, harus mengandung Huruf Besar, Huruf Kecil, Angka dan Simbol
- Ganti keyname dengan nama kalian (bebas)
- Copy Private Key yang muncul
- Tekan q
- Copy Address Ethereum kalian
(simpan semua di tempat yang aman)

## 4. Cek Daftar Key
```bash
eigenlayer operator keys list
```
## 5. Claim Faucet

- Minimal butuh 1 ETH Holesky di Address yang tadi kita dapatkan
- Claim di: `https://holesky-faucet.pk910.de/` | `https://www.holeskyfaucet.io/`
atau bridge dari sepolia `https://rinkeby.orbiter.finance/?source=Sepolia&dest=Holesky&token=ETH`

## 6. Verifikasi dan Register Operator
```bash
eigenlayer operator config create
```
- Tekan Enter
- Masukkan Address Operator kalian yang tadi dibuat
-Earning Address tekan Enter saja
- RPC ETH Holesky bisa dicari di: `https://chainlist.org/chain/17000` atau gunakan RPC pribadi (saran dari infura/alchemy)
- Pilih Network Holesky
- Pilih Local Keystore
- Untuk ECDSA Key Path, masukkan lokasi file key kalian
- `Contoh: /root/.eigenlayer/operator_keys/nama_kalian.ecdsa.key.json`

## 7. Edit File metadata.json
```bash
nano metadata.json
```
- Edit dan isi data kalian seperti Nama, Website, Deskripsi, Link Twitter
- Untuk Logo, upload logo ke Repository Github
- Ambil Link Raw-nya
- Simpan dengan ctrl + x kemudian y dan enter

## 8. Konfigurasi RPC URL
```bash
curl -I URL-RPC-KALIAN
```
`(ganti dengan URL RPC yang kalian masukkan di step sebelumnya)`

## 9. Create Metadata URL
- Upload file metadata.json kalian ke GitHub
- Ambil link RAW-nya
- Masukkan di operator.yaml bagian metadata_url dan Save `(defaultnya berada di $HOME/operator.yaml)`
```bash
nano operator.yaml
```
- Line 5 `metadata_url=PASTE_DISINI`

## 10. Update operator.yaml
```bash
eigenlayer operator update operator.yaml
```
#### Masukkan Password kalian

#### *Jika muncul tulisan "Operator Not Register":*
```bash
eigenlayer operator register operator.yaml
```
#### Masukkan Password kalian

## 11. Buat BLS-key
```bash
eigenlayer keys create --key-type bls GANTI
```
*`GANTI` dengan keyname mu*
#### Masukkan passwordmu

## 12. Install Chainbase-AVS-CLI
```bash
wget https://github.com/chainbase-labs/chainbase-avs/releases/download/v0.1.9/chainbase-node_Linux_x86_64.tar.gz && \
tar -xzf chainbase-node_Linux_x86_64.tar.gz && \
sudo mv chainbase-node /usr/local/bin/ && \
sudo chmod +x /usr/local/bin/chainbase-node && \
rm chainbase-node_Linux_x86_64.tar.gz
```
## 13. Chainbase AVS Contract Registration
```bash
git clone https://github.com/chainbase-labs/chainbase-avs-setup && cd chainbase-avs-setup/holesky
```
```
mv .env.example .env
```
### *Edit konfigurasi:*

```bash
nano .env
```

### *Isi dengan konfigurasi berikut:*
```bash
# Chainbase AVS Image
MAIN_SERVICE_IMAGE=repository.chainbase.com/network/chainbase-node:v0.1.9-ci-test
FLINK_TASKMANAGER_IMAGE=flink:latest
FLINK_JOBMANAGER_IMAGE=flink:latest
PROMETHEUS_IMAGE=prom/prometheus:latest

MAIN_SERVICE_NAME=chainbase-node
FLINK_TASKMANAGER_NAME=flink-taskmanager
FLINK_JOBMANAGER_NAME=flink-jobmanager
PROMETHEUS_NAME=prometheus

# FLINK CONFIG
FLINK_CONNECT_ADDRESS=flink-jobmanager
FLINK_JOBMANAGER_PORT=8081
NODE_PROMETHEUS_PORT=9091
PROMETHEUS_CONFIG_PATH=./prometheus.yml

# Chainbase AVS mounted locations
NODE_APP_PORT=8080
NODE_ECDSA_KEY_FILE=/app/operator_keys/ecdsa_key.json
NODE_LOG_DIR=/app/logs

# Node logs configs
NODE_LOG_LEVEL=debug
NODE_LOG_FORMAT=text

# Metrics specific configs
NODE_ENABLE_METRICS=true
NODE_METRICS_PORT=9092

# holesky smart contracts
AVS_CONTRACT_ADDRESS=0x5E78eFF26480A75E06cCdABe88Eb522D4D8e1C9d
AVS_DIR_CONTRACT_ADDRESS=0x055733000064333CaDDbC92763c58BF0192fFeBf

###############################################################################
####### BAGIAN YANG HARUS DIUBAH ##############
###############################################################################
#Ganti dengan RPC chain yang berfungsi
NODE_CHAIN_RPC=GANTI_RPC_KALIAN
NODE_CHAIN_ID=17000

# Update path sesuai dengan lokasi kalian
USER_HOME=/root
EIGENLAYER_HOME=${USER_HOME}/.eigenlayer
CHAINBASE_AVS_HOME=${USER_HOME}/chainbase-avs-setup/holesky

NODE_LOG_PATH_HOST=${CHAINBASE_AVS_HOME}/logs

# Path file ECDSA key
NODE_ECDSA_KEY_FILE_PATH="/root/.eigenlayer/operator_keys/GANTI_NAMA_KALIAN.ecdsa.key.json"
NODE_BLS_KEY_FILE_PATH=/root/.eigenlayer/operator_keys/GANTI_NAMA_KEY_KALIAN.bls.key.json

# Password untuk dekripsi key
OPERATOR_NAME=GANTI_NAMA_KALIAN
NODE_ECDSA_KEY_PASSWORD=GANTI_PASSWORD_KALIAN
NODE_BLS_KEY_PASSWORD=GANTI_PASSWORD_KALIAN
OPERATOR_ECDSA_KEY_PASSWORD=GANTI_PASSWORD_KALIAN
OPERATOR_BLS_KEY_PASSWORD=GANTI_PASSWORD_KALIAN
OPERATOR_ADDRESS=GANTI_ADDRESS_KALIAN
NODE_SOCKET=GANTI_IP_VPS:8090
```
#### *(Semua yang bertuliskan GANTI harus diganti sesuai dengan data kalian)*

## 14. Buatkan variable buat register untuk menghindari error
```bash
cat > .env.register << EOL
NODE_ECDSA_KEY_PASSWORD=GANTI
NODE_BLS_KEY_PASSWORD=GANTI
OPERATOR_ECDSA_KEY_PASSWORD=GANTI
OPERATOR_BLS_KEY_PASSWORD=GANTI
NODE_ECDSA_KEY_FILE_PATH=/root/.eigenlayer/operator_keys/GANTI.ecdsa.key.json
NODE_BLS_KEY_FILE_PATH=/root/.eigenlayer/operator_keys/GANTI.bls.key.json
EOL
```

## 15. Cari lokasi docker-compose kalian
```bash
sudo find / -name "docker-compose.yaml" -o -name "docker-compose.yml" 2>/dev/null
```
#### *kalau kalian mengikuti step mungkin docker-compose.yaml anda berada di `/chainbase-avs-setup/holeskydocker-compose.yaml`*
#### jika sudah ketemu edit port di docker-compose agar tidak error
```bash
nano docker-compose.yaml
```
#### cari kata "node" replace / edit :
```bash
node:
    image: repository.chainbase.com/network/chainbase-node:v0.2.1
    container_name: manuscript_node
    hostname: manuscript_node
    ports:
      - 8011:8011
      - 9010:9010
      - 9090:9090
```
	  
jika sudah restart docker :
```bash
docker-compose down
docker-compose up -d
```

## 16. pastikan folder tertentu ada dan pengguna docker memiliki izin menulis yang benar
```bash
cd chainbase-avs-setup/holesky
```
```bash
source .env && mkdir -pv ${EIGENLAYER_HOME} ${CHAINBASE_AVS_HOME} ${NODE_LOG_PATH_HOST}
```

## 17. Mengubah chainbase-avs.sh izin file
```bash
chmod +x ./chainbase-avs.sh
```

## 18. Ganti OPERATOR dengan Nama Operator Kalian
```bash
nano premetheus.yml
```
#### ***PASTE / Edit dengan ini***
```bash
global:
  scrape_interval: 15s
  evaluation_interval: 15s
  external_labels:
    operator: GANTI_NAMA_KALIAN
remote_write:
  - url: http://testnet-metrics.chainbase.com:9090/api/v1/write
    write_relabel_configs:
      - source_labels: [job]
        regex: "chainbase-avs"
        action: keep
scrape_configs:
  - job_name: "chainbase-avs"
    metrics_path: /metrics
    static_configs:
      - targets: ["manuscript_node:9090"]
  - job_name: "flink"
    metrics_path: /metrics
    static_configs:
      - targets:
        - "chainbase_taskmanager:9249"
        - "chainbase_jobmanager:9249"
```
		
## 19. Pastikan kalian masih di directory chainbase-avs-setup/holesky
```bash
nano chainbase-avs.sh
```
#### *Ubah bagian register_chainbase_avs() dengan ini :*
```bash
register_chainbase_avs() {
  echo "Registering Chainbase AVS, ECDSA key path: $NODE_ECDSA_KEY_FILE_PATH ,BLS key path: $NODE_BLS_KEY_FILE_PATH"
  docker run --env-file .env \
  -e OPERATOR_ECDSA_KEY_PASSWORD="$NODE_ECDSA_KEY_PASSWORD" \
  -e OPERATOR_BLS_KEY_PASSWORD="$NODE_BLS_KEY_PASSWORD" \
  --rm \
  --volume "${NODE_ECDSA_KEY_FILE_PATH}":"/app/node.ecdsa.key.json" \
  --volume "${NODE_BLS_KEY_FILE_PATH}":"/app/node.bls.key.json" \
  --volume "./node.yaml":"/app/node.yaml" \
  "repository.chainbase.com/network/chainbase-cli:v0.2.1" \
  --config /app/node.yaml "register-operator-with-avs"
  

}
```
## 20. Untuk memastikan variable masuk
```bash
export OPERATOR_ECDSA_KEY_PASSWORD="PASSWORD_KALIAN"
export OPERATOR_BLS_KEY_PASSWORD="PASSWORD_KALIAN"
```
## 21. Buat Directory & pindahkan keymu untuk variablenya
```bash
sudo mkdir -p /app
sudo cp /root/.eigenlayer/operator_keys/GANTI.ecdsa.key.json /app/node.ecdsa.key.json
sudo cp /root/.eigenlayer/operator_keys/GANTI.bls.key.json /app/node.bls.key.json
```
#### *`GANTI` Sesuai dengan keyname mu*

## 22. Berikan Peizinan env
```bash
sudo chmod 600 /app/node.ecdsa.key.json
sudo chmod 600 /app/node.bls.key.json
```


## 23. Daftarkan Chainbase AVS
```bash
./chainbase-avs.sh register
```

## 24. Run Chainbase AVS
```bash
./chainbase-avs.sh run
```

## 25. Check Kesehatan Node

```bash
curl -i 172.18.0.5:9010/eigen/node/health
```

## 26. Ambil Link Operator
```bash
cd $HOME
```
```bash
eigenlayer operator status operator.yaml
```
### DONE
## ISI [FORM](https://docs.google.com/forms/d/e/1FAIpQLSd-ikaoIrOMd-L_pTvr8e-BIOTxQjkRh8ENGClyl7m1IFbqPQ/viewform?pli=1)

## *SCROLL TRUS BANG*
----------------------------------------------------------------------
----------------------------------------------------------------------
# *Sekalian Testnetnya bro ! modal $1 ETH linea buat mint di Trusta kalo udh pernah mint skip ae*
## DAFTAR [DISINI](https://genesis.chainbase.com/referral?referral_code=L320RUKAC)
### *- Complete all task*
### *DONE*
															
<div align="center">
  
  # ✨ SALAM JEPE ✨
  
</div>

## <div align="center">Powered by : </div>
<p align="center">
  <img src="https://raw.githubusercontent.com/dwisetyawan00/logo/refs/heads/main/au_ah_transparant.png" width="300" align="center" />
</p>

<div align="center">
  
  ## 💦 techzs 💦
  
</div>






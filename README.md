
# Sepolia Ethereum Node Setup (Docker - Geth + Prysm)

## 📋 Requirements

- **OS**: Ubuntu 24.04+
- **RAM**: 16 GB
- **CPU**: 4 cores
- **Disk**: Up to 1 TB

## 🌐 Open Required Ports (GCP Firewall)

Allow TCP: 22, 8545, 8546, 8551, 3500, 4000, 30303  
Allow UDP: 30303

## 🛠️ Initial Setup

```bash
sudo -i
usermod -aG sudo your-username

sudo apt-get update && sudo apt-get upgrade -y
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip -y
```

## 🐳 Install Docker

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get install ca-certificates curl gnupg -y
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update && sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
sudo docker run hello-world
sudo systemctl enable docker && sudo systemctl restart docker
```

## 📂 Setup Node Directories & JWT

```bash
mkdir -p /root/ethereum/execution /root/ethereum/consensus
openssl rand -hex 32 > /root/ethereum/jwt.hex
cd /root/ethereum
```

## 🧾 Create `docker-compose.yml`

Paste this content inside `/root/ethereum/docker-compose.yml`:

[Too long for this section - retained in file]

## 🚀 Start the Node

```bash
docker compose up -d
docker compose logs -fn 100
```

## 🔐 Firewall Rules (UFW)

```bash
sudo ufw allow 22
sudo ufw allow ssh
sudo ufw allow 8545/tcp
sudo ufw allow 3500/tcp
sudo ufw allow 30303/tcp
sudo ufw allow 30303/udp
sudo ufw enable
```

## 🔍 Verify Node Sync

**Geth (Execution):**
```bash
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' http://localhost:8545
```

**Prysm (Consensus):**
```bash
curl http://localhost:3500/eth/v1/node/syncing
```

## 🔗 RPC Access (Replace with your VPS IP)

- Execution RPC (Sepolia): `http://<vps-ip>:8545`
- Consensus RPC(Beacon): `http://<vps-ip>:3500`

Note: Use public IP; `localhost` won’t work due to Docker networking.

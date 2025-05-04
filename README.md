# Self-Hosted RustDesk Server with Docker (Host Mode)

This guide provides instructions to quickly set up a self-hosted RustDesk server using Docker and Docker Compose, with services running in `host` network mode. The `docker-compose.yml` file will be cloned from your `rustdesk-setup` GitHub repository.

## Prerequisites

* A Linux server (e.g., AlmaLinux, Ubuntu, Debian).
* Docker installed. See [official Docker installation guide](https://docs.docker.com/engine/install/).
* Docker Compose installed (usually comes as a Docker plugin: `docker compose`). See [official Docker Compose installation guide](https://docs.docker.com/compose/install/).
* `git` installed (to clone the repository). If not installed, use `sudo apt install git` or `sudo dnf install git`.
* SSH key configured with your GitHub account to clone using SSH.
* A static public IP address for your server (referred to as `YOUR_SERVER_PUBLIC_IP` below).
* Root or sudo privileges.

### Clone Repository and Directory Setup

First, clone your `rustdesk-setup` GitHub repository which contains the `docker-compose.yml` file. This will also create the main deployment directory.

```bash
# Clone your repository using the SSH URL
git clone git@github.com:induslevel/rustdesk-setup.git
cd rustdesk-setup
```

### Create the data directory for RustDesk keys and configuration
```bash
mkdir -p data
```


### Fetch the public IP address (you can use other services like icanhazip.com if preferred)
```bash
PUBLIC_IP=$(curl -s ifconfig.me)

# Verify that PUBLIC_IP was fetched correctly
if [ -z "$PUBLIC_IP" ]; then
    echo "Failed to fetch Public IP. Please set it manually in docker-compose.yml."
    exit 1
fi
echo "Detected Public IP: $PUBLIC_IP"
```

### Use sed to replace the placeholder 'YOUR_SERVER_PUBLIC_IP' in docker-compose.yml
```bash
sed -i "s|command: hbbs -r YOUR_SERVER_PUBLIC_IP|command: hbbs -r $PUBLIC_IP|" docker-compose.yml

echo "docker-compose.yml has been updated with the public IP: $PUBLIC_IP"
```


### Firewall Configuration (firewalld)

```bash
sudo firewall-cmd --permanent --add-port=21114-21119/tcp
sudo firewall-cmd --permanent --add-port=21116/udp
sudo firewall-cmd --reload
```

### Starting RustDesk Server
```bash
cd rustdesk-setup # Or your chosen directory from cloning
docker compose up -d
```

### Checking Container Status
```bash
docker ps
```


### Viewing Server Logs
```bash
docker logs rustdesk_hbbs_support -f
docker logs rustdesk_hbbr_support -f
```


### Obtaining Server's Public Key
```bash
cat ./data/id_ed25519.pub 
```



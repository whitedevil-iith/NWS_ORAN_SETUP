# NWS_ORAN_SETUP
This repository will help you setup 1cu, 1du, and 1 ue with open5gs and ric.

# srsRAN with RIC, grafana and Prometheus $Setup Guide

## Prerequisites
Ensure your system is up to date before proceeding with the installation.

```bash
sudo apt-get update && sudo apt-get upgrade -y

# Install cmake and make
sudo apt-get update
sudo apt-get install -y cmake make

```

## Docker and dependencies Installation

The following script removes any existing Docker installations and installs the latest version along with srsRAN_4G dependencies:

```bash
#!/bin/bash
$
# Remove existing Docker packages
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
    sudo apt-get remove -y $pkg;
done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repository:
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker and other dependencies for srsUE_4G
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin build-essential cmake libfftw3-dev libmbedtls-dev libboost-program-options-dev libconfig++-dev libsctp-dev libzmq3-dev


# Verify Docker installation
sudo docker run hello-world

# Add user to Docker group
sudo groupadd docker
sudo usermod -aG docker $USER

# Enable Docker services
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

To apply the group changes, either reboot or run:
```bash
newgrp docker
```
Then, verify Docker runs without sudo:
```bash
docker run hello-world
```
Then, create the docker network required for the setup:
```bash
docker network create --subnet=10.0.0.0/8 oran-intel
```

# Docker Images for from 24whitedevil dockerhub repository

This repository provides the necessary commands to pull Docker images for the following projects:

- **open5gs**
- **srsue**
- **srsran**

## Pulling Docker Images

To pull the Docker images for each of the projects, you can use the following commands:

#### 1. `open5gs`
The `open5gs` Docker image is a part of the open-source 5G core network implementation.

```bash
docker pull 24whitedevil/open5gs
```
#### 2. `srsue`
The `srsue` Docker image is related to the software for user equipment (UE) in LTE/5G networks.
```bash
docker pull 24whitedevil/srsue
```
#### 3. `srsran`
The `srsran` Docker image is associated with the open-source LTE and 5G radio access network implementation.
```bash
docker pull 24whitedevil/srsran
```


### Step 1: Clone and Set Up RIC
```bash
mkdir -p RIC
cd RIC/
git clone https://github.com/srsran/oran-sc-ric.git
cd ../
```

## Running the Setup

### Terminal 1: Prepare and Start RIC from root directory
```bash
cp -f setup/oran-sc-ric/docker-compose.yml RIC/oran-sc-ric/docker-compose.yml
```
Then, start RIC:
```bash
cd RIC/oran-sc-ric
docker compose up
```

### Terminal 2: Start srsRAN from root directory
```bash
docker compose up
```

This setup ensures that all required components for RIC and srsRAN are properly installed and running.



# oai-deployment-tutorials

Prerequisites:
1. Docker ([tutorial here](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)) and docker compose ([tutorial here](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-22-04))
2. An account on hub.docker.com
3. IPv4 forwarding enabled (just run `sudo sysctl net.ipv4.conf.all.forwarding=1` and `sudo iptables -P FORWARD ACCEPT`)
4. Python3

## Installing and deploying the core network
### Step 1: pull the core network functions images
Run the following:
```
#!/bin/bash
docker login
docker pull oaisoftwarealliance/oai-amf:v1.5.1
docker pull oaisoftwarealliance/oai-nrf:v1.5.1
docker pull oaisoftwarealliance/oai-spgwu-tiny:v1.5.1
docker pull oaisoftwarealliance/oai-smf:v1.5.1
docker pull oaisoftwarealliance/oai-udr:v1.5.1
docker pull oaisoftwarealliance/oai-udm:v1.5.1
docker pull oaisoftwarealliance/oai-ausf:v1.5.1
docker pull oaisoftwarealliance/oai-upf-vpp:v1.5.1
docker pull oaisoftwarealliance/oai-nssf:v1.5.1
docker pull oaisoftwarealliance/oai-pcf:v1.5.1
docker pull oaisoftwarealliance/oai-nef:v1.5.1
# Utility image to generate traffic
docker pull oaisoftwarealliance/trf-gen-cn5g:latest

```
You will be asked for your dockerhub username and password first
### Step 2: synch the repos
Run the following
```
#!/bin/bash
git clone --branch v1.5.1 https://gitlab.eurecom.fr/oai/cn5g/oai-cn5g-fed.git
cd oai-cn5g-fed
git checkout -f v1.5.1

./scripts/syncComponents.sh

git submodule deinit --all --force
git submodule init
git submodule update

```

### Step 3: deploy the core
Go in this folder
```
cd oai-cn5g-fed/docker-compose
```
And comment out (by adding # at the beginning of the line) these lines:
```
cap_drop:
	- ALL
```
Then deploy the core network with the following command:
```
docker-compose -f docker-compose-no-privilege.yaml up
```

## Installing and running the gNB and the UE with RFSIM
### Step 1: clone the repo
```
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
cd openairinterface5g
```

### Step 2: install dependencies and build the gNB and UE
```
cd cmake_targets
./build_oai --ninja -I ./build_oai --ninja --gNB --nrUE -w SIMU -c
```

### Step 3: prepare configuration
Copy all the scripts in the `scripts` folder of this repo in `openairinterface5g/`

### Step 4: deploy gNB and UE
In one terminal `./run_gnb.sh` , wait a few seconds, and then in another terminal `./run_ue.sh`

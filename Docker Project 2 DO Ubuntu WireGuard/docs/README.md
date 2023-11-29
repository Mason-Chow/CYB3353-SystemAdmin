# Cloud VPN-Docker Project

## Create a Digital Ocean Ubuntu droplet

Create a Droplet using a template from Droplet Marketplace: Docker 23.06 on Ubuntu 22.04

The droplet was configured as follows:
Cheapest droplet: $4/mo
- Ubuntu 20.04
- Basic
- Regular Intel CPU

SSH Key option
root password: `set root password`

no other configuration needed

## Install Docker

Preinstalled with Droplet

## Install WireGuard

Followed tutorial at [theMatrixDev](https://thematrix.dev/setup-wireguard-vpn-server-with-docker/).

Additional tutorial at [Smart Home Beginner](https://www.smarthomebeginner.com/wireguard-docker-compose-guide-2023/) helped with personal understanding.

### Create directories and yml file
```bash
mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml
```

Copy and paste this for the yml block
```
version: '3.9'
services:
  wireguard:
    container_name: Wireguard
    image: linuxserver/wireguard:latest
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=147.182.234.97 # Droplet's public IP
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```

### Start the WireGuard Docker Container
run `sudo docker compose up -d`

## Find and connect mobile device
run `docker compose logs -f wireguard` to get the QR code for phone1, then open the Wireguard VPN app on your mobile device, create a new tunnel, and select `Create from QR code`. 

Screenshots of device ip address before and after activating the tunnel
![Mobile before](./assets/Mobile%before.png "Before tunnel activated")

## Find and connect laptop
copy the file `peer_pc1` from `~/wireguard/config/peer_pc1` to your PC and use it to set up a tunnel on the [WireGuard app](https://www.wireguard.com/install/). Once your tunnel is set up, click activate.

Screenshots of tunnel working, before screen on the left and after screen on the right.


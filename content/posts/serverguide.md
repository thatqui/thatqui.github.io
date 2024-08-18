+++
title = 'Server Guide'
date = 2024-08-18T16:32:24+03:00
draft = false
+++


Hello! Are you tired of companies like Google and Microsoft? This guide is for you!

# Requirements

## Domain name
Domains are annual. Personally, I bought mine from GoDaddy and I'm using it comfortably.

## Server
I know servers are expensive. However, you can get a VPS for less than $1 for your first year at OVHCloud.

My recommendation is to get a server with Debian 12. The guide will be based on this, you can adapt it to other distros if you want.

## Linux Knowledge
If you don't have knowledge, I do not recommend entering this field.

# What We Will Do

* A Site
* Git Server

# Getting Started

If you have both, we can start the process.

First, connect to your server via SSH; your password will be in the link sent to your email.

# Changing Password

```
sudo passwd $USER # change user password
sudo passwd root # change root password
```

# Update

```
sudo apt update # update repositories
sudo apt upgrade # update the system
```


# Linking Domain with Server

First, find the server's IP address using `ip a`; in OVH, it usually starts with 51.

Then, go to the DNS records section of your domain.

Create an A record with the name `@` and the value of your IP address. For example:

```
Type: A
Name: @
Value: Your IP Address
```


And we will create many subdomains. Subdomains are the parts before the domain. For example, mail.google.com is a subdomain, google.com is the domain.

For subdomains: Add a CNAME record for the subdomain. For example, in this guide, git and bin subdomains will be used, you can add these records:

```
CNAME git Your Main Domain
CNAME bin Your Main Domain
```


# Docker Installation

https://docs.docker.com/engine/install/debian

## Docker Rootless Installation

```
sudo systemctl disable --now docker
dockerd-rootless-setuptool.sh install
systemctl --user enable --now docker
echo 'export PATH="$HOME/.bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```


# Caddy Installation

We will install Caddy using Docker.


```
mkdir ~/caddy
cd ~/caddy
```

docker-compose.yml file:

```
version: "3.7"

services:
  caddy:
    image: caddy:latest
    restart: unless-stopped
    network_mode: host
    cap_add:
      - NET_ADMIN
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes:
      - $PWD/Caddyfile:/etc/caddy/Caddyfile
      - $PWD/site:/srv
      - caddy_data:/data
      - caddy_config:/config

volumes:
  caddy_data:
    external: true
  caddy_config:
```

```
nano Cadddyfile 
```

```
example.com {
        root * /srv
        file_server
}
```

Change example.com with your domain.

Upload your site to ~/caddy/site folder.

```
docker-compose up -d
```

You might encounter an error, which might ask you to add something to `/etc/sysctl.conf`. Add the required line and run `sudo sysctl --system`. Then, run the previous command again.

# Git Server Installation

We'll use forgejo.

```
mkdir ~/forgejo
cd ~/forgejo
```

```
nano docker-compose.yml
```


Find the Docker Compose file on the https://forgejo.org/docs/latest/admin/installation-docker page.

```
docker-compose up -d
```

```
cd ~/caddy
nano Caddyfile
```

```
git.example.com {
        reverse_proxy localhost:3000
}
```


Then save and run `docker compose restart`.

# Setting Up a Pastebin Alternative

I'll only give compose file, you can do all steps yourself.

```
version: '3.3'
services:
        pastebin:
                image: wantguns/bin
                container_name: pastebin
                ports:
                        - 6163:6163
                environment:
                        - BIN_PORT=6163 # Defaults to 6162
                        - BIN_LIMITS={form="16 MiB"}
                        - BIN_CLIENT_DESC=placeholder
                volumes:
                        - ./upload:/upload # upload folder will have your pastes
```

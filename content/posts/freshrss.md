+++
title = 'Create your newsletter app: FreshRSS'
date = 2024-08-18T16:37:57+03:00
draft = false
+++

# Docker Installation

https://docs.docker.com/engine/install

- And install docker compose too.

# FreshRSS Install

```
mkdir ~/freshrss
cd ~/freshrss
nano docker-compose.yml
```

Paste this code to docker-compose.yml file:

```
name: freshrss
services:
    freshrss:
        restart: unless-stopped
        logging:
            options:
                max-size: 10m
        ports:
            - 5050:80
        environment:
            - TZ=<yourtimezone>
            - CRON_MIN=1,31
        volumes:
            - freshrss_data:/var/www/FreshRSS/data
            - freshrss_extensions:/var/www/FreshRSS/extensions
        container_name: freshrss
        image: freshrss/freshrss
volumes:
    freshrss_data:
        external: true
        name: freshrss_data
    freshrss_extensions:
        external: true
        name: freshrss_extensions
```

And customize TZ variable.

```
docker compose up -d # or docker-compose up -d
```

And, you can customize your reverse proxy or access FreshRSS with 5050 port.

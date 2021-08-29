# My Traefik reverse-proxy config

## Setup

Create docker network named `traefik`

```bash
docker network create traefik

```

Add Cloudflare API token from My Profile > API Tokens.
The required permissions are `Zone.Zone, Zone.DNS`.
Then paste the token to `.env`

```bash
echo "CLOUDFLARE_DNS_API_TOKEN=<enter cloudflare token>" >> .env
```

## Run

Simply execute 

```bash
docker-compose up -d
```

## Adding services

Each service that you want to expose needs to be added to previously created network `traefik`.

```yml
# add service to the network
services:
  <service-name>:
    networks:
      - traefik

# define the external network
networks:
  traefik:
    external: true
```


To expose the service under particular domain it needs to be defied by labels:

```yml
    labels:
      - traefik.enable=true
      - "traefik.http.routers.<service-name>.rule=Host(`subdomain.stan.bar`)"
      - traefik.http.routers.<service-name>.tls=true
      - traefik.http.routers.<service-name>.tls.certresolver=myresolver

      # if there is more than one port exposed, you need to specify which one you want to forward to 80/443
      - "traefik.http.services.<service-name>.loadbalancer.server.port=8080"
```

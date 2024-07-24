# vps-setup
Bootstrap a VPS. Run everything in docker.

## DNS
1. Add an A record for the name @ (the root of the domain) pointing to the IP address of the VPS.
2. Add a C record for the name * (wildcard subdomains) pointing to the domain name.

## Firewall
Accept TCP on port 80 and 443.

## Docker Compose
1. Clone this repository
2. Create `.env` file in project root and set `LETSENCRYPT_EMAIL=<abc@xyz.com>`
3. Run `docker compose [-f compose-file.yml] <up -d,--detach | down> [service]`

All compose files should be stored in the project root directory so Docker Compose treats them as part of the same
project and thus puts them in the same docker network.

It is advisable to start the nginx services before starting additional services.

Prefer to use named volumes for persistent storage.

## Reverse Proxy
https://github.com/nginx-proxy/nginx-proxy

nginx-proxy sets up a container running nginx and docker-gen.
docker-gen generates reverse proxy configs for nginx and reloads nginx when containers are started and stopped.

Requests to http://foo.bar.com will be routed to a container with the VIRTUAL_HOST env var set to foo.bar.com

When a container exposes only one port, nginx-proxy will default to this port, else to port 80.
A different port can be set using the VIRTUAL_PORT env var. This variable cannot be set to more than one port
(see [Virtual Ports](https://github.com/nginx-proxy/nginx-proxy/tree/main/docs#virtual-ports)).

## SSL
https://github.com/nginx-proxy/acme-companion

acme-companion handles automated creation, renewal and use of LetsEncrypt SSL certificates for proxied Docker containers
through the ACME protocol.

Once both nginx-proxy and acme-companion containers are running, start the container you want proxied with
environment variables VIRTUAL_HOST and LETSENCRYPT_HOST both set to the domain(s) your proxied container is going to use.

## Portainer
https://docs.portainer.io/advanced/reverse-proxy/nginx



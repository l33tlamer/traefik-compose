# traefik-compose

A very simple but working template of Traefik

Using Lets Encrypt with dns-01 challenge, no need to open any ports

# Setup

Edit `required/traefik.yml` and modify the domain to use for Lets Encrypt and the domain DNS provider to use

If you want to proxy services that are not running on the same Docker host as Traefik itself, or on completely different machines (like a VM):

Edit `required/fileConfig.yml` and uncomment the example of Home Assistant for both `routers` and `services` entry.

# Run before starting:

Create Docker network:

`docker network create traefikproxy --attachable -d bridge --subnet 172.69.69.0/24 --gateway 172.69.69.1`

Create empty required file:

`touch required/acme.json`

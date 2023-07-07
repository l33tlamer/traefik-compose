# traefik-compose

A very simple but working template of Traefik

Using Lets Encrypt with dns-01 challenge, no need to open any ports

# Setup

Edit `docker-compose.yml` and modify the dashboard domain and the token variable to fit whatever dns-01 provider to use

Edit `required/traefik.yml` and modify the domain to use, and further below modify the dns-01 provider to use

To proxy services that are not running on the same Docker host as Traefik itself, or on completely different machines (like a VM):

Edit `required/fileConfig.yml` and uncomment the example of Home Assistant for both `routers` and `services` entry.

# Run before starting the first time:

Create Docker network, adjust however you want:

`docker network create traefikproxy --attachable -d bridge --subnet 172.69.69.0/24 --gateway 172.69.69.1`

Every Docker service that you want to proxy needs to be a member of this network

Create empty required file, otherwise Docker mapping fails:

`touch required/acme.json`

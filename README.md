# traefik-compose

A very simple but working template of a Traefik & Lets Encrypt setup with Docker compose

# Run before starting:

Create Docker network:

`docker network create traefikproxy --attachable -d bridge --subnet 172.69.69.0/24 --gateway 172.69.69.1`

Create folder and required file:

`mkdir required`

`touch required/acme.json`

Edit `required/traefik.yml` and modify the domain to use for Lets Encrypt and the domain DNS provider to use

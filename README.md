# traefik-compose
Working template setup of Traefik with docker compose

to run before starting:

`docker network create traefikproxy --attachable -d bridge --subnet 172.69.69.0/24 --gateway 172.69.69.1`

`mkdir required`

`touch required/acme.json`


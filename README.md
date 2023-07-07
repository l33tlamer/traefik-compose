# traefik-compose

A very simple but working template of [Traefik](https://traefik.io/) reverse proxy

Using [Lets Encrypt](https://letsencrypt.org/) with [dns-01 challenge](https://doc.traefik.io/traefik/https/acme/#providers), no need to open any ports

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

# Proxying services

See folder `example-service` for a example docker-compose.yml of a service that gets proxied with this Traefik setup.

The only important parts are the labels, you attach those to the container and Traefik will read them.

Once Traefik is running like this, you never have to touch it again. You only modify labels on new containers to proxy.

These are all that is needed:

    labels:
      - traefik.enable=true
      - traefik.docker.network=traefikproxy
      - traefik.http.routers.CHANGEME.rule=Host(`CHANGEME.example.com`)   # change name
      - traefik.http.services.CHANGEME.loadbalancer.server.port=80        # change name AND port

Simply replace CHANGEME with the name of the service and subdomain you want to use.

Important: The port needs to be the INTERNAL container port of the service you are proxying, so for example just 80.
Not 8080 if you would map it in Docker, that is only for the Docker host. Traefik talks directly to the service so
the internal port is used for that. See the example service.

After adding a new proxied service and starting it, give it a little bit of time. It can take almost a minute for Traefik to
fully add the new service and actually work, dont expect it to be instant.

# EOF

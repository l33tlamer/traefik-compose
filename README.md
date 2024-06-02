# traefik-compose

A very simple but working template of [Traefik](https://traefik.io/) reverse proxy.

Using [Lets Encrypt](https://letsencrypt.org) with the [DNS-01 challenge](https://doc.traefik.io/traefik/https/acme/#providers), no need to open any ports.

This example uses [DeSEC](https://www.desec.io) as DNS provider. You can easily adapt it to use another, like [Cloudflare](https://www.cloudflare.com).

It also is setup to use Docker container labels for the proxy-targets to make future deployments much simpler.

# Modify configs

Edit `docker-compose.yml` and adjust (sub)domain and the token variable for your DNS-01 provider

Edit `required/traefik.yml` and adjust (sub)domain, and modify the DNS-01 provider

To proxy services that are not running on the same Docker host as Traefik itself, or on completely different machines:

Edit `required/fileConfig.yml` and see the example of Home Assistant for both `routers` and `services` entries.

# Before starting the first time:

Create a dedicated Docker network for the proxy, adjust however you want:

`docker network create traefikproxy --attachable -d bridge --subnet 172.69.69.0/24 --gateway 172.69.69.1`

If you use a different network name, then you also need to change that in `required/traefik.yml`.

Every Docker service that you want to proxy needs to be a member of this network.

Create empty required file, otherwise Docker mapping fails:

`touch required/acme.json`

Then you are ready to start the Traefik container:

`docker compose up -d`

Check the output with:

`docker logs traefik`

Access the Traefik WebUI at port 8080.

Once Traefik is running like this, you never have to touch it again. You only modify labels on new containers to proxy.

# Proxying services

See folder `example-service` for a docker-compose.yml of a service that gets proxied with this Traefik setup.

The only important bits are that the container is a member of the proxy network, and has those specific labels.

These are all that is needed:

    labels:
      - traefik.enable=true
      - traefik.docker.network=traefikproxy                               # name of Docker network
      - traefik.http.routers.CHANGEME.rule=Host(`SUBDOMAIN.example.com`)  # adjust to fit your (sub)domain
      - traefik.http.services.CHANGEME.loadbalancer.server.port=80        # adjust to fit the internal target port
      - traefik.http.services.CHANGEME.loadbalancer.server.scheme=https   # only use when target is HTTPS

The CHANGEME part must be unique for each service you deploy for Traefik, simply use the name of the service.

IMPORTANT: The port needs to be the INTERNAL container port of the service you are proxying, so for example just 80.
Not 8080 if you would map it in Docker, those are only for the Docker host. But Traefik talks directly to the service
so the internal port is used for that. This is also why mapping ports to the Docker host is not required when using a
reverse proxy. See the example service file where its port 3000.

In the future, whenever you deploy a new service and you want to proxy it, all you need to do is add the service to
the proxy network and copy/paste those lines for the labels. No need to touch Traefik configs or to restart Traefik.

After adding a new proxied service and starting it, give it a little bit of time.
It can take ~30sec sometimes for Traefik to recognize a fresh container and start proxying for it.

In addition to all this, you need a proper DNS setup that points your `subdomain.example.com` of a service to the
IP of this Traefik instance, do NOT point it at the IP of the service.

# EOF

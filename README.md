# services-swarm

Docker Swarm deployment for openSUSE Brazil services

## Deployment

The deployment must follow an order, as services depend on external configuration.

### Volume

Create a volume named `uptime-data`.

```bash
docker volume create --name uptime-data --opt type=none --opt device=/srv/data/uptime --opt o=bind
```

### Network

Create a network for Traefik and all services.

```bash
docker network create --driver overlay traefik
```

### Configuration

Create a config named `cf-dns-api-token` that contains the [CF API Token](https://developers.cloudflare.com/api/tokens/create/).

```bash
docker config create cf-dns-api-token cf-dns-api-token.txt
```

Create a config named `traefik-users-file` that contains the `user:password` for Traefik Web UI.

```bash
docker config create traefik-users-file traefik-users-file.txt
```

> Use `htpasswd -n <user>` to create an encoded password.

### Deployment

Deploy Traefik first as other services may depend on it.

```bash
docker stack deploy -c traefik.yml traefik
```

Deploy CronJob as other services may depend on it.

```bash
docker stack deploy -c cronjob.yml cronjob
```

Deploy services.

```bash
docker stack deploy -c services.yml services
```

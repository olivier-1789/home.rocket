# home.rocket

Infrastructure GitOps du Raspberry Pi 5 "rocket".  
Tout push sur `main` déclenche un déploiement automatique via GitHub Actions.

## Stacks

| Stack | Description | Ports |
|-------|-------------|-------|
| `portainer-stack` | Gestion des containers Docker | 8000, 9443 |
| `adguardhome-stack` | DNS + blocage de publicités | host |
| `nginx-proxy-manager-stack` | Reverse proxy + SSL | 80, 81, 443 |
| `wikijs-stack` | Base de connaissances | 3001 |
| `homepage-stack` | Dashboard | 3002 |

## Déploiement

### Prérequis (secrets GitHub Actions)

| Secret | Description |
|--------|-------------|
| `TAILSCALE_AUTHKEY` | Clé d'auth Tailscale ephemeral |
| `PI_HOST` | IP Tailscale du RPi |
| `SSH_PRIVATE_KEY` | Clé privée SSH pour l'utilisateur `rocket` |

### Premier déploiement

```bash
# Sur le RPi, créer le fichier .env pour Wiki.js
cp wikijs-stack/.env.example wikijs-stack/.env
nano wikijs-stack/.env

# Lancer chaque stack
docker compose -f portainer-stack/docker-compose.yml up -d
docker compose -f adguardhome-stack/docker-compose.yml up -d
docker compose -f nginx-proxy-manager-stack/docker-compose.yml up -d
docker compose -f wikijs-stack/docker-compose.yml up -d
docker compose -f homepage-stack/docker-compose.yml up -d
```

### Mise à jour automatique

Chaque push sur `main` redéploie automatiquement la `homepage-stack`.  
Les autres stacks se mettent à jour manuellement ou via Portainer.

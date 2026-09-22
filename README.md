# Déploiement automatique d'un conteneur avec GitHub Actions

Ce projet fournit un exemple minimal :

- API Flask (`/` et `/health`)
- Image Docker construite et publiée sur `ghcr.io`
- Déploiement automatique sur une machine cible Linux via SSH

## 1) Prérequis machine cible

Sur la machine cible (VM/serveur) :

- Docker et Docker Compose plugin installés
- Utilisateur SSH autorisé à exécuter Docker
- Port `8080` ouvert

## 2) Secrets GitHub à configurer

Dans `Settings > Secrets and variables > Actions > New repository secret` :

- `SSH_HOST` : IP/hostname de la machine cible
- `SSH_USER` : utilisateur SSH
- `SSH_PRIVATE_KEY` : clé privée SSH (format OpenSSH)
- `SSH_PORT` : port SSH (optionnel, sinon `22`)
- `REMOTE_APP_DIR` : dossier cible (optionnel, sinon `/opt/demo-app`)
- `GHCR_USERNAME` : compte qui peut lire `ghcr.io` (souvent votre user GitHub)
- `GHCR_TOKEN` : PAT GitHub avec permission `read:packages`

## 3) Fonctionnement de la pipeline

Fichier : `.github/workflows/deploy.yml`

Sur push de `main` (ou déclenchement manuel) :

1. Build l'image Docker
2. Push sur `ghcr.io/<owner>/<repo>:latest`
3. Copie `docker-compose.prod.yml` sur la machine cible
4. Exécute `docker compose pull && docker compose up -d`

## 4) Lancer en local

```bash
docker build -t demo-web .
docker run -p 8080:8080 demo-web
```

Puis tester :

- `http://localhost:8080/`
- `http://localhost:8080/health`

## 5) Déclencher un déploiement

- Faites un push sur `main`
- Ou lancez le workflow via l'onglet **Actions**

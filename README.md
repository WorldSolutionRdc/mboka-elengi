# Proxy Nginx pour VPS (161.35.91.8)

Proxy Nginx déployé sur Google Cloud Run pour rediriger le trafic TCP vers le VPS principal.

## Configuration
| Élément | Valeur |
|---------|--------|
| **VPS cible** | `161.35.91.8:443` |
| **Port d'écoute proxy** | `8080` |
| **Région VPS** | `europe-west4` (Amsterdam, Pays-Bas) |
| **Région Cloud Run** | `europe-west1` (Belgique) |
| **Type de proxy** | TCP Stream (Layer 4) |

## Déploiement
```bash
gcloud run deploy ultra-speed-proxy \
  --source . \
  --platform managed \
  --region europe-west1 \
  --allow-unauthenticated \
  --port 8080 \
  --memory 512Mi \
  --cpu 1 \
  --timeout 3600

# Proxy Nginx pour VPS (167.99.88.149)

Proxy Nginx déployé sur Google Cloud Run pour rediriger le trafic TCP vers le VPS principal.

## Configuration
| Élément | Valeur |
|---------|--------|
| **VPS cible** | `167.99.88.149:443` |
| **Port d'écoute proxy** | `8080` |
| **Région Cloud Run** | `europe-west2` (Londres, UK) |
| **Type de proxy** | TCP Stream (Layer 4) |
| **Usage** | Tunnel pour Xray/3x-ui (VPN) |

## Optimisations (économies de crédits)
| Paramètre | Valeur | Impact |
|-----------|--------|--------|
| `min-instances` | `0` | Pas d'instance inactive = économie |
| `max-instances` | `2` | Limite les pics de consommation |
| `concurrency` | `50` | Réduit le scaling inutile |
| `timeout` | `300` | Libère les ressources rapidement |
| `memory` | `256Mi` | Minimum requis |

## Fichiers
- `nginx.conf` : Configuration Nginx optimisée (keepalive, retries, timeouts)
- `Dockerfile` : Image Nginx:alpine avec copie de la config
- `README.md` : Ce fichier

## Déploiement sur Cloud Run
```bash
gcloud run deploy v2ray-proxy \
  --source . \
  --platform managed \
  --region europe-west2 \
  --allow-unauthenticated \
  --port 8080 \
  --memory 256Mi \
  --cpu 1 \
  --timeout 300 \
  --min-instances 0 \
  --max-instances 2 \
  --concurrency 50

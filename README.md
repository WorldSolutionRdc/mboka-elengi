# Proxy Nginx pour VPS Amsterdam (161.35.91.8)

Proxy Nginx déployé sur Google Cloud Run pour rediriger le trafic TCP vers le VPS principal.

## Configuration
| Élément | Valeur |
|---------|--------|
| **VPS cible** | `161.35.91.8:443` |
| **Port d'écoute proxy** | `8080` |
| **Région Cloud Run** | `europe-west1` (Belgique) |
| **Type de proxy** | TCP Stream (Layer 4) |

## Optimisations (économies de crédits)
| Paramètre | Valeur | Impact |
|-----------|--------|--------|
| `min-instances` | `0` | Pas d'instance inactive = économie |
| `max-instances` | `2` | Limite les pics de consommation |
| `concurrency` | `50` | Réduit le scaling inutile |
| `timeout` | `300` | Libère les ressources rapidement |
| `memory` | `256Mi` | Minimum requis |

## Déploiement sur Cloud Run (Belgique)
```bash
gcloud run deploy v2ray-proxy \
  --source . \
  --platform managed \
  --region europe-west1 \
  --allow-unauthenticated \
  --port 8080 \
  --memory 256Mi \
  --cpu 1 \
  --timeout 300 \
  --min-instances 0 \
  --max-instances 2 \
  --concurrency 50

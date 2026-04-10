# Proxy Nginx pour VPS (161.35.91.8)

Proxy Nginx déployé sur Google Cloud Run pour rediriger le trafic TCP vers le VPS principal.

## Configuration
| Élément | Valeur |
|---------|--------|
| **VPS cible** | `161.35.91.8:443` |
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

Dockerfile :
FROM nginx:alpine
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]

nginx :
worker_processes auto;

events {
    worker_connections 2048;
    multi_accept on;
    use epoll;
}

stream {
    upstream backend {
        server 167.99.88.149:443 max_fails=3 fail_timeout=30s;
    }
    
    server {
        listen 8080 reuseport;
        proxy_pass backend;
        proxy_connect_timeout 10s;
        proxy_timeout 1800s;
        proxy_next_upstream on;
        proxy_next_upstream_tries 3;
        proxy_socket_keepalive on;
        tcp_nodelay on;
    }
}
Je vous ai fourni dockerfile et nginx pour que vous ayez l'idée, c'est readme qu'il faut décorer 

<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Proxy Nginx - Documentation</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', 'Fira Code', 'Courier New', monospace;
            background: linear-gradient(135deg, #0a0f1e 0%, #0e1a2a 100%);
            color: #e0e0e0;
            padding: 2rem;
            line-height: 1.6;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: rgba(18, 25, 45, 0.7);
            backdrop-filter: blur(10px);
            border-radius: 32px;
            padding: 2rem;
            box-shadow: 0 25px 45px rgba(0,0,0,0.4), 0 0 0 1px rgba(72, 187, 255, 0.15);
            transition: all 0.3s ease;
        }

        h1 {
            font-size: 2.8rem;
            background: linear-gradient(120deg, #60efff, #00b4ff, #ff6b6b);
            background-size: 200% auto;
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            animation: shine 6s linear infinite;
            margin-bottom: 0.5rem;
            letter-spacing: -0.02em;
        }

        @keyframes shine {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .badge-container {
            margin: 1.5rem 0;
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
        }

        .badge {
            padding: 6px 16px;
            border-radius: 40px;
            font-size: 0.8rem;
            font-weight: 600;
            transition: transform 0.2s, box-shadow 0.2s;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            backdrop-filter: blur(4px);
        }

        .badge:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 14px rgba(0,0,0,0.3);
        }

        .badge-green { background: rgba(0, 255, 150, 0.2); border-left: 3px solid #00ff96; color: #aaffdd; }
        .badge-blue { background: rgba(0, 180, 255, 0.2); border-left: 3px solid #00b4ff; color: #bbf0ff; }
        .badge-purple { background: rgba(156, 80, 255, 0.2); border-left: 3px solid #9c50ff; color: #dbbcff; }
        .badge-orange { background: rgba(255, 107, 107, 0.2); border-left: 3px solid #ff6b6b; color: #ffcdb0; }

        .card {
            background: rgba(10, 20, 30, 0.6);
            border-radius: 24px;
            padding: 1.5rem;
            margin: 2rem 0;
            border: 1px solid rgba(96, 239, 255, 0.2);
            transition: all 0.25s;
            backdrop-filter: blur(4px);
        }

        .card:hover {
            border-color: #60efff;
            box-shadow: 0 8px 20px rgba(96, 239, 255, 0.1);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            border-radius: 20px;
            overflow: hidden;
            margin: 1rem 0;
        }

        th, td {
            padding: 12px 18px;
            text-align: left;
            border-bottom: 1px solid rgba(96, 239, 255, 0.2);
        }

        th {
            background: rgba(0, 180, 255, 0.2);
            font-weight: 700;
            letter-spacing: 0.5px;
            color: #bbf0ff;
        }

        tr:hover td {
            background: rgba(96, 239, 255, 0.05);
        }

        pre {
            background: #0a0f1e;
            border-radius: 20px;
            padding: 1.2rem;
            overflow-x: auto;
            font-family: 'Fira Code', 'Courier New', monospace;
            font-size: 0.85rem;
            border-left: 4px solid #00b4ff;
            margin: 1rem 0;
            box-shadow: inset 0 0 8px rgba(0,0,0,0.5);
        }

        code {
            font-family: 'Fira Code', monospace;
            background: rgba(0, 180, 255, 0.15);
            padding: 3px 8px;
            border-radius: 12px;
            font-size: 0.85rem;
            color: #ffd966;
        }

        .icon {
            font-size: 1.2rem;
            vertical-align: middle;
        }

        .footer-note {
            text-align: center;
            margin-top: 3rem;
            font-size: 0.8rem;
            opacity: 0.7;
            border-top: 1px dashed rgba(96, 239, 255, 0.3);
            padding-top: 1.5rem;
        }

        @keyframes pulse {
            0% { opacity: 0.6; }
            100% { opacity: 1; }
        }

        .vps-ip {
            background: linear-gradient(135deg, #0f172a, #1e293b);
            padding: 6px 18px;
            border-radius: 80px;
            font-family: monospace;
            font-weight: bold;
            letter-spacing: 1px;
            border: 1px solid #60efff;
            display: inline-block;
            box-shadow: 0 0 6px #60efff88;
        }
    </style>
</head>
<body>
<div class="container">
    <h1>🛡️ Proxy Nginx · tunnel TCP</h1>
    <div style="margin-bottom: 1rem;">
        <span class="vps-ip">🎯 VPS cible : 161.35.91.8:443</span>
    </div>
    <div class="badge-container">
        <div class="badge badge-green">🚀 Cloud Run · europe-west2</div>
        <div class="badge badge-blue">⚡ TCP Stream (L4)</div>
        <div class="badge badge-purple">🔐 Xray / 3x-ui ready</div>
        <div class="badge badge-orange">💰 économies actives</div>
    </div>

    <!-- Configuration principale -->
    <div class="card">
        <h2 style="display: flex; gap: 10px; align-items: center; margin-bottom: 1rem;">📡 <span>Configuration du proxy</span></h2>
        <table>
            <tr><th>Élément</th><th>Valeur</th></tr>
            <tr><td>🎯 VPS cible</td><td><code>161.35.91.8:443</code></td></tr>
            <tr><td>📡 Port d’écoute proxy</td><td><code>8080</code></td></tr>
            <tr><td>🌍 Région Cloud Run</td><td><code>europe-west2</code> (Londres, UK)</td></tr>
            <tr><td>🔄 Type de proxy</td><td>TCP Stream (Layer 4)</td></tr>
            <tr><td>🔌 Usage</td><td>Tunnel pour Xray / 3x-ui (VPN)</td></tr>
        </table>
    </div>

    <!-- Optimisations -->
    <div class="card">
        <h2 style="display: flex; gap: 10px;">⚙️ <span>Optimisations & économies de crédits</span></h2>
        <table>
            <tr><th>Paramètre</th><th>Valeur</th><th>Impact</th></tr>
            <tr><td><code>min-instances</code></td><td><strong>0</strong></td><td>✅ Pas d'instance inactive = économie</td></tr>
            <tr><td><code>max-instances</code></td><td><strong>2</strong></td><td>📉 Limite les pics de consommation</td></tr>
            <tr><td><code>concurrency</code></td><td><strong>50</strong></td><td>🔄 Réduit le scaling inutile</td></tr>
            <tr><td><code>timeout</code></td><td><strong>300</strong></td><td>⏱️ Libère les ressources rapidement</td></tr>
            <tr><td><code>memory</code></td><td><strong>256Mi</strong></td><td>🧠 Minimum requis</td></tr>
        </table>
    </div>

    <!-- Fichiers -->
    <div class="card">
        <h2>📁 Structure des fichiers</h2>
        <pre>
<code>📄 nginx.conf</code>   → Configuration Nginx optimisée (keepalive, retries, timeouts)
<code>🐳 Dockerfile</code>    → Image Nginx:alpine + copie de la config
<code>📖 README.md</code>    → Documentation complète
        </pre>
    </div>

    <!-- Dockerfile -->
    <div class="card">
        <h2>🐳 Dockerfile</h2>
        <pre><code>FROM nginx:alpine
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]</code></pre>
    </div>

    <!-- nginx.conf -->
    <div class="card">
        <h2>⚙️ nginx.conf (stream TCP)</h2>
        <pre><code>worker_processes auto;

events {
    worker_connections 2048;
    multi_accept on;
    use epoll;
}

stream {
    upstream backend {
        server 161.35.91.8:443 max_fails=3 fail_timeout=30s;
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
}</code></pre>
    </div>

    <!-- Déploiement -->
    <div class="card">
        <h2>☁️ Déploiement sur Cloud Run</h2>
        <pre><code>gcloud run deploy v2ray-proxy \
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
  --concurrency 50</code></pre>
        <div class="badge-container" style="margin-top: 1rem;">
            <div class="badge badge-green">✅ Scaling à 0 possible</div>
            <div class="badge badge-blue">🧪 Ready pour production</div>
        </div>
    </div>

    <div class="footer-note">
        ⚡ Proxy Nginx · flux sécurisé · tunnel VPN résilient — maintenu avec <span style="color:#ff6b6b;">❤️</span> et économies actives.
    </div>
</div>
</body>
</html>

# Ultima Atividade

## Setup Mission Control — Etapa 3 (Em andamento)

**Data:** 25/04/2026 14:03
**Status:** Nginx config corrigida, pendente aplicar e testar

### O que foi feito
- Build estatico do Mission Control em /var/www/mission-control/ — ok
- Nginx config analisada em /etc/nginx/sites-enabled/api_bling
- Problemas identificados:
  - `try_files` com `$uri/` causa 301 em rotas SPA
  - location `/api/` aponta pra PostgREST mas sem rewrite do prefixo `/v1/`
  - CORS headers fora do server block (escopo global)
- Backup salvo em api_bling.bak
- PostgREST direto em 10.0.2.7:3000 responde (agents, squads funcionando)
- Dominio com Cloudflare dando 523 (VPS IP 217.216.67.196, Cloudflare IPs 104.21.3.250)

### Proximo passo
Aplicar a nova config do nginx (try_files corrigido, location /rest com rewrite, CORS no server block), testar com nginx -t, reload, verificar SPA fallback e resolver Cloudflare 523.

### Contexto
- PostgREST 10.0.2.7:3000
- nginx serve build estatico com `root /var/www; try_files $uri /mission-control/index.html`
- Coolify na porta 8000 (proxy /)
- Google OAuth na porta 3099 (/auth/)

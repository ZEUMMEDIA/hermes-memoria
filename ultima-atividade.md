# Ultima Atividade — 25/04/2026 ~14:50 BRT

## Resumo
Corrigi todos os 6 gaps do ecossistema Hermes em uma sessão contínua.

## O que foi feito

### #108 — Loop DRE (order_generator)
- `needs_new_order()` recriava ordens em loop porque buscava por nome original da categoria (ex: "analise-vendas") mas ordens são criadas com categoria mapeada (ex: "marketing")
- Agora usa `CATEGORY_MAP` para buscar ordem ativa existente antes de criar
- Também verifica se já existe ordem `pending`/`in_progress` — se sim, não cria nova

### #107 — KPIs nunca publicados
- `operational.py` não publicava KPIs — último registro era 06:01 (provavelmente manual)
- Adicionado `publish_kpis()` que roda a cada ciclo, independente de ordens pendentes
- Usa `source="system"` (único valor aceito pela constraint do BD)
- Publica cycles_run, orders_processed, status

### #109 — Delegation Bridge (reescrita v3)
- Bridge antiga só preparava arquivos `.md`/`.json` que ninguém lia
- Reescrita como supervisor que detecta ordens `in_progress` por >30min
- Cria ordens críticas (`priority=critical`) e insights de alerta
- Busca case-insensitive para detecção de duplicatas
- Valida contra constraints do BD (source, category)

### #110 — Squad Dev cron
- Cron original `0 4 * * *` nunca rodou (`last_run_at: null`)
- Removido e recriado com `every 30m`
- Verificação manual: nginx 200, PostgREST ok, disco 30%, RAM ok
- Insight de saúde publicado no PostgREST

### #111 — Metas órfãs
- 3 metas sem ordens: margem 20%, Bling-ML sync, DRE automatizada
- Criadas ordens #157 (margem/marketing/critical), #158 (Bling-ML/dev/high), #159 (DRE/financeiro/high)
- Tasks #115-#117 no board

### Strategic.py — Dedup
- A cada ciclo de 30min, o LLM gerava insights de alerta sobre ordens estagnadas (ordens #66, #68, #80, #84, #96) — 16 insights duplicados
- Agora: pula insights com "estagnad"/"stuck" no título (monitorado pela bridge)
- Verifica título duplicado nos insights recentes antes de salvar
- Squad priorities só cria se não existir ordem ativa do squad

### Board.py — Upgrade
- Adicionado comando `sync`: espelha ordens do PostgREST como tasks no board
- Adicionado `--all`: mostra ordens do sistema além das tasks manuais
- Detecção de duplicatas por título+squad

## Estado Atual do Board
- Tasks 1-48: originais (muitas escaladas de ciclos antigos)
- Tasks 106-118: tasks criadas nesta sessão
- #112, #107, #108, #109, #110, #111: done
- #106, #115, #116, #117: criadas/backlog
- #118: verificação final do ciclo (to_do)
- #99: Bling-ML sync (ainda pendente — depende de gateway OAuth)

## Próximos Passos
1. Verificar task #118: ciclo fecha corretamente (rodar daqui 30min)
2. Resolver #99: Bling-ML sync (gateway OAuth precisa ser reiniciado)
3. Limpar ordens escaladas antigas (#1-#57, todas duplicatas de Bling-ML/marketing/ops)
4. Squad Dev cron será executado a cada 30min — verificar se roda corretamente

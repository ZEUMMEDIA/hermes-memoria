# Ultima Atividade — 25/04/2026 15:45

## LLM Router — Cada agente com seu modelo de IA

### O que foi feito

Criado o **LLM Router** (`/root/hermes-heartbeat/llm_router.py`) que centraliza
todas as chamadas de LLM do ecossistema Hermes. Agora cada squad tem seu
próprio modelo de IA, configurado dinamicamente na tabela `agents` do PostgREST.

### Arquitetura

```
[operational.py] ──┐
[strategic.py]  ──┼──> llm_router.py ──> API (DeepSeek / OpenAI / Anthropic / OpenRouter)
                   │        ↑
              [agents table] (PostgREST)
```

- `llm_router.py`: modulo central que consulta `agents` no PostgREST, mapeia
  `model_provider` + `model_name`, e faz a chamada LLM com a API key correta
- `operational.py` (`llm_decide`): refatorado para usar o router, com fallback
  direto ao DeepSeek se o router falhar. Seleciona modelo baseado no squad
  que tem mais ordens pendentes.
- `strategic.py` (`llm_analyze`): refatorado para usar o router com
  `squad-marketing` como padrao (analise de negocios), com fallback.

### Providers suportados

| Provider    | Env Var                | URL                                    |
|-------------|------------------------|----------------------------------------|
| deepseek    | DEEPSEEK_API_KEY       | https://api.deepseek.com/v1/chat/completions |
| openai      | OPENAI_API_KEY         | https://api.openai.com/v1/chat/completions   |
| anthropic   | ANTHROPIC_API_KEY      | https://api.anthropic.com/v1/messages        |
| openrouter  | OPENROUTER_API_KEY     | https://openrouter.ai/api/v1/chat/completions |

### Como trocar o modelo de um squad

```bash
curl -s -X PATCH "http://10.0.2.7:3000/agents?name=eq.squad-dev" \
  -H "Content-Type: application/json" \
  -d '{"model_provider": "anthropic", "model_name": "claude-sonnet-4-20250514"}'
```

1. Adiciona a API key no `.env`
2. Faz PATCH no PostgREST no squad desejado
3. Proximo ciclo do cron ja usa o novo modelo

### Arquivos alterados

- `/root/hermes-heartbeat/llm_router.py` — NOVO (11.903 bytes, 320 linhas)
- `/root/hermes-heartbeat/operational.py` — atualizado `llm_decide` + nova `llm_decide_fallback`
- `/root/hermes-heartbeat/strategic.py` — atualizado `llm_analyze` + nova `llm_analyze_fallback`
- `/root/hermes-heartbeat/.env` — atualizado com placeholders para OpenAI/Anthropic/OpenRouter

### Configuracao atual dos squads

Todos rodando `deepseek/deepseek-chat` (unico provider com chave no momento).

### Board

Task #179 criada e movida para `done`.
